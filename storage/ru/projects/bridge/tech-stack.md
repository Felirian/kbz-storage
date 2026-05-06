# Используемые технологии Bridge

## Базовый стек

| Технология | Версия | Назначение |
|---|---|---|
| React | 18.2 | UI |
| TypeScript | 5.3 | Язык, strict |
| Vite | 5.1 | Сборка, dev-server |
| SCSS Modules | — | Стилизация, BEM |
| React Router | 6.22 | Роутинг |
| Zustand | 4.5 | Глобальный стейт (минимально, портал не stateful) |
| Axios | 1.6 | HTTP-клиент |
| Vitest + RTL | 1.4 / 14.2 | Юнит-тесты |
| Playwright | 1.42 | E2E |

## Специфика Bridge

### MDX

Документация написана в **MDX** (Markdown + React-компоненты). Это позволяет вставлять интерактивные сниппеты прямо в гайды:

```mdx
import { TryEndpoint } from '@/widgets/try-endpoint';

## Создание ключа

<TryEndpoint method="POST" path="/v1/keys" />
```

Сборщик MDX интегрирован в Vite через `@mdx-js/rollup`. Документация делится на:

- **conceptual** — гайды, туториалы, объяснения (пишет команда вручную);
- **generated** — справочник методов, генерируется из OpenAPI спецификации (см. ниже).

### OpenAPI и Swagger UI

Swagger UI (`swagger-ui-react`) встроен в портал и показывает интерактивный справочник методов. Источник правды — OpenAPI-спецификация (v3.0.3) из репозитория бэкенда.

Спецификация подгружается в CI Bridge:

```bash
# scripts/sync-openapi.sh
gh api -H "Accept: application/vnd.github.raw" \
  /repos/novatech/api-public/contents/openapi.yaml > public/openapi.yaml
```

Скрипт запускается еженощно по cron и при ручном триггере. Если контракт изменился — генерируется PR в Bridge.

### Monaco Editor

`@monaco-editor/react` — редактор для playground'а. Поддерживает:

- подсветку JSON и YAML;
- автодополнение по схеме (LSP-режим, конфигурируемый из OpenAPI);
- два режима: «request body» и «response viewer».

Monaco — тяжёлая зависимость (~ 2МБ gzipped в полной версии). Bridge подгружает её **лениво** — только на страницах, где нужен playground.

### OAuth 2.0 + PKCE

Авторизация партнёров — OAuth 2.0 Authorization Code Flow с PKCE. Реализация — собственная (без oidc-client), потому что нам важно полный контроль за хранилищем токенов и редиректами. Подробнее — в [API-взаимодействие](/projects/bridge/api-integration).

### Algolia DocSearch

Поиск по документации — через Algolia DocSearch. Crawler конфигурируется в репозитории Bridge, индексация проходит после каждого деплоя на staging.

## Инфраструктура

| Компонент | Решение |
|---|---|
| CI | GitHub Actions |
| Preview | Vercel (PR preview) |
| Production | AWS S3 + CloudFront |
| Логи | CloudWatch + Sentry |
| Метрики | Datadog (RUM) |
| Поиск по докам | Algolia DocSearch |
| Feature flags | LaunchDarkly (минимально) |

## Инструменты разработки

- **ESLint + Prettier** — общие конфиги студии. Локальные правила см. в [Код-стайл](/projects/bridge/code-style).
- **Husky + lint-staged** — pre-commit гонит ESLint, Prettier, типы.
- **Storybook 7.6** — документация компонентов (см. [UI-библиотека](/projects/bridge/ui-library)).
- **Playwright** — e2e-тесты основных сценариев пилотного использования.
- **Lighthouse CI** — проверка performance документации (целевая Lighthouse Score ≥ 90 на ключевых страницах).

## Обоснование ключевых выборов

**Vite, а не Next.js.** Bridge — преимущественно статический портал с интерактивными элементами. SSR не критичен (документация заранее статична, а индексирование решено через sitemap + статический HTML pre-render критичных страниц). Vite даёт лучший DX и меньше операционной сложности.

**MDX, а не классический CMS.** Команда контролирует документацию из репозитория, изменения проходят через PR и code-review. Это снижает вероятность ошибок и упрощает синхронизацию с релизами кода.

**Swagger UI, а не Redoc.** Команда оценивала оба. Swagger UI выиграл за счёт интерактивности (возможность пробовать запросы прямо в спеке). Redoc красивее, но менее функционален.

**Собственная реализация OAuth/PKCE.** Готовые библиотеки тянут лишние зависимости и реализуют не нужные нам сценарии (silent renew, многопровайдерные конфигурации). PKCE flow реализуется в ~150 строк, и Андрей принял решение писать своё с тщательным ревью.
