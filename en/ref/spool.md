#### [Docs](../index.md) / [API Reference](./index.md) / Spool

# 9.2 Spool API

## `class` Spool

This interface is implemented by all Spools, and is relied upon by the Fabrix Application to harmoniously load and manage them.

### `constructor (app, spool)`

Initialize the application with the provided Fabrix `app` instance.

| @param | type | description | required? |
|:---|:---|:---|:---|
| `app` | `fabrix` | the Fabrix application instance | yes |
| `spool` | `Object` | the Spool definition | yes |
| `spool.pkg` | `Object` | the `package.json` of the Spool | yes. throws `PackageNotDefinedError` |
| `spool.config` | `Object` | any custom configuration that the Spool requires | no |
| `spool.api` | `Object` | any additonal API resources to mix-in to the Fabrix `app` | no |

#### How to Subclass

Each Spool subclass will provide the `spool` definition to this superclass. For example:

```js
export class BootstrapSpool extends Spool {

  /**
   * Fabrix passes in "app" as a reference to itself
   * @override
   */
  constructor (app) {

    // This constructor passes along the "app" reference, and provides its own custom
    // "spool" definition to the Spool superclass constructor.
    super(app, {
      pkg: require('./package'),
      config: require('./config'),
      api: { }
    })
  }
}
```

#### Instantiation

Spools are never instantiated directly by the developer. Each Spool is instantiated in the [`Fabrix`](../fabrix.md) constructor like so:
```js
this.config.get('main.spools').forEach(Pack => new Pack(this))
```
where `this` references the `fabrix` instance. Note that `spool` argument is required here because the Spool subclass will provide this to its superclass (this class).

## Methods

The following methods correspond to each of the four [Spool Lifecycle](./build/spool.md) stages:
- validate
- configure
- initialize
- sanity
- unload

Note: Do not invoke these methods directly in your application code.

## `validate ()`

Validate any necessary preconditions for this spool. We strongly recommend that all Spools override this method and use it to check preconditions.

```js
export class BootstrapSpool extends Spool {

  /**
   * Make sure a bootstrap method is available
   */
  validate () {
    if (!this.app.config.get('bootstrap')) {
      throw new Error('config.bootstrap is required!')
    }
  }
}
```

## `configure ()`

Set any configuration required before the spools are initialized. Spools that require configuration, or need to alter/extend the app's configuration, should override this method. After all Spools have completed the `configure` phase (specifically, on the event `spool:all:configured`), the app configuration will be frozen. This is the last stage at which modifying app configuration is possible.

```js
export class BootstrapSpool extends Spool {

  /**
   * Bind the Fabrix app to the bootstrap function's context so that it can fire events.
   */
  configure () {
    this.config.bootstrap = this.config.bootstrap.bind(this.app)
  }
}
```

## `initialize ()`

This is the final stage in the Spool boot process. After the successful completion of the `validate` and `configure` stages, this method can now rely on having the necessary preconditions. In this stage, the Spool can start any necessary services or listeners that are needed to extend the framework functionality: start web servers, connect to databases, etc. **In short, this is the place to do whatever it is that your Spool is designed to do.**


```js
export class BootstrapSpool extends Spool {

  /**
   * Invoke the user-configured bootstrap function
   */
  initialize () {
    return this.config.bootstrap()
  }
}
```

## `sanity ()`
This stage runs after the boot process when the configuration of the app is technically immutable. This stage is intended to check that the Fabrix app configuration is suitable for the Spool to run in a space where there may have been name space conflicts.

```js
export class BootstrapSpool extends Spool {

  /**
   * Check that a config variable is indeed what the Bootstrap spool requires after the other lifecycle stages.
   */
  async sanity () {
    if (!this.config.get('name') === 'sanity') {
      Promise.reject(new SanityError('name does not equal sanity'))
    }
  }
}
```


## `unload ()`

This method is invoked when Fabrix is shutting down, either due to an unrecoverable runtime error, or because the user has explicitly invoked `.stop()`. This method should instruct the spool to perform any necessary cleanup with the expectation that the app will stop or reload soon thereafter. If your spool runs a daemon or any other thing that may occupy the event loop, implementing this method is important for Fabrix to exit correctly.

## Next: [Controller API](controller.md)
