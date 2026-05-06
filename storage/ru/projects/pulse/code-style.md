# Код-стайл Pulse

## Общие правила

Код-стайл проекта строится на двух инструментах: ESLint (логика и архитектурные ограничения) и Prettier (форматирование). Локально оба запускаются через `husky + lint-staged` в pre-commit. В CI выполняются оба полным проходом по всему репозиторию.

## Нейминг

### Файлы и папки

- Папки и файлы — `kebab-case`: `employees-table/`, `use-debounced-value.ts`.
- React-компоненты — `PascalCase` внутри файла, имя файла совпадает по смыслу: `MetricCard.tsx` экспортирует `MetricCard`.
- Хуки — `use-camel-case.ts`, экспорт — `useCamelCase`.
- Контейнер/презентер — `MetricCard.container.tsx` / `MetricCard.view.tsx` (см. [Архитектура приложения](/projects/pulse/architecture)).

### Переменные

- Переменные и функции — `camelCase`.
- Константы модуля — `UPPER_SNAKE_CASE`, экспорты констант — тоже.
- Типы и интерфейсы — `PascalCase`. Интерфейсам не предшествует `I`. Типы пропсов — `MetricCardProps`.
- Enum'ов избегаем — используем `as const` объекты:
  ```ts
  export const Status = { Idle: 'idle', Loading: 'loading' } as const;
  export type Status = typeof Status[keyof typeof Status];
  ```

### CSS-классы (SCSS Modules + BEM)

```scss
.card { /* блок */ }
.card__title { /* элемент */ }
.card--compact { /* модификатор */ }
```

Внутри `*.module.scss` только эти три формы. Модификаторы по состоянию — отдельно: `.card--loading`, `.card--error`. Глобальные стили запрещены вне `app/styles/global.scss`.

## ESLint

База — `@novatech/eslint-config-react`. Локальные правила:

```js
// .eslintrc.cjs (фрагмент)
module.exports = {
  extends: ['@novatech/react'],
  rules: {
    'no-restricted-imports': ['error', {
      patterns: ['@/features/*/!(index)', '@/entities/*/!(index)']
    }],
    'import/order': ['error', {
      groups: ['builtin', 'external', 'internal', 'parent', 'sibling'],
      pathGroups: [{ pattern: '@/**', group: 'internal' }],
      'newlines-between': 'always'
    }],
    '@typescript-eslint/consistent-type-imports': 'error',
    'react/jsx-no-leaked-render': 'error'
  }
};
```

Запрет на глубокий импорт реализует правило public API сегментов (см. [Архитектура приложения](/projects/pulse/architecture)).

## Prettier

```json
{
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "arrowParens": "always",
  "bracketSpacing": true
}
```

Не переопределяем — это общая конфигурация студии.

## TypeScript

`tsconfig.json` идёт в strict-mode. Дополнительно включены:

- `noUncheckedIndexedAccess: true`
- `exactOptionalPropertyTypes: true`
- `noImplicitOverride: true`

`any` запрещён ESLint-правилом. Если действительно нужно — `// eslint-disable-next-line` с комментарием, объясняющим причину. На ревью такие места обсуждаются отдельно.

## React

- Только функциональные компоненты, hooks API.
- Деструктуризация пропсов в сигнатуре: `({ title, value }: MetricCardProps)`.
- `useEffect` — только с явным набором зависимостей, исключения комментируются.
- Запрещено тянуть бизнес-логику в `view`-компоненты (см. контейнер/презентер).

## Комментарии

- Комментарий — это «почему», а не «что». Имя переменной должно объяснять «что».
- TODO/FIXME требуют ссылки на тикет в Jira: `// TODO(PULSE-1234): ...`.
- JSDoc — только для shared-утилит, не для компонентов.

## Чек-лист код-ревью

Подробно — в [Работа с репозиторием](/projects/pulse/repository-workflow). Кратко: имена соответствуют конвенциям; нет глубоких импортов; типы без `any`; SCSS-модули по BEM; для UI-изменений приложен скриншот и подтверждение от Олега.
