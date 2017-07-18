# Implementing and Testing Applications using Functional Javascript

1. [Functors in Javascript](#functors-in-javascript)
2. [Monads](#monads)
3. [Escaping the Pyramid of Doom](#escaping-the-pyramid-of-doom)
4. [ES6 Promises and Functional Programming](#es6-promises-and-functional-programming)
5. [Asynchronous Functional Programming with ES6 Generator Functions](#asynchronous-functional-programming-with-es6-generator-functions)
6. [Functional Programming with Async-Await](#functional-programming-with-async-await)
7. [Lazy Evaluation](#lazy-evaluation)
8. [Using ES6 Generator Functions for Evaluating Lazy Sequences](#using-es6-generator-functions-for-evaluating-lazy-sequences)

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

Iterables and Iterators
* Data source: array and string
* Data consumer: spread operator
* Iterate Object

#### Generator functions

`const generatorFunction = function *() { ... }`

It returns an iterator which is also an iterable object.

```javascript
const iterator = generatorFunction();
iterator.next(); // acting as an iterator
[...iterator]; // acting as an iterable object
```

`yield` returns the next value of the iteration and pauses execution of the generator function.

`return` terminates the generator function.

```javascript
const oneTwoGenerator = function *() {
  yield 1; yield 2; return;
}
```

Example:
```javascript
const daysGenerator = function *() {
  yield 'Sunday';
  yield 'Monday';
  yield 'Tuesday';
  yield 'Wednesday';
  yield 'Thursday';
  yield 'Friday';
  yield 'Saturday';
  return 'We are done';
}

const daysIterator = daysGenerator();

console.log(daysIterator.next());
console.log(daysIterator.next());
console.log(daysIterator.next());
console.log(daysIterator.next());
console.log(daysIterator.next());
console.log(daysIterator.next());
console.log(daysIterator.next());
console.log(daysIterator.next());
```

Outputs:
```javascript
Object {value: "Sunday", done: false}
Object {value: "Monday", done: false}
Object {value: "Tuesday", done: false}
Object {value: "Wednesday", done: false}
Object {value: "Thursday", done: false}
Object {value: "Friday", done: false}
Object {value: "Saturday", done: false}
Object {value: "We are done", done: true}
```

Note that the last line has `done` as `true`.

Re-implementing from Promises using Generators:

Promises:
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

Generators:
```javascript
const asyncSequence = function *() {
  const asyncPromise1 = new Promise((resolve, reject) => {
    resolve(callbackArg1);
  });
  const callbackArg1 = yield asyncPromise1;
  const asyncPromise2 = new Promise((resolve, reject) => {
    resolve(callbackArg2);
  });
  const callbackArg2 = yield asyncPromise2;
  const asyncPromise3 = new Promise((resolve, reject) => {
    resolve(callbackArg3);
  });
  const callbackArg3 = yield asyncPromise3;
}

const asyncIterator = asyncSequence();

asyncIterator.next().value
  .then(callbackArg1 => asyncIterator.next(callbackArg1).value)
  .then(callbackArg2 => asyncIterator.next(callbackArg2).value)
  .then(callbackArg3 => asyncIterator.next(callbackArg3).value)
```

### Functional Programming with Async-Await

#### Async Operator

`async` keyword denotes asynchronous functions

```javascript
const f = async function(...args){ ... }
const f = async (...args) => { ... }
async function f(...args) { ... }
```

Asynchronous functions return a promise.

Returned values are wrapped in a resolved promise

```javascript
const fiveResolved = async function() {
  return 5;
};
fiveResolved().then(v => console.log(v));
```

Errors are wrapped in a rejected promise.

```javascript
const fiveRejected = async function() {
  throw new Error('Cannot compute five');
};
fiveRejected().catch(e => console.log(e.message));
```

#### Await Operator

* Can only be used inside async functions
* `await promise;`
* Blocks execution of the function until its operand is resolved or rejected
* Resolved promise
  * `const value = await Promise.resolve(5);`
* Rejected promise
  * `await fiveRejected; // throws an error`

Example

```javascript
const isLoggedIn = async () => true;
const getTopTenGoogleBooks = async x => x;
const renderBooks = x => {
  console.log(`${x} has been rendered.`);
};

const populateBooks = async user => {
  const isLogged = await isLoggedIn(user);
  if (isLogged) {
    // Parallel async calls
    const [books1, books2] = await Promise.all([
      getTopTenGoogleBooks('JavaScript'),
      getTopTenGoogleBooks('PHP')
    ]);
    renderBooks(books1);
    renderBooks(books2);
  }
}

populateBooks('Zsolt').then(() => console.log('done'));
```

Re-implementing a chain of Promises.

```javascript
const asyncSequence = async function() {
  const asyncPromise1 = new Promise((resolve, reject) => {
    setTimeout(() => { resolve('1'); }, 1000);
  });
  const callbackArg1 = await asyncPromise1;
  console.log('callbackArg1', callbackArg1);
  const asyncPromise2 = new Promise((resolve, reject) => {
    setTimeout(() => { resolve('2'); }, 1000);
  });
  const callbackArg2 = await asyncPromise2;
  console.log('callbackArg2', callbackArg2);
  const asyncPromise3 = new Promise((resolve, reject) => {
    setTimeout(() => { resolve('3'); }, 1000);
  });
  const callbackArg3 = await asyncPromise3;
  console.log('callbackArg3', callbackArg3);
};

asyncSequence()
  .then(() => console.log('done'));
```

## Lazy Evaluation

### What is Lazy Evaluation?

* Eager or Greedy Evaluation - Immediate
* Lazy Evaluation - Delayed, on demand

Examples:

Eager Evaluation:
```javascript
[1, 2, 3, 4, 5].map(x => 2 * x)[0]
[2, 4, 6, 8, 10][0]
2
```

Lazy Evaluation:
```javascript
lazyMap([1, 2, 3, 4, 5], x => 2 * x).get(0)
(x => 2 * x)([1, 2, 3, 4, 5][0])
(x => 2 * x)(1)
2
```

```javascript
const lazyMap = (arr, mapFunction) => {
  return {
    get: function(index) {
      return mapFunction(arr[index]);
    },
    take: function(n) {
      return arr.slice(0, n).map(mapFunction);
    },
    value: function() {
      return arr.map(mapFunction)
    }
  }
}
```

With this approach, you might need to re-calculate the values multiple times.

You can use Memoization in order to avoid this.

```javascript
const memo = f => {
  // create a lookup table
  let memoMap = new Map();
  return fArg =>
  memoMap.has(fArg) ?
  memoMap.get(fArg) :
  memoMap.set(fArg, f(fArg)).get(fArg);
}
```

Memoization with lazy evaluation:
```javascript
const lazyMemoMap = (arr, mapFunction) => {
  const memo = [];
  return {
    get: function(index) {
      if (memo[index]) return memo[index];
      const result = mapFunction(arr[index]);
      memo[index] = result;
      return result;
    }
  }
}
```

Advantages and disadvantages:

* Eager Evaluation
  * Faster when all elements are needed
  * May be a lot slower when just some elements are needed
  * Calculates all values once
  * Tends to perform calculations that are thrown away
  * Requires more space in edge cases
  * Only works with finite sequences
* Lazy Evaluation
  * Slower when all elements are needed
  * A lot faster when just some elements are needed
  * May use memoization to avoid calculating the same values multiple times.
  * Only calculates what's needed
  * Requires less space in edge cases
  * Works with finite and infinite sequences

### Using ES6 Generator Functions for Evaluating Lazy Sequences

#### Lazy Filter

```javascript
const lazyFilter = function *(iterable, filterFunction) {
  for (let elem of iterable) {
    if (filterFunction(elem)) yield elem;
  }
}

const lazySequence = lazyFilter([1, 2, 3, 4, 5], x => x % 2 === 0)

lazySequence.next().value;
// 2
lazySequence.next().value;
// 4
lazySequence.next();
// Object {value: undefined, done: true}
```

Restrictions of Lazily Evaluated Sequences with Generator Functions:

* Elements can only be accessed sequentially
* Elements can only be retrieved once
* Infinite sequences are not exhaustively enumerable
