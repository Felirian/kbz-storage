---
title: Bridge · API-взаимодействие
description: OAuth 2.0 PKCE, ротация API-ключей партнёров, rate limiting, BFF-проксирование и мониторинг безопасности.
---

# API-взаимодействие в Bridge

## Транспорт

Bridge общается с двумя типами API:

- **Bridge BFF** (backend-for-frontend) — внутренний API портала: keys, quotas, organization, auth, sandbox. Над ним команда Bridge имеет прямой контроль.
- **Public API** (тот самый, для которого Bridge — портал) — Bridge не вызывает его напрямую из браузера, кроме как из sandbox-домена.

Транспорт — REST поверх HTTPS. WebSocket и SSE не используются.

## Структура клиента

```
src/shared/api/
  http.ts            # axios instance + interceptors
  oauth.ts           # PKCE flow, обёртки над auth-ручками BFF
  generated/         # типы из OpenAPI (`openapi-typescript`)
  errors.ts          # классы ошибок
src/domains/<name>/api/
  <resource>.api.ts  # вызовы к BFF
  <resource>.queries.ts # хуки React Query (где используется)
```

Прямой axios из компонентов запрещён — только через хуки или явные api-функции.

## Аутентификация: OAuth 2.0 + PKCE

Bridge использует **Authorization Code Flow с PKCE**. Реализация — собственная (см. обоснование в [Используемые технологии](/projects/bridge/tech-stack)).

### Flow

```
1. Клиент: генерация code_verifier (random 64 байта)
                    ↓ SHA256
                  code_challenge
2. Клиент: redirect на /auth/authorize?
              client_id=...
              code_challenge=...
              code_challenge_method=S256
              redirect_uri=...
              state=...
3. Auth-провайдер → 302 на /callback?code=...&state=...
4. Клиент: POST /api/v1/auth/exchange { code, code_verifier }
   (отправляется на Bridge BFF)
5. Bridge BFF меняет code на access/refresh-токены
   и сохраняет их в httpOnly cookies, scope=Bridge
6. Bridge BFF возвращает только профиль пользователя
   (без токенов)
```

### Хранение

- `code_verifier` живёт в `sessionStorage` **только** между шагами 1 и 4. После обмена — удаляется немедленно.
- `state` тоже в `sessionStorage`, проверяется на колбэке для защиты от CSRF.
- `access_token` и `refresh_token` — `httpOnly` cookies, `Secure`, `SameSite=Lax`. Скоуп — `/api/`.

> ⚠️ Внимание: токены не доступны JS никогда. Это инвариант, нарушение которого блокирует мердж.

### Refresh

При получении 401 от BFF axios-interceptor вызывает `/api/v1/auth/refresh`. Сервер ротирует токены (refresh-token rotation), выставляет новые cookies, и interceptor повторяет исходный запрос. Если рефреш падает — редирект на `/login`.

## Конвенция запросов

REST-эндпоинты BFF.

| Метод | Путь | Назначение |
|---|---|---|
| GET | `/api/v1/keys` | список ключей организации |
| POST | `/api/v1/keys` | создание ключа (плейн-версия возвращается **только в этом ответе**) |
| POST | `/api/v1/keys/:id/rotate` | ротация |
| DELETE | `/api/v1/keys/:id` | отзыв |
| GET | `/api/v1/quotas` | текущие квоты |
| GET | `/api/v1/quotas/usage` | потребление |
| POST | `/api/v1/sandbox/request` | прокси-запрос в sandbox публичного API |
| GET | `/api/v1/organization` | данные организации |
| GET | `/api/v1/organization/members` | роли партнёра |

## Обработка ошибок

```ts
class ApiError extends Error {
  code: 'VALIDATION' | 'NOT_FOUND' | 'FORBIDDEN' | 'CONFLICT' | 'RATE_LIMITED' | 'INTERNAL' | 'NETWORK' | string;
  status: number;
  details?: unknown;
  requestId?: string; // для технической поддержки
}
```

Поведение в UI:

- **VALIDATION** — рядом с полем формы (напр., при создании ключа);
- **FORBIDDEN** — заглушка с пояснением и контактом администратора партнёра;
- **CONFLICT** — стандартный диалог разрешения конфликта (например, ключ уже отозван);
- **RATE_LIMITED** — баннер с текущей квотой и временем сброса;
- **INTERNAL** — глобальный тост, без раскрытия деталей; в Sentry уходит полная информация;
- **NETWORK** — баннер «нет соединения», ретрай при восстановлении.

## Sandbox: прокси через BFF

Запросы из sandbox-домена не идут напрямую в публичный API. Они идут через `/api/v1/sandbox/request`, BFF проксирует с подмешиванием специального sandbox-ключа. Это:

- защищает реальные ключи партнёра от утечки в playground-сессии;
- даёт единый CORS-домен и упрощает CSP;
- позволяет логировать использование sandbox для аналитики.

## Idempotency

Мутации, которые могут быть повторены (создание ключа, отправка запроса в sandbox), принимают `Idempotency-Key`. Клиент генерирует UUID v4 и кэширует на время операции.

## Логирование и мониторинг

- Все ошибки 5xx — в Sentry с `requestId`. Ответственный — [Кирилл](/team#кирилл).
- Latency публичных эндпоинтов BFF — в Datadog APM, алерты настроены на p95. Инфраструктура — [Марина](/team#марина).
- Запросы к auth-эндпоинтам не логируются с телом — это правило ([Андрей](/team#андрей)).

## Связанные документы

- [Архитектура приложения](/projects/bridge/architecture) — где живёт API-слой.
- [Используемые технологии](/projects/bridge/tech-stack) — выбор PKCE и openapi-typescript.
- [Процессы](/projects/bridge/processes) — security-ревью изменений в auth.
