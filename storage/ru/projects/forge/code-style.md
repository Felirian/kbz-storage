# Код-стайл Forge

## Общие правила

Стандарты те же, что в студии в целом: ESLint + Prettier на pre-commit и в CI, TypeScript strict, SCSS Modules с BEM. Ниже — отличия и локальные правила Forge.

## Нейминг

### Файлы и папки

- Папки и файлы — `kebab-case`.
- React-компонент — `PascalCase` имя экспорта; имя файла отражает компонент: `FieldEditor.tsx`.
- В редакторе используется суффикс роли: `*Editor.tsx` (для редактора) и `*Runtime.tsx` (для рантайма). Так в одном модуле можно держать два варианта одного поля.
- Хуки — `use-camel-case.ts`.
- Селекторы Zustand — `select-camel-case.ts`, экспортируются как `selectCurrentSection`, `selectFieldById`.

### Сущности модели

- Типы схемы — `Form*`, `Field*`, `Section*`, `Validation*`.
- Идентификаторы внутри схемы — UUID v4.
- Поля схемы в JSON — `camelCase`. Расширения JSON Schema под Forge — с префиксом `x-forge-` (см. [Используемые технологии](/projects/forge/tech-stack)).

### CSS

SCSS Modules + BEM, как в [Pulse code-style](/projects/pulse/code-style). Дополнительно: классы, относящиеся к редактору, могут содержать модификатор `--editor`, чтобы визуально отличать их от runtime-вариантов:

```scss
.field--editor { /* ручки, бордеры и т.д. */ }
```

## ESLint

База — `@novatech/eslint-config-react-next`. Локальные правила:

```js
module.exports = {
  extends: ['@novatech/react-next'],
  rules: {
    'no-restricted-imports': ['error', {
      patterns: [
        { group: ['@/modules/editor/*'], message: 'Editor запрещён к импорту в runtime/core' },
      ],
    }],
    '@typescript-eslint/consistent-type-imports': 'error',
    'react/jsx-no-leaked-render': 'error',
    'no-console': ['error', { allow: ['warn', 'error'] }],
  },
  overrides: [
    {
      files: ['src/modules/runtime/**'],
      rules: {
        'no-restricted-imports': ['error', {
          patterns: ['@/modules/editor/*', 'immer'],
        }],
      },
    },
  ],
};
```

Запрет `immer` в `runtime` — следствие архитектурной цели: рантайм не должен раздуваться (см. [Архитектура приложения](/projects/forge/architecture)).

## Prettier

Общая конфигурация студии — без локальных переопределений.

## TypeScript

`strict: true`, плюс:

- `noUncheckedIndexedAccess: true`;
- `exactOptionalPropertyTypes: true`;
- `noFallthroughCasesInSwitch: true`.

`any` запрещён. В core-модуле дополнительно действует правило: типы схемы не должны быть `unknown` без последующего нарративного `parse`/`assert` через Ajv или ручной guard.

## React и Next.js

- Только функциональные компоненты.
- Server Components используем где это уместно — лендинг, доки, shell страницы. Прямой импорт клиентских библиотек (Zustand, React DnD) в RSC запрещён ESLint-правилом `no-restricted-imports` для соответствующих файлов.
- `useEffect` — с явными зависимостями. В редакторе сложные зависимости комментируются, потому что встречаются циклы по undo/redo.

## Работа со схемой

- Изменения схемы — только через операции из `modules/core/operations`. Прямые мутации (даже под Immer) в `modules/editor` запрещены.
- Каждая операция — pure-функция: принимает схему и параметры, возвращает новую схему.
- Все операции имеют юнит-тесты. Это требование [Натальи](/team#наталья) и [Марины](/team#марина): ядро критично, регрессии в нём ломают пилотные данные.

## Комментарии

- Комментарии описывают «почему», а не «что».
- TODO/FIXME требуют тикета: `// TODO(FORGE-1234): ...`.
- В core-модуле допускаются короткие пояснения инвариантов, особенно для миграций схем.

## Чек-лист код-ревью

- Соблюдение нейминга и архитектурных границ (см. [Архитектура приложения](/projects/forge/architecture)).
- Изменения схемы — только через core-операции и сопровождаются тестами.
- Нет импорта editor → runtime/core.
- В runtime не появляются «тяжёлые» зависимости (Immer, React DnD).
- Бандл-чек в CI прошёл (см. [Работа с репозиторием](/projects/forge/repository-workflow)).
- Если затронут UI — есть отметка дизайнера.
