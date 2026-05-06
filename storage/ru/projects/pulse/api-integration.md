# API-взаимодействие в Pulse

## Транспорт

Pulse общается с бэкендом по двум каналам:

- **REST поверх HTTPS** — основная часть запросов, через Axios;
- **WebSocket** — real-time обновления метрик и алертов.

Базовый URL и токен авторизации задаются переменными окружения (`VITE_API_URL`, `VITE_WS_URL`). Их получение и хранение описано в [Используемые технологии](/projects/pulse/tech-stack).

## Структура клиента

```
src/shared/api/
  http.ts          # axios instance + interceptors
  realtime.ts      # WebSocket-клиент
  errors.ts        # классы и нормализация ошибок
src/entities/<name>/api/
  <name>.api.ts    # CRUD-операции для домена
  <name>.queries.ts # хуки React Query
```

Каждая сущность инкапсулирует свой раздел API. Прямые вызовы axios из компонентов запрещены ESLint-правилом — только через хуки `use<Resource>Query` / `use<Resource>Mutation`.

## Конвенция запросов

REST-эндпоинты придерживаются ресурс-ориентированного стиля.

| Метод | Путь | Назначение |
|---|---|---|
| GET | `/api/v1/employees` | список с пагинацией и фильтрами |
| GET | `/api/v1/employees/:id` | один сотрудник |
| POST | `/api/v1/employees` | создание |
| PATCH | `/api/v1/employees/:id` | частичное обновление |
| DELETE | `/api/v1/employees/:id` | удаление |
| GET | `/api/v1/metrics/turnover` | агрегированная метрика |

Пагинация — cursor-based: запрос принимает `cursor` и `limit`, ответ возвращает `data`, `nextCursor`, `total`.

## Авторизация

OAuth 2.0 с access/refresh токенами. Access-токен короткоживущий (15 минут), хранится в `httpOnly` cookie, выставляемой бэкендом. Refresh-токен — тоже cookie, scope ограничен `/api/v1/auth/refresh`.

Axios-interceptor:

```ts
http.interceptors.response.use(
  (r) => r,
  async (error) => {
    if (error.response?.status === 401 && !error.config._retry) {
      error.config._retry = true;
      await http.post('/auth/refresh');
      return http.request(error.config);
    }
    throw normalizeError(error);
  }
);
```

Двойной 401 (рефреш не сработал) приводит к редиректу на страницу логина.

## Обработка ошибок

Ошибки нормализуются в единый класс `ApiError`:

```ts
class ApiError extends Error {
  code: string;          // 'VALIDATION', 'NOT_FOUND', 'INTERNAL', 'NETWORK', ...
  status: number;
  details?: Record<string, unknown>;
}
```

В UI принципы:

- **VALIDATION (422)** — показываем рядом с полями формы, не как глобальный тост;
- **NOT_FOUND (404)** — экран-заглушка ресурса;
- **INTERNAL (5xx)** — глобальный тост + автоматический ретрай для GET (через React Query);
- **NETWORK** — баннер «нет соединения», ретрай при восстановлении.

React Query политика ретраев:

- GET-запросы: 3 попытки с экспоненциальной задержкой (300мс, 900мс, 2700мс), только для статусов 5xx и сетевых ошибок;
- мутации не ретраятся автоматически.

## WebSocket

Соединение поднимается один раз при логине. Сообщения — JSON в формате:

```json
{
  "type": "metric.update",
  "channel": "turnover.dept-42",
  "payload": { "value": 0.12, "ts": "2025-04-12T10:23:00Z" }
}
```

Клиент подписывается на каналы через `subscribe(channel)`, отписывается через `unsubscribe`. Reconnect — с экспоненциальным backoff до 30 секунд.

> ⚠️ Внимание: при реконнекте клиент **не** запрашивает пропущенные события. Для критичных метрик сразу после `open` дёргаем REST-эндпоинт `/metrics/<name>` для синхронизации snapshot'а.

## Idempotency

Все мутации, которые могут быть повторены пользователем (экспорт отчёта, отправка уведомления), принимают заголовок `Idempotency-Key`. Клиент генерирует его как UUID v4 и кэширует на время операции. См. реализацию в `shared/api/idempotency.ts`.

## Логирование и мониторинг

Каждый запрос с длительностью > 1500мс логируется в Sentry breadcrumb. Ошибки 5xx и сетевые — в Sentry как issue. Алерты на превышение error-rate настроены в Datadog (см. [Паспорт проекта](/projects/pulse/passport) для контактов и SLA). Технический контакт — [Кирилл](/team#кирилл).
