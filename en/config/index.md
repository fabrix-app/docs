#### [Docs](../index.md) / Configuration

# 3. Configuration

Fabrix provides a unified way to configure all facets of your application. All configuration files are located in the `config/` directory.

## 3.1. [`index.ts`](manifest.md)

A manifest of the available configuration files.

## 3.2. [`main.ts`](main.md)

Declare the Spools to load, set filesystem paths, and other basic application settings.

## 3.3. [`log.ts`](log.md)

Setup the logger that Fabrix will use during runtime.

## 3.4. [`routes.ts`](routes.md)

Define how web requests are routed to controllers.

## 3.5. [`stores.ts`](stores.md)

Define data stores. Each [Model](../build/model.md) maps itself to a "store".

## 3.6. [`i18n.ts`](i18n.md)

Setup internationalization, define multi-language mappings, etc.

## 3.7. [`web.ts`](web.md)

Configure the web server. Set the listening port, load any plugins, and define advanced web server settings.

# Environment

Fabrix can load environment-dependent configuration based on the `NODE_ENV` environment variable. By default, `NODE_ENV=development`. By creating separate configurations inside `config/env/`, different configurations are loaded based on the value of `NODE_ENV`. Note: Anytime you add a config file, remember to add it to the [`index.ts`](index.md) manifest!

## Example

When `NODE_ENV=production`, the following configuration in `config/env/production.ts` will override the existing value of `config.web.hostname` set in `config/web.ts`.

```js
// config/env/production.ts
export const production = {
  web: {
    hostname: 'fabrix.app'
  }
}
```

# Runtime Configuration

## Access

The application configuration is available at `this.config` inside all Controllers, Services, and Policies. The nested objects in the configuration files are flattened into a [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map).

```js
// api/services/MapService.ts
export class MapService extends Service {

  /**
   * Renders a single map tile
   */
  getTile ({ x, y }) {
    const threads = this.config.get('map.renderThreads')

    return this.getMap({ x, y }, threads)
  }
}
```

## Modify

The configuration is frozen and *immutable* during runtime; specifically, the configuration is frozen after all Spools complete their `configure` stage.

```js
// spool-mapnik/index.ts
export class MapnikSpool extends Spool {

  /**
   * Set the default number of renderThreads based on the number of CPU cores
   * available on the system.
   */
  configure () {
    const cpuCores = os.cpus().length
    this.config.set('map.renderThreads', cpuCores)
  }

  /**
   * Changed my mind; need to set map.renderThreads to something else
   */
  initialize () {
    this.config.set('map.renderThreads', 4) // throws IllegalAccessError
  }
}
```
