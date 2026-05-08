---
title: Forge · Дизайн-система
description: Компоненты конструктора и рантайма, токены, состояния полей формы, drag-and-drop паттерны.
---

# Дизайн-система Forge

## Состав

Дизайн-система Forge — три артефакта:

1. **Figma-библиотека** `Forge / Components` (опубликованная);
2. **Storybook** проекта (см. [UI-библиотека](/projects/forge/ui-library));
3. **Код-пакет** `src/shared/ui/`.

Источник правды — Figma. Расхождения с реализацией трактуются как баги и заводятся в Jira.

## Особенность Forge: два контекста

Большая часть компонентов имеет два варианта:

- **Editor variant** — компонент в редакторе схемы. Содержит ручки, индикаторы валидности, режимы выделения.
- **Runtime variant** — компонент при заполнении формы конечным пользователем. Чище, без редакторских украшений.

Это не значит «два разных компонента»: общий код шарится через core-операции и общие низкоуровневые UI-блоки. Но визуальные состояния и UX могут отличаться.

## Токены

Двухуровневая система:

- **Primitives** — палитра, типографика, отступы, тени (см. [Стайлгайд](/projects/forge/styleguide)).
- **Semantic** — `--color-bg-canvas`, `--color-text-strong`, `--color-border-active`, `--shadow-card`, и т.п.

Экспорт из Figma в JSON через Tokens Studio, сборка скриптом `npm run tokens:build`. Изменение токенов проходит ревью у Виктории и Елены.

## Базовые компоненты

| Компонент | Состояния | Статус |
|---|---|---|
| `Button` | default, hover, focus, active, disabled, loading | stable |
| `IconButton` | + tooltip-обвязка | stable |
| `Input` | default, focus, error, disabled, with-prefix, with-suffix | stable |
| `Textarea` | + autosize | stable |
| `Select` | default, open, focus, error, disabled | stable |
| `Combobox` | + async-поиск | beta |
| `Checkbox` | unchecked, checked, indeterminate | stable |
| `Radio` | unchecked, checked, disabled | stable |
| `Switch` | off, on, disabled | stable |
| `DatePicker` | day, range, datetime | beta |
| `Modal`, `Drawer`, `Popover`, `Tooltip` | стандартные | stable |
| `Toast` | success, warning, danger, info | stable |
| `Tabs` | horizontal, vertical | stable |

## Доменные компоненты

### Конструктор

| Компонент | Назначение | Статус |
|---|---|---|
| `FieldTree` | дерево полей формы с DnD | stable |
| `FieldEditorShell` | обвязка редактируемого поля (выделение, бордеры, ручки) | stable |
| `FieldPalette` | палитра доступных типов полей | stable |
| `SchemaInspector` | панель свойств выбранного поля | stable |
| `SchemaPreviewToggle` | переключатель «редактор / превью» | stable |
| `RouteCanvas` | холст маршрутов согласования | beta |
| `RouteStepCard` | карточка шага в маршруте | beta |

### Рантайм

| Компонент | Назначение | Статус |
|---|---|---|
| `FormHeader` | заголовок и описание формы | stable |
| `FieldRuntimeText`, `FieldRuntimeSelect`, ... | конкретные поля для исполнителя | stable (по типам) |
| `ProgressIndicator` | прогресс по разделам | stable |
| `SubmitBar` | финальная панель с действиями | stable |
| `RouteTimeline` | визуализация состояния согласования для исполнителя | beta |

## Состояния компонентов

Каждый интерактивный компонент обязан иметь:

- default;
- hover (transition 150мс);
- focus (видимый ring, 2px, `--color-primary-500`, offset 2px);
- active;
- disabled (понижен контраст, `cursor: not-allowed`);
- loading (если применимо);
- error (если применимо).

В редакторе дополнительно:

- **selected** — фиолетовая обводка, иконка drag-handle;
- **invalid** — индикатор у поля, всплывающая подсказка с описанием ошибки.

## Адаптивность

| Имя | Ширина |
|---|---|
| `xs` | < 640px |
| `sm` | ≥ 640px |
| `md` | ≥ 1024px |
| `lg` | ≥ 1280px |
| `xl` | ≥ 1440px |

Редактор — desktop-only (`md`+). Рантайм — полноценно адаптивен (`xs`+), потому что исполнители могут заполнять формы с мобильного.

## Версионирование

Дизайн-система Forge версионируется отдельно от кода продукта и публикуется в `@novatech/forge-ds`. Breaking changes требуют RFC, плана миграции и согласования с Натальей (PO) — потому что меняют опыт уже опубликованных форм у клиентов.

## Связанные документы

- [Стайлгайд](/projects/forge/styleguide).
- [Регламент передачи макетов](/projects/forge/handoff-regulation).
- [UI-библиотека](/projects/forge/ui-library).
