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


### Dependency injection


### Registries


### Saved Searches


