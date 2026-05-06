# Дизайн-система Bridge

## Состав

Дизайн-система Bridge — три артефакта:

1. **Figma-библиотека** `Bridge / Components`;
2. **Storybook** проекта (см. [UI-библиотека](/projects/bridge/ui-library));
3. **Код-пакет** `src/shared/ui/`.

Источник правды — Figma. Реализация в коде должна совпадать; расхождения — баг.

## Особенность Bridge: документация-центрик

Большая часть UI-компонентов проектируется с учётом того, что они появляются в документационных страницах MDX. Это диктует требования:

- **аккуратный вертикальный ритм** — компоненты не ломают типографику окружающего текста;
- **возможность вложения в Markdown** — таблицы, callout'ы, code samples должны работать как естественные элементы документа;
- **тёмная тема обязательна** — без неё компонент не уходит в `stable`.

## Токены

Двухуровневая система:

- **Primitives** — палитра, типографика, отступы (см. [Стайлгайд](/projects/bridge/styleguide)).
- **Semantic** — `--color-bg-canvas`, `--color-bg-code`, `--color-text-strong`, `--color-text-muted`, `--color-border-default`, `--color-link`.

Каждый семантический токен имеет вариант для `data-theme="dark"`. Сборка через Tokens Studio + `npm run tokens:build`.

## Базовые компоненты

| Компонент | Состояния | Статус |
|---|---|---|
| `Button` | default, hover, focus, active, disabled, loading | stable |
| `IconButton` | + tooltip-обвязка | stable |
| `LinkButton` | визуально как ссылка, ведёт себя как кнопка | stable |
| `Input` | default, focus, error, disabled | stable |
| `Textarea` | + autosize | stable |
| `Select` | default, open, focus, error, disabled | stable |
| `Checkbox`, `Radio`, `Switch` | стандартные | stable |
| `Modal`, `Drawer`, `Tooltip`, `Popover` | стандартные | stable |
| `Toast` | success, warning, danger, info | stable |
| `Tabs` | horizontal, segmented (для табов code samples) | stable |
| `Breadcrumbs` | стандартный | stable |

## Документационные компоненты

| Компонент | Назначение | Статус |
|---|---|---|
| `Callout` | info / warning / danger / success блоки в MDX | stable |
| `CodeBlock` | блок кода с подсветкой и copy-кнопкой | stable |
| `CodeTabs` | переключение примеров на разных языках (curl/JS/Python) | stable |
| `EndpointBadge` | бейдж HTTP-метода (`GET`, `POST`, ...) | stable |
| `ParameterTable` | таблица параметров запроса/ответа | stable |
| `TryEndpoint` | inline-виджет для пробного запроса в MDX | beta |
| `TocSidebar` | боковое оглавление документа | stable |
| `AnchorList` | список якорей справа от контента | stable |
| `VersionBadge` | бейдж версии API/метода | stable |
| `DeprecationBanner` | баннер deprecated-метода | stable |

## Доменные компоненты

### Sandbox / Playground

| Компонент | Назначение | Статус |
|---|---|---|
| `MethodPicker` | выбор endpoint'а | stable |
| `RequestBuilder` | конструктор запроса (path, headers, body) | stable |
| `JsonEditor` | редактор JSON на Monaco | stable |
| `ResponseViewer` | визуализация ответа | stable |
| `ShareSnapshot` | копирование ссылки на текущее состояние playground | beta |

### Keys / Quotas / Organization

| Компонент | Назначение | Статус |
|---|---|---|
| `KeyCard` | карточка ключа со статусом | stable |
| `KeyCreateDialog` | диалог создания (один раз показывается plain-key) | stable |
| `RotateConfirm` | подтверждение ротации | stable |
| `QuotaBar` | прогресс-бар потребления квоты | stable |
| `MemberRow` | строка члена организации | beta |

## Состояния компонентов

Каждый интерактивный компонент имеет полный набор состояний (default / hover / focus / active / disabled, плюс loading / error по применимости). Фокус-ring обязателен для соответствия WCAG 2.2 AA — Bridge целится в публичный SOC2-аудит, и accessibility часть подготовки.

Дополнительно для документационных компонентов:

- **dark variant** — обязателен;
- **в потоке текста** — компонент проверяется в окружении абзацев, чтобы не ломать ритм.

## Адаптивность

| Имя | Ширина |
|---|---|
| `xs` | < 640px |
| `sm` | ≥ 640px |
| `md` | ≥ 1024px |
| `lg` | ≥ 1280px |
| `xl` | ≥ 1440px |

Документация — полностью адаптивна (`xs`+). Sandbox и playground — `md`+ (на мобильном Monaco работает плохо).

## Версионирование

Дизайн-система Bridge публикуется во внутренний npm как `@novatech/bridge-ds`. Breaking changes требуют RFC и согласования с Андреем.

## Связанные документы

- [Стайлгайд](/projects/bridge/styleguide).
- [Регламент передачи макетов](/projects/bridge/handoff-regulation).
- [UI-библиотека](/projects/bridge/ui-library).
