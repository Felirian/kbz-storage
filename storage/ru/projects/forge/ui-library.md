---
title: Forge · UI-библиотека
description: Реестр компонентов конструктора и рантайма со статусами и ссылками на Storybook.
---

# UI-библиотека Forge

## Назначение

Реестр компонентов проекта со ссылками на Storybook и статусом готовности. Используется командой при оценке задач (есть ли уже такой компонент?) и при добавлении новых блоков.

## Адреса

- Storybook (актуальная сборка с `main`): `https://forge-storybook.novatech.internal`
- Исходники: `src/shared/ui/` и `src/modules/<module>/ui/` (см. [Архитектура приложения](/projects/forge/architecture)).
- Доступ — корпоративный SSO.

## Статусы

| Статус | Описание |
|---|---|
| stable | Используется в production, API стабильно |
| beta | В production, API может измениться |
| experimental | Только в Storybook, не подключён в продукте |
| deprecated | Не использовать в новом коде, есть план миграции |

## Базовые компоненты (`shared/ui`)

### Формы

| Компонент | Storybook | Статус |
|---|---|---|
| `Button` | [story](https://forge-storybook.novatech.internal/?path=/story/button) | stable |
| `IconButton` | [story](https://forge-storybook.novatech.internal/?path=/story/icon-button) | stable |
| `Input` | [story](https://forge-storybook.novatech.internal/?path=/story/input) | stable |
| `Textarea` | [story](https://forge-storybook.novatech.internal/?path=/story/textarea) | stable |
| `Select` | [story](https://forge-storybook.novatech.internal/?path=/story/select) | stable |
| `Combobox` | [story](https://forge-storybook.novatech.internal/?path=/story/combobox) | beta |
| `Checkbox` | [story](https://forge-storybook.novatech.internal/?path=/story/checkbox) | stable |
| `Radio` | [story](https://forge-storybook.novatech.internal/?path=/story/radio) | stable |
| `Switch` | [story](https://forge-storybook.novatech.internal/?path=/story/switch) | stable |
| `DatePicker` | [story](https://forge-storybook.novatech.internal/?path=/story/date-picker) | beta |
| `FileUpload` | [story](https://forge-storybook.novatech.internal/?path=/story/file-upload) | beta |

### Навигация и обратная связь

| Компонент | Storybook | Статус |
|---|---|---|
| `Tabs` | [story](https://forge-storybook.novatech.internal/?path=/story/tabs) | stable |
| `Breadcrumbs` | [story](https://forge-storybook.novatech.internal/?path=/story/breadcrumbs) | stable |
| `Toast` | [story](https://forge-storybook.novatech.internal/?path=/story/toast) | stable |
| `Modal` | [story](https://forge-storybook.novatech.internal/?path=/story/modal) | stable |
| `Drawer` | [story](https://forge-storybook.novatech.internal/?path=/story/drawer) | stable |
| `Tooltip` | [story](https://forge-storybook.novatech.internal/?path=/story/tooltip) | stable |
| `Popover` | [story](https://forge-storybook.novatech.internal/?path=/story/popover) | stable |
| `Stepper` | [story](https://forge-storybook.novatech.internal/?path=/story/stepper) | stable |

### Данные

| Компонент | Storybook | Статус |
|---|---|---|
| `Table` | [story](https://forge-storybook.novatech.internal/?path=/story/table) | stable |
| `EmptyState` | [story](https://forge-storybook.novatech.internal/?path=/story/empty-state) | stable |
| `Skeleton` | [story](https://forge-storybook.novatech.internal/?path=/story/skeleton) | stable |
| `Avatar` | [story](https://forge-storybook.novatech.internal/?path=/story/avatar) | stable |
| `Badge` | [story](https://forge-storybook.novatech.internal/?path=/story/badge) | stable |
| `Tag` | [story](https://forge-storybook.novatech.internal/?path=/story/tag) | stable |

## Доменные компоненты

### Конструктор схемы (`modules/editor/ui`)

| Компонент | Storybook | Статус |
|---|---|---|
| `FieldTree` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-field-tree) | stable |
| `FieldEditorShell` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-field-shell) | stable |
| `FieldPalette` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-field-palette) | stable |
| `SchemaInspector` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-schema-inspector) | stable |
| `SchemaPreviewToggle` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-preview-toggle) | stable |
| `UndoRedoPanel` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-undo-redo) | stable |
| `ConflictDialog` | [story](https://forge-storybook.novatech.internal/?path=/story/editor-conflict-dialog) | beta |

### Поля рантайма (`modules/runtime/ui`)

| Компонент | Storybook | Статус |
|---|---|---|
| `FieldRuntimeText` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-text) | stable |
| `FieldRuntimeNumber` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-number) | stable |
| `FieldRuntimeSelect` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-select) | stable |
| `FieldRuntimeDate` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-date) | stable |
| `FieldRuntimeFile` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-file) | beta |
| `FieldRuntimeTable` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-table) | beta |
| `FieldRuntimeRating` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-rating) | experimental |
| `FieldRuntimeSignature` | [story](https://forge-storybook.novatech.internal/?path=/story/runtime-signature) | experimental |

### Маршруты согласования (`modules/routes/ui`)

| Компонент | Storybook | Статус |
|---|---|---|
| `RouteCanvas` | [story](https://forge-storybook.novatech.internal/?path=/story/routes-canvas) | beta |
| `RouteStepCard` | [story](https://forge-storybook.novatech.internal/?path=/story/routes-step-card) | beta |
| `RouteTimeline` | [story](https://forge-storybook.novatech.internal/?path=/story/routes-timeline) | beta |
| `RouteForkNode` | [story](https://forge-storybook.novatech.internal/?path=/story/routes-fork-node) | experimental |

## Добавление нового компонента

1. Виктория публикует компонент в Figma-библиотеке `Forge / Components`.
2. Создаётся issue в Jira (`FORGE-UI-...`).
3. Реализация — в соответствующем модуле (`shared/ui` для общих, `modules/<x>/ui` для доменных).
4. Story обязательна; для доменных — несколько вариантов состояний.
5. PR ревьюят [Марина](/team#марина) (код) и [Виктория](/team#виктория) (визуальное соответствие).
6. После мерджа компонент попадает в реестр со статусом `beta`. Перевод в `stable` — после месяца стабильного использования без правок API.

Подробнее о критериях передачи макета — в [Регламент передачи макетов](/projects/forge/handoff-regulation).

## Deprecation

Сейчас deprecated-компонентов нет, но запланирована миграция:

- `FieldRuntimeTable` — текущая внутренняя реализация будет заменена на TanStack Table (см. техдолг в [Беклог и roadmap](/projects/forge/backlog-roadmap)).
