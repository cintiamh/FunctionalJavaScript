# Implementing and Testing Applications using Functional Javascript

1. [Functors in Javascript](#functors-in-javascript)
2. [Monads](#monads)
3. [Escaping the Pyramid of Doom](#escaping-the-pyramid-of-doom)

## Functors in Javascript

### Function Composition

```
compose(f, g)(x) = f(g(x))
```

Where `f` and `g` are unary function, which means each one has only one argument.

Example:
```javascript
compose = (f, g) => x => f(g(x))
const double = x => 2 * x;
const plusone = x => x + 1;
compose(double, plusone)(5)
// 12
```

Notice that the compose functions arguments are executed from right to left.

### Categories

A category is:
* A set of JavaScript values
  * Booleans (false, true)
  * Lamp states ("red", "yellow", "green")
* A set of morphisms (functions) defined on these values
  * Identity transformation
    * `const identity = x => x;`
  * Associativity
    * `compose( compose( f, g ), h ) = compose(f, compose(g, h));`

### Functors

* Mappings between categories
* Example: Map function of arrays
* Arrays implementing the map function are functors
* Containers: Arrays
* Morphisms - Square function
  * `[1, 2, 3, 4].map(x => x * x)` yields `[1, 4, 9, 16]`
  * `[1, 2, 3, 4]` and `[1, 4, 9, 16]` are functors.

#### Properties of Functors

* Functors lift values from their container
* The values can be of any type
* Apply a callback function on each value
* The morphed values are placed in a new container
* The new and old containers match in structure

## Monads

### What are Monads?

* Monads are containers that implements:
  * A unit method: `unit(value): monad` - it builds a monad from a value.
  * A `flatMap` method. It's like `map` but flattens the returned value.

Example using Underscore:
```javascript
_.map(['understanding', 'monads'], s => [...s])
// returns two arrays of arrays
_.flatten(_.map(['understanding', 'monads'], s => [...s]))
// returns a single array with all characters in it
_.flatMap(['understanding', 'monads'], s => [...s])
// returns the same as above.
```

### Axioms of Monads

* `flatMap(monad, unit) = monad`
* `flatMap(unit(value), f) = f(value)`
* `flatMap(flatMap(monad, f), g) = flatMap(monad, value => flatMap(f(value), g))`

Check Monet.js

## Asynchronous Code and Promises in Depth

### Escaping the Pyramid of Doom

Also known as "Callback hell".

Callbacks inside callbacks.

```javascript
higherOrderFunction1( ( ...callbackArgs1 ) => {
  higherOrderFunction2(( ...callbackArgs2) => {
    higherOrderFunction3(( ...callbackArgs3) => {
      // ...
    })
  })
})
```

Symptom: "Callback hell"
Root cause: Handling asynchronous code.

### ES6 Promises and Functional Programming

#### Promises

Describe the eventual result of an asynchronous operation.

As a kid I used to ask my parents for ice cream when there was no ice cream shop around.
My parents would usually promise me, that as soon as we saw a shop, I would get an ice cream.
Until we found an ice cream shop, I would be left with that promise; as soon as I got the ice cream the promise would be 'resolved' and I would say "thank you".
If we didn't end up finding an ice cream shop I would usually fail gracefully by demanding cake.

```javascript
const giveMeIceCream = new Promise((resolve, reject) => {
  // ...
  if (iceCreamIsFound) {
    resolve(iceCream);
  }
  // ...
  reject(noShopWasFound);
});

giveMeIceCream.then(onFulfilled, onRejected);
giveMeIceCream.catch(onRejected);
```

#### Handling multiple promises

* `Promise.all`

```javascript
Promise.all([firstPromise, secondPromise])
  .then(onAllPromisesAreFulfilled)
  .catch(onAnyPromiseIsRejected);
```

Example:

Callbacks inside callbacks
```javascript
higherOrderFunction1((...callbackArgs1) => {
  higherOrderFunction2((...callbackArgs2) => {
    higherOrderFunction3((...callbackArgs3) => {
      // ...
    })
  })
})
```

To Promises
```javascript
const asyncPromise1 = new Promise((resolve, reject) => {
  resolve(callbackArg1);
}).then(callbackArg1 => {
  const asyncPromise2 = new Promise((resolve, reject) => {
    resolve(callbackArg2);
  });
  return asyncPromise2;
}).then(callbackArg2 => {
  const asyncPromise3 = new Promise((resolve, reject) => {
    resolve(callbackArg3);
  });
  return asyncPromise3;
}).then(callbackArg3 => {

});
```

### Asynchronous Functional Programming with ES6 Generator Functions
