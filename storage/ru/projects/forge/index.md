---
title: Forge — навигация по документации
description: Оглавление документации проекта Forge: разделы по менеджменту, разработке и дизайну low-code конструктора форм.
---

# Forge

Low-code конструктор для создания внутренних форм, маршрутов согласования и шаблонов документов. Позволяет корпоративным клиентам настраивать процессы без участия разработчиков. Статус: **active development, public beta**.

**Команда:** [Марина](/team#марина), [Дарья](/team#дарья), [Виктория](/team#виктория), [Наталья](/team#наталья)

---

## Содержание

### Менеджмент

- [Паспорт проекта](/projects/forge/passport) — цель, аудитория (конструкторы и исполнители), бизнес-метрики, ключевые даты и риски
- [Структура команды](/projects/forge/team-structure) — роли, матрица RACI, особенности продуктовой команды, эскалация
- [Процессы](/projects/forge/processes) — недельный спринт-цикл, trunk-based с фича-флагами, SLA для пилотных клиентов
- [Беклог и roadmap](/projects/forge/backlog-roadmap) — приоритизация по MoSCoW, источники требований, roadmap по кварталам

### Разработка

- [Используемые технологии](/projects/forge/tech-stack) — Next.js SSR, React DnD, Zustand + Immer, JSON Schema, особенности сборки
- [Архитектура приложения](/projects/forge/architecture) — архитектура конструктора и рантайма, движок схем, нормализованная модель полей
- [Код-стайл](/projects/forge/code-style) — нейминг, ESLint, TypeScript-специфика для JSON Schema, правила покрытия тестами
- [Работа с репозиторием](/projects/forge/repository-workflow) — trunk-based development, фича-флаги, Conventional Commits, правила PR и ревью
- [API-взаимодействие](/projects/forge/api-integration) — REST API конструктора и рантайма, авторизация, webhooks, валидация на сервере

### Дизайн

- [Стайлгайд](/projects/forge/styleguide) — палитра, типографика, сетка, специфика двух контекстов — конструктор и рантайм
- [Дизайн-система](/projects/forge/design-system) — компоненты конструктора и рантайма, токены, состояния полей, drag-and-drop паттерны
- [Регламент передачи макетов](/projects/forge/handoff-regulation) — чек-лист для Product Designer, участие в A/B-тестах, ревью пилотных профилей
- [UI-библиотека](/projects/forge/ui-library) — реестр компонентов обоих слоёв (конструктор / рантайм) со ссылками на Storybook

---

**Storybook:** `https://forge-storybook.novatech.internal` · **Jira:** `FORGE` · **Slack:** `#forge-team`
