# grunt-angular-service
> Convert libraries to Angular services as part of your workflow.

`grunt-angular-service` wraps JavaScript code in calls to one of Angular's various
provider methods (`factory`, `service`, `constant`, `value`),
thereby creating code you can inject via Angular's dependency
injection.


## Getting Started
This plugin requires Grunt `~0.4.1`

If you haven't used [Grunt](http://gruntjs.com/) before, be sure to check out
the [Getting Started](http://gruntjs.com/getting-started) guide, as it explains how
to create a [Gruntfile](http://gruntjs.com/sample-gruntfile) as well as install and use
Grunt plugins. Once you're familiar with that process, you may install this plugin
with this command:

```shell
npm install grunt-angular-service --save-dev
```

Once the plugin has been installed, it may be enabled inside your Gruntfile with this
line of JavaScript:

```js
grunt.loadNpmTasks('grunt-angular-service');
```

## The "ngservice" task


### How it Works
  1. You provide `ngservice` a target library. This is the library whose
  functionality you want to inject using Angular dependency injection.
  2. `Ngservice` executes the target library's code.
  3. Using the `exportStrategy` option you set in your `ngservice`
  grunt task, `ngservice` determines what to return from the `factory`,
  `service`, `constant`, or `value` provider it creates for you.

### Use Cases
  1. You aren't using a script loader, and want angular style DI for all your
  non-Angular libraries.
  2. You have a node library you want to use client side with angular.

There are probably many more use cases. If any come to mind, please share.

## Overview
In your project's Gruntfile, add a section named `ngservice` to the data object passed into `grunt.initConfig()`.

```js
grunt.initConfig({
  ngservice: {
    name: 'myLibService',    // First argument passed to angular.factory(), angular.service(), etc.
    module: 'myLibModule',     // Name of Module the service is being added to.
    defineModule: true,        // Define a new module?
    files: {
        'my_library_as_angular_service.js': 'my_library.js'
    }
  },
})
```

Will produce the file `my_library_as_angular_service.js`:

```js
angular.module('myLibModule', []).factory('myLibAsService', function() {
  // Your library here.
});
```

## Settings

### name
Type: `String`

The name of the service being created.

### module
Type: `String`

The name of the module to which the service is added.

### defineModule
Type: `Boolean` Default Value: `false`

Whether to define the module.

### moduleDependencies
Type: `Array` Default Value: `None`

A list of module names to pass as dependencies to the newly created module. Only
applies when *defining* the module (`defineModule` must be set to `true`).

```javascript
angular.module('myModule', ['dependency1', 'dependency2'].factory(...);
```

### dependencies
Type: `Array` Default Value: `None`

A list of dependencies to be injected using Angular dependency injection. The target
library will have access to each dependency via `this`.

```javascript
// grunt-angular-service will produce the code below.
angular.module('myModule').factory(['dep1', 'dep2', function(dep1, dep2){ ... }]);

// In the target library, the dependency can be accessed via `this` like so:
this.dep1;
this.dep2;
```

### choose
Type: `String`

Declare a specific property to export. Use this if your library exports multiple
properties and you're only interested in one of them.

### exportStrategy
> Determines how to map the target library's output to the angular service.

Type: `String` Default: `default` Allowed: `default`, `context`, `exports`, `value`

There are many ways in which a JavaScript may expose its API. It may add a property
onto `window`. It may add a property onto `this`. It may use `amd`. It may use
`module.exports`, etc. By default, `ngservice` attempts to make an educated guess
as to how the target exposes its API. However, the educated case my not work for all
libraries.

* `default`: The default `exportStrategy` is as follows:
  1. If `choose` was passed and module.exports\[`choose`\] exists, return it.
  2. If `choose` was passed and the library added the property `this`\[`choose`\], then return `this`\[`choose`\].
  3. If `choose` was passed, and the library is an JS object containing the `choose` property, return the value of the property.
  4. If `choose` was passed but checks 1-3 failed, return undefined.
  5. If returnValue is not null or undefined, return it.
  6. If module.exports has a single property, return the value of that property.
  7. If module.exports has more than a single property, return module.exports.
  8. If context has a single property, return the value of the single property.
  9. If context has more than a single property, return context.
  10. Return undefined.
* `context`: If the target JS code adds a property onto the context within which it
executes (its `this` value), then set this value. If the target code adds more
than one property onto its context, and you only care to retrieve one of them, use
the `choose` setting.
* `value`: If the target "library" is a value such as an object literal, a string, or
a number, use this option.
* `exports`: If the target library uses node style `module.exports`, use this option.
If the target library adds more than one property onto `module.exports` and you only
care to retrieve one of these properties, use the `choose` option.


## Usage Examples

### Generate a Angular Service from an Existing JavaScript Library
In this example, underscore.js is ported to an angular service.
```js
grunt.initConfig({
  ngservice: {
    module: "myModule",
    name: "_",
    files: {
      'services/underscore_service.js': 'path/to/underscore.js',
    },
  },
})
```

### Generate a Service From an Existing JavaScript Library that has Dependencies
In this contrived example, underscore.js and Backbone.js are both converted to angular services, with the former provided as a dependency to the latter.

```js
grunt.initConfig({
  ngservice: {
    underscore: {
      name: '_',
      module: 'myModule',
      files: {
        'services/underscore_service.js': 'path/to/underscore.js',
      }
    },
    backbone: {
      name: 'Backbone',
      module: 'myModule',
      dependencies: ['_'],
      files: {
        'services/backbone_service.js': 'path/to/backbone.js',
      }
    }
  }
})
```

## Contributing
In lieu of a formal styleguide, take care to maintain the existing coding style. Add unit tests for any new or changed functionality. Lint and test your code using [Grunt](http://gruntjs.com/).

## Release History
_(Nothing yet)_

## License
The MIT License

Copyright (c) 2013 Orr Bibring

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
