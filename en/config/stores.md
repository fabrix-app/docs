#### [Docs](../index.md) / [Configuration](./index.md) / stores.ts

# 3.5. `stores.ts`

Define data stores. Each [Model](../build/model.md) maps itself to a "store". A single Fabrix application can configure many data stores of similar or different types.

```js
// config/stores.ts

export const stores = {

  /**
   * A store named "localdisk" that uses the Waterline ORM
   */
  roledb: {
    orm: 'waterline',
    adapter: require('waterline-sqlite3'),
    migrate: 'alter'
  },

  /**
   * A store named "localpostgres" that uses Knex.js
   */
  userdb: {
    orm: 'knex',
    client: 'pg',
    connection: {
      host: 'localhost',
      user: 'fabrix',
      password: 'parkranger',
      database: 'fabrix'
    }
  }

}
```

The configuration makes use of functionality provided by [spool-waterline](https://github.com/fabrix-app/spool-waterline) and [spool-knex](https://github.com/fabrix-app/spool-knex).

## `roledb`

This store defines an `adapter` property, which the Waterline Spool will use to establish a connection to a local SQLite database. [Full Waterline Spool Docs](https://github.com/fabrix-app/spool-waterline).

## `userdb`

This store defines `client` and `connection` properties, which the Knex Spool will use to establish a connection to a Postgres database. [Full Knex Spool Docs](https://github.com/fabrix-app/spool-knex).

# Usage

## Models

Each Model defines its backing store. To illustrate how multiple stores can be used, we'll store Users in the Postgres database, and we'll store associated Roles in the SQLite3 database.

```js
// api/models/User.ts

export class User extends Model {
  static config () {
    return {
      store: 'userdb'
    }
  }

  /**
   * The Knex Spool sends us a Knex "table" object to define the schema
   */
  static schema (table) {
    table.increments('id').primary()
    table.string('username')
  }
}
```

```js
// api/models/Role.ts

export class Role extends Model {
  static config () {
    return {
      /**
       * Override table name. By default, database tables/collections are named
       * after the Model name. (i.e., "role" by default)
       */
      tableName: 'userroles',
      store: 'roledb'
    }
  }

  /**
   * The Waterline Spool requires that a Waterline schema definition object
   * is returned
   */
  static schema () {
    return {
      id: {
        primaryKey: true
      },
      name: {
        type: 'string',
        notNull: true
      },
      user: {
        type: 'integer',
        index: true
      }
    }
  }
}
```

## Services

```js
// api/services/PermissionService.ts

export class PermissionService extends Service {
  /**
   * Return true if the given User has the required role; false otherwise.
   */
  isPermitted (user, requiredRole) {
    return this.app.orm.Role
      .find({
        user: user.id,
        name: requiredRole
      })
      .then(([ role ]) => return !!role)
  }
}
```
