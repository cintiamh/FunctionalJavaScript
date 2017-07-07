# Fun Fun Functions - Functional programming in JavaScript

1. [Higher-order Functions](#higher-order-functions)
2. [Map](#map)
3. [Reduce basics](#reduce-basics)
4. [Reduce Advanced](#reduce-advanced)
5. [Closures](#closures)
6. [Currying](#currying)
7. [Recursion](#recursion)
8. [Promises](#promises)
9. [Functors](#functors)

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
  // Returns true or false value for verification.
  return animal.species === 'dog';
});
```

## Map

Map is another higher-order function, just like `Array.filter`.

The difference is that Map doesn't just use the values in the array. Map transforms all values in the Array.

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

Getting an array with all the names:

Example with for loop:
```javascript
var names = [];
for (var i = 0; i < animals.length; i++) {
  names.push(animals[i].name);
}
```

Using `map`:
```javascript
var names = animals.map(function(animal) {
  // returns a transformed object to replace the original object.
  return animal.name;
});
```

## Reduce basics

Previous examples:
* `Array.map()`: transforms an array of things and returns an array of same size with transformed items.
* `Array.filter()`: gets a smaller array from the bigger array.
* `Array.find()`: gets a single element from the bigger array (first match).

You can create your own list transformation using `Array.reduce()`.

Example:
```javascript
var orders = [
  { amount: 250 },
  { amount: 400 },
  { amount: 100 },
  { amount: 325 },
];
```

For loop example:
```javascript
var totalAmount = 0;
for (var i = 0; i < orders.length; i++) {
  totalAmount += orders[i].amount;
}
```

Using reduce:
```javascript
// The callback has 2 arguments, the accumulator and the current item in the array.
var totalAmount = orders.reduce(function(sum, order) {
  return sum + order.amount;
  // The following 0 is the initial state object.
  // This will be passed on the first call of the callback.
}, 0);
```

## Reduce Advanced

Example: You are getting data from a txt file with data separated with tabs into a JSON format.

data.txt file:
```
mark johansson  waffle iron  80  2
mark johansson  blender 200 1
mark johansson  knife 10  4
Nikita Smith  waffle iron 80  1
Nikita Smith  knife 10  2
Nikita Smith  port  20  3
```

Our code would look like this:
```javascript
import fs from 'fs';

var output = fs.readFileSync('data.txt', 'utf8')
  // Removes empty lines and spaces
  .trim()
  // Splits into an array of lines
  .split('\n')
  // Splits each line into an array separated by tabs
  .map(line => line.split('\t'))
  // Iterates over the resulting array of arrays of information from the text file.
  .reduce((customers, line) => {
    customers[line[0]] = customers[line[0]] || [];
    customers[line[0]].push({
      name: line[1],
      price: line[2],
      quantity: line[3]
    });
    return customers;
  }, {})
```

Now we have the desired output:
```javascript
{
  "mark johansson": [
    {
      name: 'waffle iron',
      price: 80,
      quantity: 2
    },
    {
      name: 'blender',
      price: 200,
      quantity: 1
    },
    ...
  ],
  "Nikita Smith": [
    ...
  ]
}
```

## Closures

The function body has access to variables that are defined outside of it.

```javascript
var me = 'Bruce Wayne';
function greetMe() {
  console.log('Hello, ' + me + '!');
}
me = 'Batman';
greetMe();
// Hello, Batman!
```

It gets the current value of the variable from when it's run.
It doesn't copy the variable value from when it was defined.

### Why use Closures?

```javascript
function sendRequest() {
  var requestID = '123';
  $.ajax({
    url: '/myUrl',
    success: function(response) {
      alert('Request ' + requestID + ' returned');
    }
  });
}
```

The ajax call is asynchronous, so even after a while it will have access to the requestID.

If there was no such thing as Closure, we probably would have to have an Object instance holding all the info about the request.

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures

## Currying

Currying is when a function doesn't take all its arguments upfront.
Instead, it wants you to give it the first argument, and then the function returns another function, which then you are supposed to call with a second argument, which then will return a new function, with a third argument, and so on until all the arguments are provided. And then the last function at the chain is supposed to return the final value you are looking for.

Example (no currying):
```javascript
let dragon = (name, size, element) =>
  name + ' is a ' +
  size + ' dragon that breathes ' +
  element + '!'
```

Now the same example with currying:
```javascript
let dragon =
  name =>
    size =>
      element =>
        name + ' is a ' +
        size + ' dragon that breathes ' +
        element + '!'

console.log(dragon('fluffykins')('tiny')('lightning'))
```

We can also call this in parts:
```javascript
let fluffykinsDragon = dragon('fluffykins');
let tinyDragon = fluffykinsDragon('tiny');

console.log(tinyDragon('lightning'));
```

Libraries like Lodash has a method like `_.curry(<function>)` that can convert a regular function (like the first example) to work like the curried version of the function.

### Why Currying

Example (no currying):
```javascript
let dragons = [
  { name: 'fluffykins', element: 'lightning' },
  { name: 'noomi', element: 'lightning' },
  { name: 'karo', element: 'fire' },
  { name: 'doomer', element: 'timewarp' }
]

let hasElement = (element, obj) => obj.element === element

let lightningDragons = dragons.filter(x => hasElement('lightning', x));

console.log(lightningDragons)
```

Using lodash's curry:
```javascript
import '_' from 'lodash';

let hasElement = _.curry((element, obj) => obj.element === element)
// hasElement returns a new function that expects the obj to be compared to.
let lightningDragons = dragons.filter(hasElement('lightning'));
```

## Recursion

Recursion is when a function calls itself until it doesn't.

Example:
```javascript
let countDownFrom = (num) => {
  // Stop condition
  if (num === 0) return;
  console.log(num);
  // Calls itself again with next value
  countDownFrom(num - 1);
}
countDownFrom(10);
```

### Why use recursion?

Every time you do a loop, you can use recursion instead.
But this doesn't always work the other way around.

Example:
```javascript
let categories = [
  { id: 'animals', parent: null },
  { id: 'mammals', parent: 'animals' },
  { id: 'cats', parent: 'mammals' },
  { id: 'dogs', parent: 'mammals' },
  { id: 'chihuahua', parent: 'dogs' },
  { id: 'labrador', parent: 'dogs' },
  { id: 'persian', parent: 'cats' },
  { id: 'siamese', parent: 'cats' },
];

// Make a tree structure from this list:
let makeTree = (categories, parent = null) => {
  let node = {};

  categories
    .filter(c => c.parent === parent)
    .forEach(c => node[c.id] = makeTree(categories, c.id));

  return node;
}

console.log(makeTree(categories));
```

## Promises

Functional programming is a way to make your code more composable.

In functional programming we use a lot of callback as a way to given a function, "execute this other function - callback when you're done".

Promises has the same idea, but a lot more powerful, because they are more composable than callbacks.

Callback example:
```javascript
import loadImageCallbacked from './load-image-callbacked';

let addImg = (src) => {
  let imgElement = document.createElement('img');
  imgElement.src = src;
  document.body.appendChild(imgElement);
}

loadImageCallbacked('images/cat1.jpg', (error, img1) => {
  addImg(img1.src);
  loadImageCallbacked('images/cat2.jpg', (error, img2) => {
    addImg(img2.src);
    loadImageCallbacked('images/cat3.jpg', (error, img3) => {
      addImg(img3.src);
    });
  });
});
```

This is not only ugly, but it is NOT running in parallel. Because the next `loadImageCallbacked` will always wait for the previous `addImg` finish before starting.

load-image-callbacked.js
```javascript
function loadImage(url, callback) {
  let image = new Image();
  image.onload = function() {
    callback(null, image);
  }
  image.onerror = function() {
    let message = 'Could not load image at ' + url);
    callback(new Error(message));
  }
  image.src = url;
}
export default loadImage;
```

Promise Example:
```javascript
import loadImagePromised from './load-image-promised';

Promise.all([
  loadImagePromised('images/cat1.jpg'),
  loadImagePromised('images/cat2.jpg'),
  loadImagePromised('images/cat3.jpg'),
]).then((images) => {
  images.forEach(img => addImg(img.src))
}).catch((error) => {
  // handle error here
});
```

load-image-promised.js
```javascript
import 'babelify/polyfill'; // for the Promise

function loadImage(url) {
  return new Promise((resolve, reject) => {
    let image = new Image();

    image.onload = function() {
      resolve(image);
    }
    image.onerror = function() {
      let message = 'Could not load image at ' + url);
      reject(new Error(message));
    }
    image.src = url;
  });
}
export default loadImage;
```

## Functors
