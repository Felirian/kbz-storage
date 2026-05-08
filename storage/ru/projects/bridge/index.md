---
title: Bridge — навигация по документации
description: Оглавление документации проекта Bridge: разделы по менеджменту, разработке и дизайну портала для API-партнёров.
---

# Bridge

Портал для внешних разработчиков-партнёров: документация API, sandbox для тестирования запросов, управление ключами доступа и квотами. Статус: **pre-launch**, пилотное тестирование с партнёрами.

**Команда:** [Андрей](/team#андрей), [Марина](/team#марина), [Кирилл](/team#кирилл), [Анна](/team#анна)

---

## Содержание

### Менеджмент

- [Паспорт проекта](/projects/bridge/passport) — цель, внешняя аудитория (API-партнёры), бизнес-метрики, ключевые даты и риски
- [Структура команды](/projects/bridge/team-structure) — роли, матрица RACI, особенности команды без выделенного PO, порядок эскалации
- [Процессы](/projects/bridge/processes) — feature-branch стратегия, спринт-цикл, security-ревью как обязательный этап
- [Беклог и roadmap](/projects/bridge/backlog-roadmap) — приоритизация MoSCoW, требования к launch readiness, roadmap по кварталам

### Разработка

- [Используемые технологии](/projects/bridge/tech-stack) — MDX, Swagger UI, Monaco Editor, OAuth 2.0 PKCE, особенности security-стека
- [Архитектура приложения](/projects/bridge/architecture) — BFF-паттерн, MDX-конвейер, модули Sandbox и Playground, изоляция auth-слоя
- [Код-стайл](/projects/bridge/code-style) — нейминг, ESLint с security-правилами, запрет хранения секретов, MDX-конвенции
- [Работа с репозиторием](/projects/bridge/repository-workflow) — feature-branch workflow, обязательный security-апрув, CI с SAST, процедура релиза
- [API-взаимодействие](/projects/bridge/api-integration) — OAuth PKCE, ротация ключей, rate limiting, BFF-проксирование, мониторинг

### Дизайн

- [Стайлгайд](/projects/bridge/styleguide) — палитра, типографика для документации и UI, специфика code-тем (светлая/тёмная)
- [Дизайн-система](/projects/bridge/design-system) — компоненты портала, DocBlock-паттерны, состояния Playground, accessibility-требования
- [Регламент передачи макетов](/projects/bridge/handoff-regulation) — чек-лист UX/UI, обязательная проверка в MDX-окружении, aria-спецификации
- [UI-библиотека](/projects/bridge/ui-library) — реестр компонентов: документационные, навигационные, интерактивные (Sandbox, Playground)

---

**Storybook:** `https://bridge-storybook.novatech.internal` · **Jira:** `BRIDGE` · **Slack:** `#bridge-team`

> ⚠️ Изменения в модулях auth и keys проходят обязательный security-ревью у [Андрея](/team#андрей) перед мерджем.
