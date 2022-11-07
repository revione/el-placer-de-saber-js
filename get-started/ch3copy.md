# I Know JS: Get Started - 1st Edition

## Chapter 3: The Roots

&nbsp;

## Iteration


JavaScript, as part of ECMAScript 6, supports the iterator pattern with any object that provides a next() method, which returns an object with two specific properties: done and value. Here's an example that shows a reverse array iterator:

```js
function reverseArrayIterator(array) {
  var index = array.length - 1
  return {
    next: () =>
      index >= 0 
        ? { value: array[index--], done: false } 
        : { done: true },
  }
}

const it = reverseArrayIterator(["three", "two", "one"])

// // Test # 0

console.log(it) //-> { next: [Function: next] }
console.log(it.next()) //-> { value: 'one', done: false }

// Test # 1
console.log(it.next().value) //-> 'one'
console.log(it.next().value) //-> 'two'
console.log(it.next().value) //-> 'three'
console.log(`Are you done? ${it.next().done}`) //-> true

// Test # 2
console.log(`
  ${it.next().value} // one
  ${it.next().value} // two
  ${it.next().value} // three 
  ${it.next().value} // undefined
  ${it.next().done}  // true
  ${it.next().done}  // true
`)

// Test # 3

console.log(`
  ${it.next().done}   // false
  ${it.next().value}  // two
  ${it.next().value}  // three
  ${it.next().value}  // undefined
  ${it.next().done}   // true
  ${it.next().done}   // true
`)

// Test # 4

console.log(`
  ${it.next().done}   // false
  ${it.next().value}  // two
  ${it.next().value}  // three
  ${it.next().done}   // true
`)

// Test # 5

console.log(`
  ${it.next().value}  // one
  ${it.next().value}  // two
  ${it.next().value}  // three
  ${it.next().done}   // true
`)

```

it is desirable to provide Iterator semantics on objects so that they can be iterated automatically via for...of loops. 

Some of JavaScript's built-in types such as Array, Map, or Set already define their own iteration behavior. 

The same effect can be achieved by defining an object's meta @@iterator method, also referred to by Symbol.iterator. 
This creates an Iterable object.

Here's an example of a 
range function that generates a list of values starting from start to end, exclusive, 
using a regular for loop to generate the numbers:

```js
function range(start, end) {
  return {
    [Symbol.iterator]() {
      return this;  // #A
    },
    next() {
      return start <= end 
        ? { value: start++, done: false } // #B
        : { done: true, value: end } // #B
    },
  };
}

console.log(range(1,4)) 
// {
//   next: [Function: next],
//   [Symbol(Symbol.iterator)]: [Function: [Symbol.iterator]]
// }

for (number of range(1, 5)) {
    console.log(number); // -> 1, 2, 3, 4, 5
}
```

The iteration mechanism of built-in types, like strings, can also be manipulated:

```js
let iter = ["I", "t", "e", "r", "a", "t", "o", "r"][Symbol.iterator]();

console.log(iter) // Object [Array Iterator] {}
console.log(iter.next()) // { value: 'I', done: false }
console.log(iter.next().value) // t
console.log(iter.next().value) // e
```

### Consuming Iterators

With the ES6 iteration protocol in place, it's workable to consume a data source one value at a time, checking after each `next()` call for `done` to be `true` to stop the iteration. But this approach is rather manual, so ES6 also included several mechanisms (syntax and APIs) for standardized consumption of these iterators.

One such mechanism is the `for..of` loop:

```js
// given an iterator of some data source:
var it = [0, 1, 2, 3]

// loop over its results one at a time
for (let val of it) {
  console.log(`Iterator value: ${val}`)
}
// Iterator value: 0
// Iterator value: 1
// Iterator value: 2
// Iterator value: 3

var vals = [...it]

console.log(vals)     // [ 0, 1, 2, 3 ]
console.log([...it]) //  [ 0, 1, 2, 3 ]

```

### Spred Operator

the iterator-spread form of __`...`__ follows the iterator-consumption protocol to retrieve all available values from an receiving context.

```js
// Make an array
var TheArray = [0, 1, 2, 3]

// spred 
logArguments(...TheArray);

function logArguments() {
  // Arguments:
  console.log(arguments) // [Arguments] { '0': 0, '1': 1, '2': 2, '3': 3 }

  // The arguments object is a local variable available within all non-arrow functions. 
  // Refer inside the function by using arguments word.
  // arguments has entries for each argument the function was called with, with the first entry's index at 0.
  console.log(arguments[0]) 

  const TheArguments = [...arguments]
  
  // spred the arguments incoming
  console.log(TheArguments) // 0 1 2 3 
}
```

### Iterables

The iterator-consumption protocol is technically defined for consuming _iterables_; 
an iterable is a value that can be iterated over.

The protocol automatically creates an iterator instance from an iterable, 
and consumes _just that iterator instance_ to its completion. 

This means a single iterable could be consumed more than once; each time, a new iterator instance would be created and used.

So where do we find iterables?

ES6 defined the basic data structure/collection types in JS as iterables. 

This includes strings, arrays, maps, sets, and others.

Consider:

```js
// an array is an iterable
var arr = [10, 20, 30];

for (let val of arr) {
    console.log(`Array value: ${val}`);
}
// Array value: 10
// Array value: 20
// Array value: 30
```

Since arrays are iterables, we can shallow-copy an array using iterator consumption via the `...` spread operator:

```js
var arrCopy = [...arr];
```

We can also iterate the characters in a string one at a time:

```js
var greeting = "Hello world!";
var chars = [...greeting];

console.log(chars)
```

A `Map` data structure uses objects as keys, associating a value (of any type) with that object. Maps have a different default iteration than seen here, in that the iteration is not just over the map's values but instead its _entries_. An _entry_ is a tuple (2-element array) including both a key and a value.

Consider:

```js
// given two DOM elements, `btn1` and `btn2`

var buttonNames = new Map();
buttonNames.set(btn1, "Button 1");
buttonNames.set(btn2, "Button 2");

for (let [btn, btnName] of buttonNames) {
    btn.addEventListener("click", function onClick() {
        console.log(`Clicked ${btnName}`);
    });
}
```

In the `for..of` loop over the default map iteration, we use the `[btn,btnName]` syntax (called "array destructuring") to break down each consumed tuple into the respective key/value pairs (`btn1` / `"Button 1"` and `btn2` / `"Button 2"`).

Each of the built-in iterables in JS expose a default iteration, one which likely matches your intuition. But you can also choose a more specific iteration if necessary. For example, if we want to consume only the values of the above `buttonNames` map, we can call `values()` to get a values-only iterator:

```js
for (let btnName of buttonNames.values()) {
    console.log(btnName);
}
// Button 1
// Button 2
```

Or if we want the index _and_ value in an array iteration, we can make an entries iterator with the `entries()` method:

```js
var arr = [10, 20, 30];

for (let [idx, val] of arr.entries()) {
    console.log(`[${idx}]: ${val}`);
}
// [0]: 10
// [1]: 20
// [2]: 30
```

For the most part, all built-in iterables in JS have three iterator forms available: keys-only (`keys()`), values-only (`values()`), and entries (`entries()`).

Beyond just using built-in iterables, you can also ensure your own data structures adhere to the iteration protocol; doing so means you opt into the ability to consume your data with `for..of` loops and the `...` operator. "Standardizing" on this protocol means code that is overall more readily recognizable and readable.

| NOTE:                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| You may have noticed a nuanced shift that occurred in this discussion. We started by talking about consuming **iterators**, but then switched to talking about iterating over **iterables**. The iteration-consumption protocol expects an _iterable_, but the reason we can provide a direct _iterator_ is that an iterator is just an iterable of itself! When creating an iterator instance from an existing iterator, the iterator itself is returned. |



## `this` Keyword

One of JS's most powerful mechanisms is also one of its most misunderstood: the `this` keyword. One common misconception is that a function's `this` refers to the function itself. Because of how `this` works in other languages, another misconception is that `this` points the instance that a method belongs to. Both are incorrect.

As discussed previously, when a function is defined, it is _attached_ to its enclosing scope via closure. Scope is the set of rules that controls how references to variables are resolved.

But functions also have another characteristic besides their scope that influences what they can access. This characteristic is best described as an _execution context_, and it's exposed to the function via its `this` keyword.

Scope is static and contains a fixed set of variables available at the moment and location you define a function, but a function's execution _context_ is dynamic, entirely dependent on **how it is called** (regardless of where it is defined or even called from).

`this` is not a fixed characteristic of a function based on the function's definition, but rather a dynamic characteristic that's determined each time the function is called.

One way to think about the _execution context_ is that it's a tangible object whose properties are made available to a function while it executes. Compare that to scope, which can also be thought of as an _object_; except, the _scope object_ is hidden inside the JS engine, it's always the same for that function, and its _properties_ take the form of identifier variables available inside the function.

```js
function classroom(teacher) {
    return function study() {
        console.log(`${teacher} says to study ${this.topic}`);
    };
}
var assignment = classroom("Kyle");
```

The outer `classroom(..)` function makes no reference to a `this` keyword, so it's just like any other function we've seen so far. But the inner `study()` function does reference `this`, which makes it a `this`-aware function. In other words, it's a function that is dependent on its _execution context_.

| NOTE:                                                                      |
| :------------------------------------------------------------------------- |
| `study()` is also closed over the `teacher` variable from its outer scope. |

The inner `study()` function returned by `classroom("Kyle")` is assigned to a variable called `assignment`. So how can `assignment()` (aka `study()`) be called?

```js
assignment();
// Kyle says to study undefined  -- Oops :(
```

In this snippet, we call `assignment()` as a plain, normal function, without providing it any _execution context_.

Since this program is not in strict mode (see Chapter 1, "Strictly Speaking"), context-aware functions that are called **without any context specified** default the context to the global object (`window` in the browser). As there is no global variable named `topic` (and thus no such property on the global object), `this.topic` resolves to `undefined`.

Now consider:

```js
var homework = {
    topic: "JS",
    assignment: assignment,
};

homework.assignment();
// Kyle says to study JS
```

A copy of the `assignment` function reference is set as a property on the `homework` object, and then it's called as `homework.assignment()`. That means the `this` for that function call will be the `homework` object. Hence, `this.topic` resolves to `"JS"`.

Lastly:

```js
var otherHomework = {
    topic: "Math",
};

assignment.call(otherHomework);
// Kyle says to study Math
```

A third way to invoke a function is with the `call(..)` method, which takes an object (`otherHomework` here) to use for setting the `this` reference for the function call. The property reference `this.topic` resolves to `"Math"`.

The same context-aware function invoked three different ways, gives different answers each time for what object `this` will reference.

The benefit of `this`-aware functions—and their dynamic context—is the ability to more flexibly re-use a single function with data from different objects. A function that closes over a scope can never reference a different scope or set of variables. But a function that has dynamic `this` context awareness can be quite helpful for certain tasks.

```js
function classroom(teacher) {
  return function study() {
      console.log(
          `${ teacher } says to study ${ this.topic }`
      );
  };
}

var assignment = classroom("Kyle");

var homework = {
  topic: "JS",
  assignment: assignment
};

homework.assignment();  // Kyle says to study JS


var otherHomework = {
  topic: "Math"
};

assignment.call(otherHomework); // Kyle says to study Math
```

## Prototypes

Where `this` is a characteristic of function execution, a prototype is a characteristic of an object, and specifically resolution of a property access.

Think about a prototype as a linkage between two objects; the linkage is hidden behind the scenes, though there are ways to expose and observe it. This prototype linkage occurs when an object is created; it's linked to another object that already exists.

A series of objects linked together via prototypes is called the "prototype chain."

The purpose of this prototype linkage (i.e., from an object B to another object A) is so that accesses against B for properties/methods that B does not have, are _delegated_ to A to handle. Delegation of property/method access allows two (or more!) objects to cooperate with each other to perform a task.

Consider defining an object as a normal literal:

```js
var homework = {
    topic: "JS",
};
```

The `homework` object only has a single property on it: `topic`. However, its default prototype linkage connects to the `Object.prototype` object, which has common built-in methods on it like `toString()` and `valueOf()`, among others.

We can observe this prototype linkage _delegation_ from `homework` to `Object.prototype`:

```js
homework.toString(); // [object Object]
```

`homework.toString()` works even though `homework` doesn't have a `toString()` method defined; the delegation invokes `Object.prototype.toString()` instead.

### Object Linkage

To define an object prototype linkage, you can create the object using the `Object.create(..)` utility:

```js
var homework = {
    topic: "JS",
};

var otherHomework = Object.create(homework);

otherHomework.topic; // "JS"
```

The first argument to `Object.create(..)` specifies an object to link the newly created object to, and then returns the newly created (and linked!) object.

Figure 4 shows how the three objects (`otherHomework`, `homework`, and `Object.prototype`) are linked in a prototype chain:

<figure>
    <img src="images/fig4.png" width="200" alt="Prototype chain with 3 objects" align="center">
    <figcaption><em>Fig. 4: Objects in a prototype chain</em></figcaption>
    <br><br>
</figure>

Delegation through the prototype chain only applies for accesses to lookup the value in a property. If you assign to a property of an object, that will apply directly to the object regardless of where that object is prototype linked to.

| TIP:                                                                                                                                                                   |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Object.create(null)` creates an object that is not prototype linked anywhere, so it's purely just a standalone object; in some circumstances, that may be preferable. |

Consider:

```js
homework.topic;
// "JS"

otherHomework.topic;
// "JS"

otherHomework.topic = "Math";
otherHomework.topic;
// "Math"

homework.topic;
// "JS" -- not "Math"
```

The assignment to `topic` creates a property of that name directly on `otherHomework`; there's no effect on the `topic` property on `homework`. The next statement then accesses `otherHomework.topic`, and we see the non-delegated answer from that new property: `"Math"`.

Figure 5 shows the objects/properties after the assignment that creates the `otherHomework.topic` property:

<figure>
    <img src="images/fig5.png" width="200" alt="3 objects linked, with shadowed property" align="center">
    <figcaption><em>Fig. 5: Shadowed property 'topic'</em></figcaption>
    <br><br>
</figure>

The `topic` on `otherHomework` is "shadowing" the property of the same name on the `homework` object in the chain.

| NOTE:                                                                                                                                                                                                                                                                                                   |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Another frankly more convoluted but perhaps still more common way of creating an object with a prototype linkage is using the "prototypal class" pattern, from before `class` (see Chapter 2, "Classes") was added in ES6. We'll cover this topic in more detail in Appendix A, "Prototypal 'Classes'". |

### `this` Revisited

We covered the `this` keyword earlier, but its true importance shines when considering how it powers prototype-delegated function calls. Indeed, one of the main reasons `this` supports dynamic context based on how the function is called is so that method calls on objects which delegate through the prototype chain still maintain the expected `this`.

Consider:

```js
var homework = {
    study() {
        console.log(`Please study ${this.topic}`);
    },
};

var jsHomework = Object.create(homework);
jsHomework.topic = "JS";
jsHomework.study();
// Please study JS

var mathHomework = Object.create(homework);
mathHomework.topic = "Math";
mathHomework.study();
// Please study Math
```

The two objects `jsHomework` and `mathHomework` each prototype link to the single `homework` object, which has the `study()` function. `jsHomework` and `mathHomework` are each given their own `topic` property (see Figure 6).

<figure>
    <img src="images/fig6.png" width="495" alt="4 objects prototype linked" align="center">
    <figcaption><em>Fig. 6: Two objects linked to a common parent</em></figcaption>
    <br><br>
</figure>

`jsHomework.study()` delegates to `homework.study()`, but its `this` (`this.topic`) for that execution resolves to `jsHomework` because of how the function is called, so `this.topic` is `"JS"`. Similarly for `mathHomework.study()` delegating to `homework.study()` but still resolving `this` to `mathHomework`, and thus `this.topic` as `"Math"`.

The preceding code snippet would be far less useful if `this` was resolved to `homework`. Yet, in many other languages, it would seem `this` would be `homework` because the `study()` method is indeed defined on `homework`.

Unlike many other languages, JS's `this` being dynamic is a critical component of allowing prototype delegation, and indeed `class`, to work as expected!
