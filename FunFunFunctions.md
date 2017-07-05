# Fun Fun Functions - Functional programming in JavaScript

1. [Higher-order Functions](#higher-order-functions)

## Higher-order Functions

Why Functional Programming?
* Less bugs: Easier to reason about
* Less time: Re-use more code

1. Functions are values.

```javascript
var triple = function(x) {
  return x * 3;
}

var waffle = triple;

waffle(30); // 90
```

Functions can be assigned to variables, or passed into other functions, higher-order functions.

What are higher-order functions good for?

* Composition: multiple smaller functions into a bigger function.

Example:
```javascript
var animals = [
  { name: 'Fluffykins', species: 'rabbit' },
  { name: 'Caro', species: 'dog' },
  { name: 'Hamilton', species: 'dog' },
  { name: 'Harold', species: 'fish' },
  { name: 'Ursula', species: 'cat' },
  { name: 'Jimmy', species: 'fish' },
]
```

Iterating with a regular for loop:
```javascript
var dogs = [];
for (var i = 0; i < animals.length; i++) {
  if (animals[i].species === 'dog') {
    dogs.push(animals[i]);
  }
}
```

Using the `filter` method with a callback function:
```javascript
var dogs = animals.filter(function(animal) {
  return animal.species === 'dog';
});
```

## Map
