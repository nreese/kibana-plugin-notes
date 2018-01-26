# Kibana knowledge share
This is a collection of knowledge that will make Kibana code and plugin development a little bit easier.

**Note:** Kibana is constantly changing and getting better. This document is targeted at the 6.0 baseline.

## Complete understanding of Javascript

### Functions are objects
Functions are objects and can have properties.

```
const myFunc = function(a, b) {
  return a + b;
}
myFunc.prop1 = 'someValue';
myFunc.prop2 = function () {
  return 'a function property of a function, my head hurts';
}

console.log( myFunc(1,2) ) // 3
console.log( myFunc.prop1 ) // someValue
console.log( myFunc.prop2() ) // a function property of a function, my head hurts
```

## ECMAScript 6 and TC-39
Kibana uses the lastest Javascript. Getting familiar with newer language features helps a lot.

### [Destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
Concise way of unpacking values from arrays, or properties from objects, into distinct variables.

```
const myObj = {
  prop1: 'someValue',
  prop2: 'anotherValue'
};
```

```
// Old way
const prop1 = myObj.prop1;
const prop2 = myObj.prop2;
```

```
//Destructuring assignment
{ prop1, prop2 } = myObj;
```

[Kibana Example](https://github.com/elastic/kibana/blob/master/src/core_plugins/kibana/public/home/components/home.js#L14)

### [Arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
Shorter syntax, does not have its own `this`

```
const self = this;
// function syntax
function(arg1, arg2) {
  self.sum = arg1 + arg2;
}
```

```
// Arrow functions
(arg1, arg2) => {
  this.sum = arg1 + arg2;
}
```

#### Arrow function concise body
```
// block body
(x, y) => { 
  return x + y; 
}; 
```

```
// concise body
(x, y) => x + y; 
```

### [Array iteration methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)
Provide a declaritive syntax for collection iteration

```
// imperative iteration
const myArray = [1,2,3];
const mySquaredArray = [];
for (let i=0, i<) {
  mySquaredArray[i] = myArray[i] * myArray[i];
}
```

```
// Declaritive iteration
const myArray = [1,2,3];
const mySquaredArray = myArray.map((arrayElement) => {
  return arrayElement * arrayElement;
});
```

[Kibana Example](https://github.com/elastic/kibana/blob/master/src/core_plugins/kibana/public/home/components/home.js#L35)


### [Async functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
An async function returns a `Promise` when called. The `Promise` will be resolved when the async function returns a value.


### [Spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator)

```
const insertAtIndex = function(origArray, index, newItem) {
  const newArray = [];
  newArray.concat(origArray.slice(0, index));
  newArray.push(newItem);
  newArray.concat(origArray.slice(index + 1))
  return newArray;
}

```

```
// spread operators + arrow functon with concise body
const insertAtIndex = (origArray, index, newItem) => [
  ...origArray.slice(0, index),
  newItem,
  ...origArray.slice(index + 1)
];
```


## Kibana idiosyncrasies

### Build process
Understanding the build process makes it easier to find things in source code 

#### babel
JavaScript transpiler that converts new JavaScript syntax into plain old ES5 JavaScript that can run in any browser (even the old ones).

```
// Actuall code
getIndexPatterns = async (search) => {
  const resp = await this.props.scope.vis.API.savedObjectsClient.find({
    type: 'index-pattern',
    fields: ['title'],
    search: `${search}*`,
    search_fields: ['title'],
    perPage: 100
  });
  return resp.savedObjects;
}
```

```
// What's running in browser
_this.getIndexPatterns = function () {
  var _ref2 = _asyncToGenerator( /*#__PURE__*/regeneratorRuntime.mark(function _callee(search) {
    var resp;
    return regeneratorRuntime.wrap(function _callee$(_context) {
      while (1) {
        switch (_context.prev = _context.next) {
          case 0:
            _context.next = 2;
            return _this.props.scope.vis.API.savedObjectsClient.find({
              type: 'index-pattern',
              fields: ['title'],
              search: search + '*',
              search_fields: ['title'],
              perPage: 100
            });

          case 2:
            resp = _context.sent;
            return _context.abrupt('return', resp.savedObjects);

          case 4:
          case 'end':
            return _context.stop();
        }
      }
    }, _callee, _this2);
  }));

  return function (_x) {
    return _ref2.apply(this, arguments);
  };
}
```

#### webpack
Module bundler. Builds `KIBANA_HOME/optimize/bundles/kibana.bundle.js` from `KIBANA_HOME/src/`

[webpackShims/leaflet.js](https://github.com/elastic/kibana/blob/6.2/webpackShims/leaflet.js)
```
require('../node_modules/leaflet/dist/leaflet.css');
window.L = module.exports = require('../node_modules/leaflet/dist/leaflet');
window.L.Browser.touch = false;
window.L.Browser.pointer = false;

require('../node_modules/leaflet.heat/dist/leaflet-heat.js');

require('../node_modules/leaflet-draw/dist/leaflet.draw.css');
require('../node_modules/leaflet-draw/dist/leaflet.draw.js');

require('../node_modules/leaflet-responsive-popup/leaflet.responsive.popup.css');
require('../node_modules/leaflet-responsive-popup/leaflet.responsive.popup.js');
```

### Dependency injection


### [Indexed Array](https://github.com/elastic/kibana/blob/6.0/src/ui/public/indexed_array/indexed_array.js#L24)
An array with some special methods added to make searching easy.

```
// this is generally a data-structure that IndexedArray is good for managing
const users = [
  { name: 'John', id: 69, username: 'beast', group: 'admins' },
  { name: 'Anon', id: 0, username: 'shhhh', group: 'secret' },
  { name: 'Fern', id: 42, username: 'kitty', group: 'editor' },
  { name: 'Mary', id: 55, username: 'sheep', group: 'editor' }
];

const indexedArray = new IndexedArray({
  index: ['username'],
  group: ['group'],
  order: ['id'],
  initialSet: users
});

const usersJohn = indexedArray.byUsername('beast');
```

### Registries
Registries are a function with a `register` property.
* Calling `register`, adds a `moduleProvider` to an array. `moduleProvider` is a function that returns a module.
* Calling the function itself
  * Calls each moduleProvider (with injected dependencies)
  * Each module is added to an Indexed Array.
  * returns Indexed Array.

[uiRegistry](https://github.com/elastic/kibana/blob/6.0/src/ui/public/registry/_registry.js#L48)
```
export function uiRegistry(spec) {
  spec = spec || {};

  const constructor = _.has(spec, 'constructor') && spec.constructor;
  const invokeProviders = _.has(spec, 'invokeProviders') && spec.invokeProviders;
  const iaOpts = _.defaults(_.pick(spec, IndexedArray.OPT_NAMES), { index: ['name'] });
  const props = _.omit(spec, notPropsOptNames);
  const providers = [];

  const registry = function (Private, $injector) {
    // call the registered providers to get their values
    iaOpts.initialSet = invokeProviders
      ? $injector.invoke(invokeProviders, undefined, { providers })
      : providers.map(Private);

    // index all of the modules
    let modules = new IndexedArray(iaOpts);

    // mixin other props
    _.assign(modules, props);

    // construct
    if (constructor) {
      modules = $injector.invoke(constructor, modules) || modules;
    }

    return modules;
  };

  registry.displayName = '[registry ' + props.name + ']';

  registry.register = function (privateModule) {
    providers.push(privateModule);
    return registry;
  };

  return registry;
}
```


[vis_types.js](https://github.com/elastic/kibana/blob/6.0/src/ui/public/registry/vis_types.js)
```
import { uiRegistry } from 'ui/registry/_registry';

export const VisTypesRegistryProvider = uiRegistry({
  name: 'visTypes',
  index: ['name'],
  order: ['title']
});
```

Example of registering new module [timeline/public/vis/index.js](https://github.com/elastic/kibana/blob/6.0/src/core_plugins/timelion/public/vis/index.js#L18)
```
import { VisTypesRegistryProvider } from 'ui/registry/vis_types';

VisTypesRegistryProvider.register(TimelionVisProvider);

function TimelionVisProvider(Private) {
  const VisFactory = Private(VisFactoryProvider);
  const timelionRequestHandler = Private(TimelionRequestHandlerProvider);

  // return the visType object, which kibana will use to display and configure new
  // Vis object of this type.
  return VisFactory.createAngularVisualization({
    name: 'timelion',
    title: 'Timelion',
    image,
    description: 'Build time-series using functional expressions',
    category: CATEGORY.TIME,
    visConfig: {
      defaults: {
        expression: '.es(*)',
        interval: 'auto'
      },
      template: visConfigTemplate,
    },
    editorConfig: {
      optionsTemplate: editorConfigTemplate,
      defaultSize: DefaultEditorSize.MEDIUM,
    },
    requestHandler: timelionRequestHandler.handler,
    responseHandler: 'none',
    options: {
      showIndexSelection: false
    }
  });
}
```

Example of using the registry in [vis.js](https://github.com/elastic/kibana/blob/6.0/src/ui/public/vis/vis.js#L13)
```
import { VisTypesRegistryProvider } from 'ui/registry/vis_types';

const visTypes = Private(VisTypesRegistryProvider);

this.type = visTypes.byName[type];
```


### Saved Searches


## UI framework
Re-usable [UI components](https://github.com/elastic/kibana/tree/master/ui_framework). **Warning:** This has been deprecated in 6.2 and will be replaced by [Elastic UI Framework](https://github.com/elastic/eui)


## Plugin examples
6.0 resources
* [Developing Kibana Visualizations - video](https://www.elastic.co/webinars/creating-custom-kibana-visualizations)
* [Developing Visualizations - kibana documentation](https://www.elastic.co/guide/en/kibana/6.0/development-visualize-index.html)
* [Developing new Kibana visualizations - blog](https://www.elastic.co/blog/developing-new-kibana-visualizations)
* Not a plugin link but found this blog post about [custom region maps](https://www.elastic.co/blog/custom-region-maps-in-kibana-6-0) that I think you would be interested in

The best way to build plugins is to look at working examples. Kibana uses its own plugin system so there are lots of great examples in the code base.

### New REST endpoint
Specify [init](https://github.com/elastic/kibana/blob/6.0/src/core_plugins/timelion/index.js#L87) element in plugin definition.

[init](https://github.com/elastic/kibana/blob/6.0/src/core_plugins/timelion/init.js#L4) is a function that gets pass `hapi` server object when called.

Add enpoint by [adding route to server](https://github.com/elastic/kibana/blob/6.0/src/core_plugins/timelion/server/routes/run.js#L15)

```
// Plugin 
export default function (kibana) {
  return new kibana.Plugin({
    require: ['kibana', 'elasticsearch'],
    uiExports: {},
    init: function (server) {
      server.route({
        method: ['POST', 'GET'],
        path: '/api/timelion/run',
        handler: async (request, reply) => {
          // do stuff
          const results = [];
          reply(results);
        }
      });
    }
  });
}
```


### New config value

### New kibana.yml value

### 


