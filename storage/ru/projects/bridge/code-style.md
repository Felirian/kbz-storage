---
title: Bridge · Код-стайл
description: Нейминг, ESLint с security-правилами, запрет хранения секретов в коде, конвенции для MDX-файлов.
---

# Код-стайл Bridge

## Общие правила

ESLint + Prettier на pre-commit и в CI; TypeScript strict; SCSS Modules + BEM. Дополнительно для Bridge действуют security-ориентированные правила и более строгие требования к типизации внешних данных.

## Нейминг

### Файлы и папки

- Папки и файлы — `kebab-case`.
- Компоненты — `PascalCase` имя экспорта; имя файла отражает компонент.
- Хуки — `use-camel-case.ts`, экспорт — `useCamelCase`.
- Контекстные обёртки — суффикс `Provider` (`AuthProvider.tsx`).
- MDX-страницы — `kebab-case.mdx`. Имя файла = последний сегмент URL.
- OpenAPI-генерируемые сущности — префикс `Api` (`ApiKey`, `ApiQuotaUsage`), чтобы не путаться с внутренними доменными моделями.

### Сущности

- Доменные типы — `PascalCase` без префикса `I`.
- Константы модуля — `UPPER_SNAKE_CASE`.
- Перечисления — `as const` объекты (см. ниже).

### CSS-классы

SCSS Modules + BEM. Для документационных страниц допустим короткий вид (`.docs-toc`, `.docs-toc__item`) — потому что docs выделены в отдельный домен и не используют широкие компоненты.

## ESLint

База — `@novatech/eslint-config-react`. Локальные правила:

```js
module.exports = {
  extends: ['@novatech/react'],
  rules: {
    'no-restricted-imports': ['error', {
      paths: [
        { name: 'localStorage', message: 'Используйте только httpOnly cookies через BFF' },
        { name: 'sessionStorage', message: 'Запрещено для security-чувствительных данных' },
      ],
    }],
    'no-restricted-globals': ['error', 'localStorage', 'sessionStorage'],
    '@typescript-eslint/consistent-type-imports': 'error',
    '@typescript-eslint/no-explicit-any': 'error',
    'react/jsx-no-leaked-render': 'error',
    'no-console': ['error', { allow: ['warn', 'error'] }],
  },
};
```

Дополнительно подключён плагин `eslint-plugin-security` с минимальным набором правил, релевантных для фронтенда (например, проверка `eval`).

> ⚠️ Внимание: запрет `localStorage`/`sessionStorage` — не догма ради догмы. Это следствие архитектурного решения [Андрея](/team#андрей) (см. [Архитектура приложения](/projects/bridge/architecture)). Исключения возможны только для не-чувствительных данных (например, тема оформления) и оформляются `// eslint-disable-next-line` с объяснением.

## Prettier

Общая конфигурация студии, без локальных переопределений.

## TypeScript

`strict: true`, плюс:

- `noUncheckedIndexedAccess: true`;
- `exactOptionalPropertyTypes: true`;
- `noFallthroughCasesInSwitch: true`;
- `useUnknownInCatchVariables: true`.

`any` запрещён ESLint-правилом. Внешние данные (ответы API, данные от партнёра) типизируются через сгенерированные типы из OpenAPI (генерация через `openapi-typescript`).

```ts
import type { paths } from '@/shared/api/generated';

type CreateKeyResponse = paths['/v1/keys']['post']['responses']['201']['content']['application/json'];
```

## React

- Только функциональные компоненты.
- `useEffect` — с явными зависимостями.
- В sandbox-домене разрешено использование `useReducer` для сложных состояний редактора — это упрощает отладку.

## OAuth и работа с токенами

- Токены **никогда** не появляются в JS-коде (живут в `httpOnly` cookie). В TypeScript нет типа для access-token, потому что код его не видит.
- PKCE-параметры (verifier, challenge) хранятся в `sessionStorage` **только** на время редиректа; правило ESLint выключается локально с комментарием и ссылкой на RFC по auth.

## Комментарии

- Комментарий объясняет «почему».
- TODO/FIXME требуют ссылки на тикет: `// TODO(BRIDGE-1234): ...`.
- В security-чувствительных местах (auth, ключи, sandbox) допустимы развёрнутые комментарии с объяснением threat model.

## Чек-лист код-ревью

- Соблюдение нейминга и архитектурных границ доменов.
- Нет прямого доступа к localStorage/sessionStorage в новых местах.
- Внешние данные типизированы через сгенерированные типы.
- Если задача `security: yes` — отдельный апрув от Андрея зафиксирован в комментарии PR.
- Если затронут UI — есть отметка дизайнера (`design-review: ok`).
- Если затронута документация — обновлены `guides/` и/или `api-reference/`, без расхождений с OpenAPI.
- Bundle size в пределах целей (см. [Архитектура приложения](/projects/bridge/architecture)).
