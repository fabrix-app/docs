# How to create a Spool
###### Fabrix - UserGuides

## Introduction

The Getting page covers how to start a Fabrix app from scratch.

Hi guys ! Here you will learn how to create a customized Spool for fabrix. You will see project's structure and how to run and test it before publishing on npm. Hope you're ready !

NOTE: This tutorial assumes that you have previously installed Node.js 4.0.0 (or more) on your machine, also have a Fabrix project created (see [Getting started with fabrix](http://blog.jaumard.com/en/2016/01/05/getting-started-with-fabrix)).

## What is a Spool ?

If you followed my first tutorial carefully, you already know this ! But I do not blame you if you forgot ^^, a Spool is a simple module which extends Fabrix functionalities. It can be a web server like express4, a tasks runner like grunt...

## Installation
Spool can be generated with yeoman. So if you didn’t install it, you should do it now :

`npm install -g yo`

Now, you can install the yeoman generator for Spool :

`npm install -g generator-spool`

You are finally ready to create your first spool ! :)

## Create a project
That’s very easy, just follow the guide :

```
mkdir mySpool;
cd mySpool;
yo spool;
```

Steps to personalized your spool are in following order:

. Name of your project (by default, it’s the name of the folder), here we let default’s name `mySpool`
. Description of your project
. Project’s homepage url: url of your project repository
. Author's Name, generally, you :)
. Author's Email
. Author's Homepage
. Package keywords
. GitHub username or organization
. Your website
. License

After this, the generator will create your project structure and install dependancies (`npm install`).

![Project generated](/assets/img/tutoYoSpool1.png)

## Project structure
You should see something like this :

![Project structure](/assets/img/tutoYoSpool2.png)

### API
As with Fabrix, you can generate api files with the cli  :

```
fab spool:api apiName;
fab spool:model modelName;
fab spool:controller controllerName;
fab spool:policy policyName;
fab spool:service serviceName;
```

WARNING: if you don't use `fab` to generate API, don't forget to add those into `index.ts` as in following: if it’s a model, add it in `models/index.ts`, if it’s a controller add it in `controllers/index.ts`... If your file is not in `index.ts`, it will be ignored !

WARNING 2:
If your Spools need Models, Controllers and Policies so your class must implement `FabrixModel`, `FabrixController` and `FabrixPolicy` in order to work with more than one orm/webserver. 

Those classes may need to implement Waterline interface definition (models) and Hapi (controllers, policies).

### Configuration
You can put your own file’s configuration here. As you can see, a `spool.ts` file communicates Fabrix you provide some APIs and configuration to the project (`provides` field). But it allows you too to configure the lifcycle of your spool. Indeed, if your spool needs to wait for some events or emit ones, you can put those here :

```
/**
 * Spool Configuration
 *
 * @see {@link http://fabrix.app/doc/spool/config
 */
export const spool = {

  /**
   * API and config resources provided by this Spool.
   */
  provides: {
    resources: ['controllers']
    api: {
      controllers: [ ]
      // ...
    },
    config: [ ]
  },

  /**
   * Configure the lifecycle of this pack; as how it boots up, and in which
   * order it loads relatively to other spools.
   */
  lifecycle: {
    configure: {
      /**
       * List of events which must be fired before the configured lifecycle
       * method is invoked in this Spool
       */
      listen: [ ],

      /**
       * List of events emitted by the configured lifecycle method
       */
      emit: [ ]
    },
    initialize: {
      listen: [ ],
      emit: [ ]
    }
  }
}
```

Let see how our Spool can interact with Fabrix server.

### Spool class
It is your Spool’s core. It’s this file which allows you to interact with Fabrix server. This class is under `index.js` and looks like :
```
const Spool = require('spool')

module.exports = class Archetype extends Spool {

  /**
   * TODO document method
   */
  validate () {

  }

  /**
   * TODO document method
   */
  configure () {

  }

  /**
   * TODO document method
   */
  initialize () {

  }
  
  /**
   * TODO document method
   */
  sanity () {
  
  }

  constructor (app) {
    super(app, {
      config: require('./config'),
      api: require('./api'),
      pkg: require('./package')
    })
  }
}
```
First, you want to rename `Archetype` by your fabrix ’s name (here `MySpool`). As you can see, you have three methods in it :

- validate
- configure
- initialize
- sanity

WARNING : if you want to create a Web Server or ORM compatible Spool you need to use `spool-webserver` / `spool-datastore` instead of `spool`. For more information see those repo : [spool-webserver](https://github.com/fabrix-app/spool-webserver) and [spool-datastore](https://github.com/fabrix-app/spool-datastore)

#### Validate
Validate function is the first called. As it’s named, you can check if the configuration of your spool is correct (if it’s needed). For example, the hapi spool checks if web server configuration under `config/web.js` is correct. In this tutorial we'll just add some log.

```
validate () {
	this.app.config.log.info('My Spool is valid')
	return Promise.resolve()
}
```
At last, we call `Promise.resolve()` to tell Fabrix our Spool is valid, if it's not we can call `Promise.reject()`

#### Configure
Next come the configure function, this method is called after all events waiting in your Spool (see Configuration section below).
You can configure your Spool removing the hapi Spool. Here, it’s created the web server and configured all routes bind controllers and policies...

```
configure () {
	this.app.log.info('My Spool is configured')
	return Promise.resolve()
}
```
At last, we call `Promise.resolve()` to tell Fabrix our Spool is valid, if it's not we can call `Promise.reject()`

#### Initialize
Initialize is the final method called. Here, you must start your Spool (to do the job it was first here for ^^), hapi launches the web server on host and port configured on `configure` function.

```
initialize () {
	this.app.log.info('My Spool is initialized')
	return Promise.resolve()
}
```
At last, we call `Promise.resolve()` to tell Fabrix our Spool is valid, if it's not we can call `Promise.reject()`

We understood how to interact with a Fabrix server, let see now how to run and test our new Spool.

## Run and test
We wrote our Spool and that's nice but... How can I check if it's working !?

To reach this goal, you’ll need a Fabrix project and some npm commands :)

First, link your entire Spool with this command :

```
cd mySpool;
npm link;
```

Then, go to your Fabrix project and install your Spool :
```
cd myfabrix;
npm link mySpool;
```

All you need to do now, in order to run it, is to edit `config/main.ts` your Fabrix project and add your spool in `spools` :

```js
/**
 * Spool Configuration
 * (app.config.main)
 *
 * @see http://fabrix.app/doc/config/main
 */
export const main = {

  /**
   * Order *sort of* matters. Each module is loaded according to its own
   * requirements, but the order in which they are loaded is the order that conflicting
   * names are merged.
   */
  spools: [
    require('@fabrix/spool-repl').REPLSpool,
    require('@fabrix/spool-router').RouterSpool,
    require('@fabrix/spool-hapi').HapiSpool,
    require('spool-mySpool').MySpool
  ]
}

```

Start your server with `npm start` and you should see if your spool is executed :

![Spool logs](/assets/img/tutoYoSpool3.png)

Now you are ready to create some new cool Spools! Make sure to check NPM before you begin writing, there may already be a spool that does exactly what you want.
