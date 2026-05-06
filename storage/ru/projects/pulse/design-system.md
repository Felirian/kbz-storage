# Дизайн-система Pulse

## Состав

Дизайн-система Pulse — это связка трёх артефактов:

1. **Figma-библиотека** `Pulse / Components` (опубликованная, подключается ко всем продуктовым файлам);
2. **Storybook** проекта — живая документация и playground (см. [UI-библиотека](/projects/pulse/ui-library));
3. **Код-пакет** `src/shared/ui/` — реализация компонентов на React.

Источником правды является Figma. Если в Storybook расхождение с Figma — это баг реализации.

## Токены

Токены делятся на два уровня:

- **Primitives** — палитра, типографика, отступы, радиусы (см. [Стайлгайд](/projects/pulse/styleguide)).
- **Semantic** — назначение токенов в контексте: `--color-bg-card`, `--color-text-muted`, `--color-border-default`. Семантические токены ссылаются на primitives.

Токены экспортируются из Figma в JSON через плагин `Tokens Studio` и собираются в CSS-переменные скриптом `npm run tokens:build`. Изменения токенов проходят через ревью Олега и Дизайн-Лида.

## Компоненты

### Базовые

| Компонент | Состояния | Статус |
|---|---|---|
| `Button` | default, hover, focus, active, disabled, loading | stable |
| `Input` | default, focus, error, disabled, with-icon | stable |
| `Select` | default, open, focus, error, disabled | stable |
| `Checkbox` | unchecked, checked, indeterminate, disabled | stable |
| `Radio` | unchecked, checked, disabled | stable |
| `Switch` | off, on, disabled | stable |
| `Tooltip` | top, right, bottom, left | stable |
| `Modal` | small, medium, large, full | stable |
| `Toast` | success, warning, danger, info | stable |
| `Tabs` | horizontal, segmented | stable |
| `DatePicker` | day, range | beta |

### Доменные

| Компонент | Назначение | Статус |
|---|---|---|
| `MetricCard` | KPI-карточка с трендом | stable |
| `EmployeeRow` | строка таблицы сотрудников с аватаром и тегами | stable |
| `TrendBadge` | бейдж положительной/отрицательной динамики | stable |
| `DepartmentTreeSelect` | иерархический селект отделов | beta |
| `RealtimeIndicator` | статус соединения WebSocket | stable |
| `ExportMenu` | выпадашка форматов экспорта | beta |

### Графики

Палитра графиков отдельная (8 базовых цветов + светлые/тёмные тона). Конкретные правила применения цветов на графиках:

- ряды одного смысла — одного оттенка, контрастируют насыщенностью;
- негативная динамика — `--color-danger-500`, позитивная — `--color-success-500`;
- акценты на одном ряду — `--color-accent-500`.

Графики оборачиваются в компонент `Chart`, который добавляет общие отступы, легенду и поведение пустого состояния.

## Состояния компонентов

Все интерактивные компоненты обязательно имеют:

| Состояние | Требования |
|---|---|
| default | базовый вид |
| hover | изменение фона/обводки, transition 150мс |
| focus | видимый ring (2px, `--color-primary-500`, offset 2px) |
| active | визуальная обратная связь нажатия |
| disabled | пониженная контрастность, `cursor: not-allowed` |
| loading (если применимо) | спиннер или skeleton |
| error (если применимо) | красная обводка, текст ошибки |

Фокус-ring — обязателен для соответствия WCAG 2.2 AA.

## Адаптивность

Контрольные точки:

| Имя | Ширина | Применение |
|---|---|---|
| `xs` | < 640px | мобильный (сейчас не приоритетен) |
| `sm` | ≥ 640px | планшет вертикально |
| `md` | ≥ 1024px | планшет горизонтально, минимальный desktop |
| `lg` | ≥ 1280px | основной desktop |
| `xl` | ≥ 1440px | широкий desktop |

Pulse фокусируется на `md`+. Мобильная вёрстка реализована для отчётных экранов и логина, остальные — desktop-only с заглушкой при меньшей ширине.

## Версионирование

Дизайн-система версионируется отдельно от продуктовой кодовой базы. Версия публикуется в `@novatech/pulse-ds` (внутренний npm-registry). Breaking changes требуют RFC и плана миграции.

## Связанные документы

- [Стайлгайд](/projects/pulse/styleguide) — токены и базовые правила.
- [UI-библиотека](/projects/pulse/ui-library) — реестр компонентов и ссылки на Storybook.
- [Регламент передачи макетов](/projects/pulse/handoff-regulation) — что должно быть в макете перед хендоффом.
