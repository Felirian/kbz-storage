---
title: Pulse · Используемые технологии
description: Технологический стек проекта: React 18, TypeScript, Recharts, React Query, WebSocket, CI/CD и инструменты разработки.
---

# Используемые технологии Pulse

## Базовый фронтенд-стек

| Технология | Версия | Назначение |
|---|---|---|
| React | 18.2 | UI-фреймворк |
| TypeScript | 5.3 | Язык, strict mode |
| Vite | 5.1 | Сборка и dev-server |
| SCSS Modules | — | Стилизация, BEM-нейминг |
| React Router | 6.22 | Клиентский роутинг |
| Zustand | 4.5 | Глобальный стейт |
| React Query | 4.36 | Серверный кэш и синхронизация |
| Axios | 1.6 | HTTP-клиент с interceptors |
| Vitest + RTL | 1.4 / 14.2 | Юнит- и интеграционные тесты |

Полный список зависимостей зафиксирован в `package.json`. Обновления мажорных версий проходят через RFC (см. [Процессы](/projects/pulse/processes)).

## Специфика Pulse

### Recharts

Используется для построения графиков (bar, line, area, treemap). Выбран за компактный API и интеграцию с React. Известное ограничение — деградация производительности на сериях больше 5000 точек, для таких случаев планируется переход на собственную обёртку поверх D3 (см. [Беклог и roadmap](/projects/pulse/backlog-roadmap)).

### React Query

Серверное состояние полностью управляется React Query. Конвенции:

- ключи запросов — массивы, первый элемент — домен (`['employees', filters]`, `['turnover', period]`);
- `staleTime` по умолчанию — 30 секунд, для агрегационных дашбордов — 5 минут;
- мутации инвалидируют ключи через `queryClient.invalidateQueries(['домен'])`.

### WebSocket

Real-time обновления критичных метрик идут через WebSocket-соединение с бэкендом. Реализация — в `src/shared/lib/realtime/`. Каждый дашборд подписывается на канал и обновляет данные точечно через `setQueryData`. Подробнее об интеграции — в [API-взаимодействие](/projects/pulse/api-integration).

### Recharts + WebSocket: ограничения

> ⚠️ Внимание: при частоте обновлений выше 10 событий в секунду Recharts заметно ремонтирует SVG. Для таких потоков делаем дебаунс на уровне Zustand-стора (300мс), а не пропускаем апдейты в очередь React Query напрямую.

## Инфраструктура

| Компонент | Решение |
|---|---|
| CI | GitHub Actions |
| Preview-деплои | Vercel |
| Production | AWS (ECS Fargate за CloudFront) |
| Логи | CloudWatch + Sentry для фронтенда |
| Метрики | Datadog (RUM), синтетика — Checkly |
| Feature flags | LaunchDarkly |

## Инструменты разработки

- **ESLint** — конфиг проекта расширяет `@novatech/eslint-config-react`. Локальные правила см. в [Код-стайл](/projects/pulse/code-style).
- **Prettier** — общая конфигурация студии, без локальных переопределений.
- **Husky + lint-staged** — пре-коммит проверяет ESLint и Prettier на изменённых файлах. Тесты в pre-commit не гоняем — только в CI.
- **Storybook 7.6** — для документации UI-компонентов. Реестр — в [UI-библиотека](/projects/pulse/ui-library).
- **Playwright** — e2e-тесты, гоняются в CI на каждом PR в `develop`. Тестовый набор пока минимальный, расширение запланировано на Q3 (см. [Беклог и roadmap](/projects/pulse/backlog-roadmap)).

## Обоснование ключевых выборов

**Zustand против Redux Toolkit.** Pulse — преимущественно read-heavy приложение, основная сложность не в редьюсерах, а в синхронизации с сервером. Zustand даёт минимальный boilerplate, серверное состояние закрывает React Query.

**React Query 4, а не TanStack Query 5.** Миграция запланирована, но заблокирована несовместимостью с внутренней обёрткой графиков (см. техдолг в [Беклог и roadmap](/projects/pulse/backlog-roadmap)).

**Recharts против Visx/Nivo.** На старте проекта команда оценила три библиотеки; Recharts выиграл по скорости интеграции. Сейчас часть критичных графиков мигрирует на D3.
