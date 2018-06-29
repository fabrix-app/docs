#### [Docs](../../) / [API Reference](./) / fabrix

# 9.1 fabrix API

## `class` fabrix `extends` [EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)

This class instantiates, starts, and stops the application and all Spools.

### `constructor (app)`

Initialize the application with the provided `app` definition. All configured Spools are instantiated, and context is bound to resource (Controller, Service, etc) methods.

| @param | type | description | required? |
|:---|:---|:---|:---|
| `app` | `Object` | the app definition | yes. throws `RangeError`) |
| `app.pkg` | `Object` | the `package.json` of the module | yes. throws `PackageNotDefinedError` |
| `app.api` | `Object` | the application's API definition | yes. throws `ApiNotDefinedError` |

| @emits | description |
|:---|:---|
| `spool:<pack>:constructed` | after each Spool is successfully instantiated |

In a [New fabrix Application](../start.md), fabrix is instantiated in `server.js` like so:

```js
const fabrix = require('fabrix')
const app = require('./')
const fabrix = new fabrix(app)

fabrix.start().catch(err => fabrix.stop(err))
```

| @return type | description |
|:---|:---|
| `fabrix` | the fabrix Application |


## Methods

### `start ()`

Binds event listeners and initiates the Spool Boot Process.

| @emits | description |
|:---|:---|
| `fabrix:start` | emitted before the start of the Spool boot process |
| `fabrix:ready` | emitted once all Spools have successfully initialized |
| `spool:<pack>:validated` | emitted after each Spool completes the `validate` stage |
| `spool:<pack>:configured` | emitted after each Spool completes the `configure` stage |
| `spool:<pack>:initialized` | emitted after each Spool completes the `initialized` stage |
| `spool:all:validated` | emitted after all Spools have completed the `validated` stage |
| `spool:all:configured` | emitted after all Spools have completed the `configure` stage |
| `spool:all:initialized` | emitted after all Spools have completed the `initialize` stage |

| @return type | description |
|:---|:---|
| `Promise (fabrix)` | the fabrix Application |

### `stop (err)`

Unbind all event listeners and unload all Spools.

| @param | type | description | required? |
|:---|:---|:---|:---|
| `err` | `Error` | the error that caused the application to stop | no |

| @emits | description |
|:---|:---|
| `fabrix:stop` | emitted before the Spools unload |
| `spool:<pack>:unloaded` | emitted after each Spool completes the `unload` stage |
| `spool:all:unloaded` | emitted after all Spools have completed the `unload` stage |

| @return type | description |
|:---|:---|
| `Promise (fabrix)` | the fabrix Application |

### `emit (eventName [, ...args])`

Emits the event, and logs the event at the `debug` level.

| @overrides |
|:---|
| [`EventEmitter.emit`](https://nodejs.org/api/events.html#events_emitter_emit_eventname_args) |

| @param | type | description | required? |
|:---|:---|:---|:---|
| `eventName` | `String` | calls the listeners registered to `eventName` | yes |
| `...args` | any | Arguments passed to the listeners | no |

| @return type | description |
|:---|:---|
| `fabrix` | the fabrix Application |

### `onceAny (eventNames [, handler])`

Resolve Promise (or invoke optional `handler`) once **any** of the given events has been emitted.

| @param | type | description | required? |
|:---|:---|:---|:---|
| `eventNames` | `String` | event names to listen for | yes |
| `handler` | `Function` | handler function to invoke instead of Promise | no |

| @return type | description |
|:---|:---|
| `Promise(Array<Argument>)` | the arguments passed by the first event emitted |

### `after (eventNames [, handler])`

Resolve Promise (or invoke optional `handler`) once **all** of the given events have been emitted.

| @param | type | description | required? |
|:---|:---|:---|:---|
| `eventNames` | `String` | event names to listen for | yes |
| `handler` | `Function` | handler function to invoke instead of Promise | no |

| @return type | description |
|:---|:---|
| `Promise(Array<Argument>)` | the arguments passed by the each emitted event |

### `log`

Log a message.

| @emits | description |
|:---|:---|
| `log:<level>` | contains the content of the log message |

`<level>` will be one of:
- silly
- debug
- info
- warn
- error

```js
this.app.log.debug('hello')
```

Inside resource classes (e.g. Controller, Service, Policy) and Spools, the following shorthand is available:

```js
this.log.debug('hello')
```

### `__` and `t`

Translate a message according to the application's [i18n Configuration](../config/i18n.md).

```js
this.app.__('hello.world')
this.app.t('hello.world')
```

Inside resource classes (e.g. Controller, Service, Policy) and Spools, the following shorthand is available:

```js
this.__('hello.world')
this.t('hello.world')
```

## Fields

### `env`

Type: `Object`

A serialized cache of `process.env`. Accessing environment variables using this field is more performant than using `process.env` directly.

```js
{
  NODE_ENV: 'development',
  SHELL: '/bin/bash',
  USER: 'fabrix-app',

  // ... etc
}
```

### `versions`

Type: `Object`

A serialized cache of `process.versions`.

```js
{
  http_parser: '2.7.0',
  node: '6.9.4',
  v8: '5.1.281.89',

  // ... etc
}
```

### `config`

Type: `Map`

The app configuration.

```js
this.app.config.get('main.paths.root') // e.g. /home/fabrix-app/workspace/myapp
```

### `models`

Type: `Object`

The application's Models

```js
const user = new this.app.models.User({
  username: 'fabrix',
  // ...
})
```

### `services`

Type: `Object`

The application's Services

```js
this.app.services.MapService.getTile({ x, y })
  .then(tile => reply(tile)
```

### `pkg`

Type: `Object`

The `package.json` for the running module.

```js
this.app.pkg.name     // e.g. 'my-fabrix-app'
this.app.pkg.version  // e.g. 0.0.0
```

### `_fabrix`

Type: `Object`

The `package.json` for the `fabrix` module.

```js
this.app._fabrix.name     // 'fabrix'
this.app._fabrix.version  // e.g. 3.0.0
```

## Next: [Spool API](spool.md)
