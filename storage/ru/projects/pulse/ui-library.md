---
title: Pulse · UI-библиотека
description: Реестр всех компонентов проекта со статусами stable/beta/deprecated и ссылками на Storybook.
---

# UI-библиотека Pulse

## Назначение

UI-библиотека — реестр компонентов проекта с указанием статуса и ссылками на Storybook. Это операционный документ: команда сверяется с ним при оценке задач (есть ли уже такой компонент?) и при добавлении новых элементов в дизайн-систему.

## Адреса

- Storybook (актуальная сборка с `develop`): `https://pulse-storybook.novatech.internal`
- Storybook (production-зафиксированная): `https://pulse-storybook.novatech.internal/stable`
- Исходники: `src/shared/ui/` (см. [Архитектура приложения](/projects/pulse/architecture)).

Доступ — через корпоративный SSO. Пароля не существует.

## Статусы

| Статус | Что значит |
|---|---|
| **stable** | Используется в production, API стабильно, breaking changes — через major-версию пакета |
| **beta** | Доступен в production, но API может меняться по результатам наблюдений; ломаются только в `feature`-ветках |
| **deprecated** | Запрещён к использованию в новых местах, есть план миграции |
| **experimental** | Только в Storybook, в production не подключён |

## Реестр компонентов

### Базовые формы

| Компонент | Storybook | Статус | Заметки |
|---|---|---|---|
| `Button` | [story](https://pulse-storybook.novatech.internal/?path=/story/button) | stable | Варианты: `primary`, `secondary`, `ghost`, `danger` |
| `IconButton` | [story](https://pulse-storybook.novatech.internal/?path=/story/icon-button) | stable | Только для иконочных действий |
| `Input` | [story](https://pulse-storybook.novatech.internal/?path=/story/input) | stable | С поддержкой prefix/suffix |
| `Textarea` | [story](https://pulse-storybook.novatech.internal/?path=/story/textarea) | stable | autosize по контенту |
| `Select` | [story](https://pulse-storybook.novatech.internal/?path=/story/select) | stable | single, multi |
| `Combobox` | [story](https://pulse-storybook.novatech.internal/?path=/story/combobox) | beta | Async-поиск, виртуализация |
| `Checkbox` | [story](https://pulse-storybook.novatech.internal/?path=/story/checkbox) | stable | |
| `Radio` | [story](https://pulse-storybook.novatech.internal/?path=/story/radio) | stable | |
| `Switch` | [story](https://pulse-storybook.novatech.internal/?path=/story/switch) | stable | |
| `DatePicker` | [story](https://pulse-storybook.novatech.internal/?path=/story/date-picker) | beta | day, range, без time |

### Навигация и обратная связь

| Компонент | Storybook | Статус |
|---|---|---|
| `Tabs` | [story](https://pulse-storybook.novatech.internal/?path=/story/tabs) | stable |
| `Breadcrumbs` | [story](https://pulse-storybook.novatech.internal/?path=/story/breadcrumbs) | stable |
| `Pagination` | [story](https://pulse-storybook.novatech.internal/?path=/story/pagination) | deprecated (мигрируем на cursor-pagination, см. ниже) |
| `Toast` | [story](https://pulse-storybook.novatech.internal/?path=/story/toast) | stable |
| `Modal` | [story](https://pulse-storybook.novatech.internal/?path=/story/modal) | stable |
| `Drawer` | [story](https://pulse-storybook.novatech.internal/?path=/story/drawer) | stable |
| `Tooltip` | [story](https://pulse-storybook.novatech.internal/?path=/story/tooltip) | stable |
| `Popover` | [story](https://pulse-storybook.novatech.internal/?path=/story/popover) | stable |

### Данные и отображение

| Компонент | Storybook | Статус |
|---|---|---|
| `Table` | [story](https://pulse-storybook.novatech.internal/?path=/story/table) | stable |
| `VirtualTable` | [story](https://pulse-storybook.novatech.internal/?path=/story/virtual-table) | beta |
| `EmptyState` | [story](https://pulse-storybook.novatech.internal/?path=/story/empty-state) | stable |
| `Skeleton` | [story](https://pulse-storybook.novatech.internal/?path=/story/skeleton) | stable |
| `Avatar` | [story](https://pulse-storybook.novatech.internal/?path=/story/avatar) | stable |
| `Badge` | [story](https://pulse-storybook.novatech.internal/?path=/story/badge) | stable |
| `TrendBadge` | [story](https://pulse-storybook.novatech.internal/?path=/story/trend-badge) | stable |

### Доменные компоненты Pulse

| Компонент | Storybook | Статус |
|---|---|---|
| `MetricCard` | [story](https://pulse-storybook.novatech.internal/?path=/story/domain-metric-card) | stable |
| `EmployeeRow` | [story](https://pulse-storybook.novatech.internal/?path=/story/domain-employee-row) | stable |
| `DepartmentTreeSelect` | [story](https://pulse-storybook.novatech.internal/?path=/story/domain-department-tree-select) | beta |
| `RealtimeIndicator` | [story](https://pulse-storybook.novatech.internal/?path=/story/domain-realtime-indicator) | stable |
| `ExportMenu` | [story](https://pulse-storybook.novatech.internal/?path=/story/domain-export-menu) | beta |

### Графики

| Компонент | Storybook | Статус |
|---|---|---|
| `Chart.Line` | [story](https://pulse-storybook.novatech.internal/?path=/story/charts-line) | stable |
| `Chart.Bar` | [story](https://pulse-storybook.novatech.internal/?path=/story/charts-bar) | stable |
| `Chart.Area` | [story](https://pulse-storybook.novatech.internal/?path=/story/charts-area) | stable |
| `Chart.Heatmap` | [story](https://pulse-storybook.novatech.internal/?path=/story/charts-heatmap) | beta |
| `Chart.D3Scatter` | [story](https://pulse-storybook.novatech.internal/?path=/story/charts-d3-scatter) | experimental |

## Добавление нового компонента

1. Олег публикует компонент в Figma-библиотеке.
2. Создаётся issue в Jira `PULSE-UI`.
3. Разработчик имплементирует в `src/shared/ui/<name>/` с обязательной story.
4. PR ревьюят [Кирилл](/team#кирилл) (код) и [Олег](/team#олег) (визуальное соответствие).
5. После мерджа компонент попадает в реестр со статусом `beta`. Перевод в `stable` — по результатам месяца использования без правок API.

Подробнее о критериях передачи — в [Регламент передачи макетов](/projects/pulse/handoff-regulation).

## Deprecation

`Pagination` (offset-based) помечен deprecated. Все новые экраны используют курсорную пагинацию (см. [API-взаимодействие](/projects/pulse/api-integration)). Старый компонент будет удалён в версии `2.0.0` — сроки в [Беклог и roadmap](/projects/pulse/backlog-roadmap).
