# Getting Started
###### fabrix - UserGuides

You will learn some basic information about fabrix, how to create a fabrix project, and the basic structure of a fabrix project.

This guide assumes that you have previously installed Node.js 4.0.0 (or higher) on your machine. Some knowledge of MVC and Javascript is needed.

## What is Fabrix?

Fabrix is a new Node.js MVC framework written in TypeScript and transpiled to ES2015 (ES6). It's a modular framework which allows you to choose your every day framework(s) and make them work together.

## Installation

Fabrix use a cli to generate a basic project. So if you haven't installed it yet, do it now :

`npm install -g @fabrix/fab-cli`

You are now ready to create your first application! :)

## Create a project

Creating a project is very easy. Just follow this guide:

```
mkdir myproject;
cd myproject;
fab fabrix;
```

You should see something like this :

![.Web server choice](/assets/img/tutoYofabrix1.png)

The first step is to choose the web server. By default it's hapi but you can choose another one if prefer. This choice will affect how you write your `policies` and `controllers`, but we will se this later.

The other steps are for `package.json` information. In order :

* Name of your project, by default the name of the folder, here we set the default to `myproject`
* Description of your project
* Project homepage url, url of your project repository
* Author's Name, you :)
* Author's Email
* Author's Homepage
* Package keywords
* GitHub username or organization
* Your website
* License

After this the generator will create your project structure and install dependancies (`npm install`).

![.Project generated](/assets/img/tutoYofabrix2.png)

## Run a project

Like the yeoman generator instructs, you only need to do :

`node server.js`

Your server should now be running on port 3000, and you should see on your console :

![.Start log](/assets/img/tutoYofabrix3.png)

And if your open http://localhost:3000 you should see :

![.Hello fabrix !](/assets/img/tutoYofabrix4.png)

To stop server you need to `Ctrl+c` twice.

## How to use Yeoman Generators

Fabrix uses the cli to generate scaffolding for new
applications, and to create resources inside the application.

```sh
$ fab --help

Usage:
  fab fabrix

Generators:

  Create New Model
    fab model <model-name>

  Create New Controller
    fab controller <controller-name>

  Create New Policy
    fab policy <policy-name>

  Create New Service
    fab service <service-name>
```

## Project structure
Now your server is running, let see how :)

![.Project structure](/assets/img/tutoYofabrix5.png)

### API
As said earlier, Fabrix is a MVC framework. MVC stands for Model-View-Controller. This is a software design pattern is used to separate an application's concerns:

- Model : The Model represents an object or Javascript POJO (Plain Old JavaScript Object) carrying data. It can also have logic to update controller if its data changes.
- View : The View represents the visualization of the data that model contains.
- Controller : The Controller acts on both model and view. It controls the data flow into model objects and updates the view whenever data changes. It keeps view and model separate.

Here in Fabrix, the files structure is pretty common: you have a folder `api` for all your `controllers`, `models`, `policies` and `services`.

As you can see, the MVC design pattern in Fabrix is slightly different; it adds 2 new layers: `policies` that run before controllers, and `services` who make controllers methods more accessible.  Even more so, you can extend the API to whatever you want by including it in the api manifest.

Let's see how the default file structure looks.

#### Models
Model files depends on your ORM. By default fabrix uses Waterline, so model definition will look like (http://waterline.js.org/docs/models/models.html) :

```js
const Model = require('fabrix-model')
import { FabrixModel as Model } from '@fabrix/fabrix/dist/common'
/**
 * User
 *
 * @description A User model
 */
export class User extends Model {
  static schema() {
    return {
      username: 'string'
    }
  }
}
```

You can create a new model with this command :
`fab model myModelName`

WARNING: if you don't use `fab` to generate your models don't forget to add them under `api/models/index.ts` or it will be ignored.

#### Controllers
If you remember, when setting up our Fabrix app, we chose hapi as our web server, so all controller functions have to look like hapi middleware functions.
Here is what you will have in `ViewController.ts` :
```
import { FabrixController as Controller } from '@fabrix/fabrix/dist/common'
/**
 * @module ViewController
 *
 * @description Default Controller included with a new fabrix app
 * @see {@link http://fabrix.app/doc/api/controllers}
 * @this fabrixApp
 */
export class ViewController extends Controller {
	/**
   *
   */
  helloWorld (request, reply) {
    reply('Hello fabrix !')
  }
}
```
Here is what you should see when you open your browser on http://localhost:3000. But how is this function mapped to route `/` ? You will see this in Config section.

If your controller(s) extends 'fabrix-controller' then your controller(s) methods must have to implement only hapi interface (with `request` and `reply`) and your controllers will work with hapi, express4 or any webserver spool who support standard fabrix controllers.
If your controllers doesn't extends this class like the example above, your controller methods can implement native interface of the choosen webserver (`request`, `reply` for hapi, `req`, `res`, `next` for express4...).

You can create new controllers with this command :
`fab controller myControllerName`

WARNING: if you don't use `fab` to generate your controllers don't forget to add them under `api/controllers/index.ts` or it will be ignored.

#### Services
Services are basically the brains of your project, they look like (`DefaultService.ts`) :
```
import { FabrixService as Service } from '@fabrix/fabrix/dist/common'
import * as _ from 'lodash'

/**
 * @module DefaultService
 *
 * @description Default Service included with a new fabrix app
 * @see {@link http://fabrix.app/doc/api/services}
 * @this fabrixApp
 */
export class DefaultService extends Service {

  /**
   * Return some info about this application
   */
  getApplicationInfo () {
    return {
      app: this.pkg.version,
      node: process.version,
      libs: process.versions,
      spools: _.map(_.omit(this.packs, 'inspect'), pack #> {
        return {
          name: pack.name,
          version: pack.pkg.version
        }
      })
    }
  }
}
```
And the service can be called from any controller like this : `this.app.services.DefaultService.getApplicationInfo()` (see DefaultController.js)

You can create new services with this command :
`fab service myServiceName`

WARNING: if you don't use `fab` to generate your services don't forget to add them under `api/services/index.js` or it will be ignored.

#### Policies
Policies are functions that run before the controllers to check and validate rules, data, or whatever you need.
Like controllers, if you have chosen hapi a policy file should look like :

```js
import { FabrixPolicy as Policy } from '@fabrix/fabrix/dist/common'

/**
 * @module Default
 * @description Test document Policy
 */
export class Default extends Policy {
  info(request, reply) {
    reply()
  }
}
```
But how is this function mapped to a controller? You will see this in Config section.

You can create new policies with this command :
`fab policy myPolicyName`

WARNING: if you don't use `fab` to generate your policies don't forget to add them under `api/policies/index.ts` or it will be ignored.

### Config
The `config` folder contains all the config file for each part of the server. You can change the running port under `config/web.ts`, or the template engine in `config/views.ts`.

- env : Config file that overrides the default per environment, you can change the entire config for your production environment
- database : Information about your database: type, migration strategy, et cetera.
- tapestries : Options for your REST API: add a prefix, enable/disable tapestry, et cetera.
- i18n  : Configuration for multi language support: set default language and translations.
- locales : Folder in which to put all translation files
- log : Logger options for your project. Uses winston by default.
- main : Allow you to enable/disable spools.*
- policies : Allow you to map your policies with controllers.
- routes : Allows you to map your controller's functions with routes.
- session : Options for managing sessions: strategies, cookies, web tokens, et cetera.
- views : Allow you to configure your template engine.
- web : Options for web server, like port.
- webpack : Configuration for building assets, it's a default webpack configuration (http://webpack.github.io/docs/configuration.html)

NOTE: `spools*`: All modules used by fabrix are call Spool. For example to use Hapi as web server, fabrix uses a Spool named `spool-hapi`. Don't hesitate to look on npm to find some cool Spools.

WARNING: if you add some config files don't forget to add them under `config/index.js` or it will be ignored.

You now have the basic knowledge to make a simple fabrix project!

## More?
Did you notice that when you start your project you have `fabrix>`?

Try to write `app`, `app.api.controllers`, `app.api.services` or even `get('/api/v1/default/info')`. Cool isn't it, this is one feature of spool-repl package, that implement the possibility to debug from interactive shell.

### LICENSE

[MIT](LICENSE)
created by @jaumard
