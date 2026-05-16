---
title: Bridge · Архитектура приложения
description: "BFF-паттерн, MDX-конвейер документации, JsonEditor на Monaco, модули Sandbox и Playground, изоляция auth-слоя и RFC-процесс."
---

# Архитектура приложения Bridge

## Подход

Bridge организован по **доменно-секционному принципу**: каждая секция портала — самостоятельный домен с собственными страницами, виджетами и контрактами с бэкендом. Это компромисс между FSD (как в [Pulse](/projects/pulse/architecture)) и плоским роутер-ориентированным подходом.

```
src/
  app/                # инициализация, провайдеры, роутер
  domains/
    docs/             # документация (MDX + Swagger UI)
    sandbox/          # песочница и playground
    keys/             # управление API-ключами
    quotas/           # квоты и лимиты
    organization/     # настройки организации, роли партнёра
    auth/             # OAuth + PKCE flow, profile
  shared/
    ui/               # UI-библиотека (см. ui-library)
    api/              # axios instance, OAuth helpers
    lib/              # утилиты, хуки
    config/           # окружение
content/
  guides/             # MDX conceptual-документация
  api-reference/      # генерируемый раздел из OpenAPI
public/
  openapi.yaml        # синхронизированная спецификация
```

## Домены

### docs

Главный по объёму домен. Делится на:

- **guides** — MDX-страницы, написанные командой;
- **api-reference** — Swagger UI поверх `public/openapi.yaml`;
- **changelog** — история изменений API (тоже MDX).

Маршрутизация документации статична — сборка генерирует sitemap и pre-rendered HTML критичных страниц для SEO.

### sandbox

Реализует **изолированное окружение** для проб запросов. Пользовательские запросы идут не на реальный API, а на sandbox-эндпоинт бэкенда, который возвращает реалистичные стабы.

Состояние playground'а (выбранный метод, тело запроса, история) хранится в Zustand-сторе, **не персистится** на сервере, но восстанавливается из URL для шаринга:

```
/sandbox?m=POST&p=/v1/keys&b=eyJ...
```

### JsonEditor — компонент редактирования JSON

Ключевой интерактивный элемент sandbox-домена. Реализован как тонкая обёртка над **Monaco Editor** (`@monaco-editor/react`) с набором настроек, специфичных для Bridge.

#### Расположение

```
src/domains/sandbox/components/JsonEditor/
  JsonEditor.tsx          # корневой компонент
  JsonEditor.module.scss
  useJsonEditor.ts        # хук: валидация, схема, тема
  json-editor.types.ts
  index.ts
```

#### Конфигурация Monaco

```ts
const EDITOR_OPTIONS: editor.IStandaloneEditorConstructionOptions = {
  language: 'json',
  theme: colorScheme === 'dark' ? 'bridge-dark' : 'bridge-light',
  fontSize: 13,
  lineNumbers: 'on',
  minimap: { enabled: false },
  scrollBeyondLastLine: false,
  wordWrap: 'on',
  formatOnPaste: true,
  tabSize: 2,
};
```

Темы `bridge-dark` и `bridge-light` зарегистрированы один раз при инициализации Monaco (`monaco.editor.defineTheme`) и совпадают с палитрой из [Стайлгайда](/projects/bridge/styleguide).

#### JSON Schema валидация

Для каждого выбранного эндпоинта из OpenAPI-спецификации извлекается JSON Schema тела запроса и передаётся в Monaco через `monaco.languages.json.jsonDefaults.setDiagnosticsOptions`:

```ts
monaco.languages.json.jsonDefaults.setDiagnosticsOptions({
  validate: true,
  schemas: [{
    uri: `bridge://schema/${operationId}`,
    fileMatch: ['*'],
    schema: requestBodySchema,   // JSON Schema из openapi.yaml
  }],
  enableSchemaRequest: false,
});
```

Это даёт inline-подсветку ошибок, автодополнение и hover-документацию прямо в редакторе — без отдельного валидатора.

#### Состояние и интеграция с sandbox

Значение редактора не хранится в React state — это источник задержек при большом JSON. Вместо этого:

- Monaco держит значение во внутреннем буфере;
- при отправке запроса хук `useJsonEditor` вызывает `editor.getValue()` и валидирует синхронно;
- результат пишется в Zustand-стор sandbox'а только при изменении (debounce 400 мс).

```ts
// useJsonEditor.ts (упрощённо)
const handleChange = useDebouncedCallback((value: string) => {
  sandboxStore.setRequestBody(value);
}, 400);
```

#### Форматирование и горячие клавиши

| Действие | Сочетание |
|---|---|
| Форматировать JSON | `Shift+Alt+F` |
| Свернуть все блоки | `Ctrl+K Ctrl+0` |
| Развернуть все блоки | `Ctrl+K Ctrl+J` |
| Перейти к ошибке | `F8` |

Форматирование также доступно кнопкой «Format» над редактором — вызывает `editor.getAction('editor.action.formatDocument').run()`.

#### Lazy-загрузка

Monaco весит ~2 МБ (gzipped ~500 КБ), поэтому компонент подгружается только при открытии sandbox:

```ts
// domains/sandbox/pages/Sandbox.tsx
const JsonEditor = lazy(() => import('../components/JsonEditor'));
```

До загрузки показывается `<Skeleton height={280} />`. Это ограничение зафиксировано в метриках производительности — Monaco не должен попадать в основной бандл (см. раздел «Производительность» выше).

#### Ограничения

> ⚠️ Внимание: Monaco не поддерживает SSR. Компонент обёрнут в `dynamic(() => import(...), { ssr: false })` (Next.js). Любая попытка рендерить его на сервере вызовет ошибку `ReferenceError: document is not defined`.

### keys, quotas, organization

CRUD-домены: классические список/карточка с операциями. State минимален — только selected items и фильтры. Большая часть состояния живёт в Axios + React Query (используется выборочно, только в этих доменах).

### auth

Авторизация через OAuth 2.0 + PKCE. Подробно — в [API-взаимодействие](/projects/bridge/api-integration). Поддерживает редиректы провайдера и хранение токенов в `httpOnly` cookies (выставляются бэкендом портала, который выступает в роли BFF).

## Документация: pipeline

```
Backend repo (openapi.yaml)
      ↓ ночной cron в Bridge CI
public/openapi.yaml (PR)
      ↓ CI: проверки (валидация спеки, breaking changes)
mergeable PR → develop
      ↓ деплой на staging
Algolia crawler → индекс
```

При несовпадении — Conceptual-гайды vs Generated reference — CI Bridge выдаёт предупреждение (но не блокирует мердж). Регулярная сверка — раз в спринт (см. [Процессы](/projects/bridge/processes)).

## Управление состоянием

| Тип | Решение |
|---|---|
| Серверное (keys, quotas) | React Query (только в этих доменах) |
| Глобальный UI (тема, селект организации) | Zustand |
| Sandbox (текущая сессия) | Zustand, локальный для домена |
| Локальное | `useState` |

Принципиально: Bridge не глобализует состояние без необходимости. Большая часть страниц самодостаточна.

## Производительность

Целевые показатели:

- LCP документационных страниц — ≤ 2 секунд (RUM, p75);
- Lighthouse Performance Score ≥ 90 на ключевых страницах;
- бандл главной страницы — ≤ 200 КБ gzipped;
- Monaco Editor подгружается только на страницах playground'а (lazy chunk).

Sentry и Datadog RUM включены с pre-launch — собираем baseline для последующего сравнения.

## Безопасность

Bridge — security-чувствительный продукт. Архитектурные решения:

- **BFF (backend-for-frontend)** для авторизации: токены не лежат в localStorage / sessionStorage, только в `httpOnly` cookies, выставляемых сервером Bridge;
- **CSP** заголовки выставляются в CloudFront, запрещают inline-скрипты и неизвестные домены;
- **Subresource Integrity** для всех внешних скриптов (Algolia, Datadog RUM);
- **Никаких ключей партнёров** в localStorage. Полные ключи показываются один раз — при создании, после чего хранятся только их хеши.

## RFC-процесс

RFC обязателен для (см. [Процессы](/projects/bridge/processes)):

- любого изменения OAuth/PKCE flow;
- расширения политики хранения ключей;
- введения новой внешней зависимости в sandbox или auth-домены;
- любого изменения, которое расширяет attack surface.

Шаблон RFC:

1. Контекст и проблема.
2. Альтернативы.
3. Предложение и влияние.
4. Threat model: какие атаки релевантны и как митигируются.
5. Откат.

RFC апрувит [Андрей](/team#андрей). Для security-релевантных RFC дополнительно консультируется security-лид студии.

> ⚠️ Внимание: любое изменение в обработке cookies или CSP — обязательно с pentest-проходом до релиза в production.
