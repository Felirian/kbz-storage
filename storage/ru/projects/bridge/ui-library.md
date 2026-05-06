# UI-библиотека Bridge

## Назначение

Реестр компонентов проекта со ссылками на Storybook и статусом готовности. Используется при оценке задач (есть ли уже такой компонент?) и при добавлении новых.

## Адреса

- Storybook (актуальная сборка с `develop`): `https://bridge-storybook.novatech.internal`
- Storybook (production-зафиксированная): `https://bridge-storybook.novatech.internal/stable` (заработает после релиза)
- Исходники: `src/shared/ui/` и `src/domains/<x>/ui/` (см. [Архитектура приложения](/projects/bridge/architecture)).

Доступ — корпоративный SSO.

## Статусы

| Статус | Описание |
|---|---|
| stable | API стабильно, есть полные dark-варианты, accessibility проверена |
| beta | В использовании, но API может измениться |
| experimental | Только в Storybook, не подключён в продукте |
| deprecated | Не использовать в новом коде |

## Базовые компоненты (`shared/ui`)

| Компонент | Storybook | Статус |
|---|---|---|
| `Button` | [story](https://bridge-storybook.novatech.internal/?path=/story/button) | stable |
| `IconButton` | [story](https://bridge-storybook.novatech.internal/?path=/story/icon-button) | stable |
| `LinkButton` | [story](https://bridge-storybook.novatech.internal/?path=/story/link-button) | stable |
| `Input` | [story](https://bridge-storybook.novatech.internal/?path=/story/input) | stable |
| `Textarea` | [story](https://bridge-storybook.novatech.internal/?path=/story/textarea) | stable |
| `Select` | [story](https://bridge-storybook.novatech.internal/?path=/story/select) | stable |
| `Combobox` | [story](https://bridge-storybook.novatech.internal/?path=/story/combobox) | beta |
| `Checkbox` | [story](https://bridge-storybook.novatech.internal/?path=/story/checkbox) | stable |
| `Radio` | [story](https://bridge-storybook.novatech.internal/?path=/story/radio) | stable |
| `Switch` | [story](https://bridge-storybook.novatech.internal/?path=/story/switch) | stable |
| `Tabs` | [story](https://bridge-storybook.novatech.internal/?path=/story/tabs) | stable |
| `Modal` | [story](https://bridge-storybook.novatech.internal/?path=/story/modal) | stable |
| `Drawer` | [story](https://bridge-storybook.novatech.internal/?path=/story/drawer) | stable |
| `Tooltip` | [story](https://bridge-storybook.novatech.internal/?path=/story/tooltip) | stable |
| `Popover` | [story](https://bridge-storybook.novatech.internal/?path=/story/popover) | stable |
| `Toast` | [story](https://bridge-storybook.novatech.internal/?path=/story/toast) | stable |
| `Breadcrumbs` | [story](https://bridge-storybook.novatech.internal/?path=/story/breadcrumbs) | stable |
| `Badge`, `Tag` | [story](https://bridge-storybook.novatech.internal/?path=/story/badge) | stable |
| `EmptyState` | [story](https://bridge-storybook.novatech.internal/?path=/story/empty-state) | stable |
| `Skeleton` | [story](https://bridge-storybook.novatech.internal/?path=/story/skeleton) | stable |

## Документационные компоненты (`shared/ui/docs`)

| Компонент | Storybook | Статус |
|---|---|---|
| `Callout` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-callout) | stable |
| `CodeBlock` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-code-block) | stable |
| `CodeTabs` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-code-tabs) | stable |
| `EndpointBadge` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-endpoint-badge) | stable |
| `ParameterTable` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-parameter-table) | stable |
| `TryEndpoint` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-try-endpoint) | beta |
| `TocSidebar` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-toc-sidebar) | stable |
| `AnchorList` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-anchor-list) | stable |
| `VersionBadge` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-version-badge) | stable |
| `DeprecationBanner` | [story](https://bridge-storybook.novatech.internal/?path=/story/docs-deprecation-banner) | stable |

## Доменные компоненты

### Sandbox / Playground (`domains/sandbox/ui`)

| Компонент | Storybook | Статус |
|---|---|---|
| `MethodPicker` | [story](https://bridge-storybook.novatech.internal/?path=/story/sandbox-method-picker) | stable |
| `RequestBuilder` | [story](https://bridge-storybook.novatech.internal/?path=/story/sandbox-request-builder) | stable |
| `JsonEditor` | [story](https://bridge-storybook.novatech.internal/?path=/story/sandbox-json-editor) | stable |
| `ResponseViewer` | [story](https://bridge-storybook.novatech.internal/?path=/story/sandbox-response-viewer) | stable |
| `ShareSnapshot` | [story](https://bridge-storybook.novatech.internal/?path=/story/sandbox-share-snapshot) | beta |
| `HistoryDrawer` | [story](https://bridge-storybook.novatech.internal/?path=/story/sandbox-history-drawer) | experimental |

### Keys / Quotas / Organization (`domains/keys/ui`, ...)

| Компонент | Storybook | Статус |
|---|---|---|
| `KeyCard` | [story](https://bridge-storybook.novatech.internal/?path=/story/keys-card) | stable |
| `KeyCreateDialog` | [story](https://bridge-storybook.novatech.internal/?path=/story/keys-create-dialog) | stable |
| `RotateConfirm` | [story](https://bridge-storybook.novatech.internal/?path=/story/keys-rotate-confirm) | stable |
| `RevokeConfirm` | [story](https://bridge-storybook.novatech.internal/?path=/story/keys-revoke-confirm) | stable |
| `QuotaBar` | [story](https://bridge-storybook.novatech.internal/?path=/story/quotas-bar) | stable |
| `QuotaSummary` | [story](https://bridge-storybook.novatech.internal/?path=/story/quotas-summary) | beta |
| `MemberRow` | [story](https://bridge-storybook.novatech.internal/?path=/story/org-member-row) | beta |
| `RoleSelect` | [story](https://bridge-storybook.novatech.internal/?path=/story/org-role-select) | beta |

## Добавление нового компонента

1. Анна публикует компонент в Figma-библиотеке `Bridge / Components`.
2. Создаётся issue в Jira (`BRIDGE-UI-...`).
3. Реализация — в `src/shared/ui/`, `src/shared/ui/docs/` или `src/domains/<x>/ui/`.
4. Story обязательна; для документационных компонентов — обязательна **дополнительная** story с компонентом в потоке текста (см. [Регламент передачи макетов](/projects/bridge/handoff-regulation)).
5. PR ревьюят [Марина](/team#марина) или [Андрей](/team#андрей) (код) и [Анна](/team#анна) (визуальное соответствие).
6. После мерджа — статус `beta`. Перевод в `stable` — после месяца использования без правок API и подтверждения a11y / dark theme.

## Deprecation

Сейчас deprecated-компонентов нет. Возможно появление deprecated-помеченных версий после Q4 2025, когда команда начнёт миграцию некоторых внутренних компонентов на готовые библиотечные (например, замена internal `JsonEditor` на расширенную обёртку Monaco).
