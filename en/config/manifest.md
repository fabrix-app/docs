#### [Docs](../../) / [Configuration](./) / index.js

# 3.1. `index.js`

A manifest of the available configuration files. Configuration files added/created should be required in this file. This file should not be used to define any configuration values.

```js
// config/index.ts

export { main } from './main'
export { log } from './log'
export { routes } from './routes'
// ... etc
```

## Environment Configuration

Each environment-specific configuration should contain its own manifest file.

```js
// config/env/index.ts

export { staging } from './staging'
```

```js
// config/env/staging/index.ts

export { main } from './main'
export { log } from './log'
export { routes } from './routes'
// ... etc
```
