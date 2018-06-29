#### [Docs](../../) / [Configuration](./) / main.js

# 3.2. `main.js`

Declare the Spools to load, set filesystem paths, and other basic application settings.

```js
// config/main.js

module.exports = {
  packs: [
    require('spool-router'),
    require('spool-hapi'),
    require('spool-mapnik')
  ],

  paths: {
    root: path.resolve(__dirname, '..'),
    // ... other paths
  }
}
```

## `main.packs`

The list of Spools to load into the framework. Spools to include can be found on the [Plugins](http://fabrix.app/plugins) page. Note: if `main.packs` is empty, the program will start and immediately exit!

As discussed in the [fabrix Design Theory](../ref/theory.md) section:

> The fabrix framework has exactly one feature: the ability to harmoniously manage Spools.
> The capability available to the developer is thus an emergent property of the Spools the user has installed. 

Read more about Spools:
- [Concepts: Spool](../build/spool.md)
- [Reference: Spool API](../ref/spool.md)

## `main.paths`

fabrix and Spools use these paths during runtime for many reasons. fabrix and some Spools may store temporary information such as REPL history, compiled asset files, logfiles, etc.
