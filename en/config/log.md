#### [Docs](../../) / [Configuration](./) / log.ts

# 3.3. `log.js`

Setup the logger that Fabrix will use during runtime.

## `logger`

In order to boot, Fabrix requires that the value `config.log.logger` is set with an object that contains at least the following methods:

- debug
- info
- warn
- error

An example using the [*winston*](https://github.com/winstonjs/winston) logger:

```js
// config/log.ts
export const log = {
  logger: new winston.Logger({
    level: 'debug',
    exitOnError: false,
    transports: [
      new (winston.transports.Console)({
        prettyPrint: true,
        colorize: true
      })
    ]
  })
}
```

## Environment-Specific Logging

When deploying to production, you will likely want to configure the logger to level `info` or higher. This can be done using [Environment Configuration](https://github.com/fabrix-app/doc/blob/master/en/config/README.md#environment).

### Example

In this example, Winston is configured to log to the console, as well as a permanent logfile.

```js
// config/env/production.ts
export const production = {
  log: {
    logger: new winston.Logger({
      level: 'info',
      exitOnError: false,
      transports: [

        new winston.transports.Console({
          timestamp: true
        }),

        new winston.transports.File({
          name: 'error-file',
          level: 'warn',
          filename: 'fabrix.error.log',
          timestamp: true
        })

      ]
    }
  }
}
```
