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


### Registries


### Saved Searches


## Re-usable UI components

### UI framework


## Plugin examples


