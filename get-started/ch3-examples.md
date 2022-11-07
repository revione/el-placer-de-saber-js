### Spred

```js
// Make an array
var TheArray = [0, 1, 2, 3]

// spred array into a function
logArguments(...TheArray)

function logArguments() {
  console.log(arguments)
  // [Arguments] { '0': 0, '1': 1, '2': 2, '3': 3 }
  //The arguments object is a local variable available within all non-arrow functions.
  console.log(arguments[0])

  const TheArguments = [...arguments]

  // spred the arguments incoming
  console.log(TheArguments) // [ 0, 1, 2, 3 ]
  console.log(...arguments) // 0 1 2 3
}

```

### Spred a string

```js
var greeting = "Hello world!";
var chars = [...greeting];

console.log(chars)
/* 
  [
    'H', 'e', 'l', 'l',
    'o', ' ', 'w', 'o',
    'r', 'l', 'd', '!'
  ]
*/
```

### Map iteration

```js

var btn1 = { name: "btn1" }

var btn2 = { name: "btn2" }

var buttonNames = new Map()
console.log(buttonNames) // Map(0) {}
buttonNames.set(btn1, "Button 1")
console.log(buttonNames) // Map(1) { { name: 'btn1' } => 'Button 1' }
buttonNames.set(btn2, "Button 2")
console.log(buttonNames)
/*
  Map(2) {
    { name: 'btn1' } => 'Button 1',
    { name: 'btn2' } => 'Button 2'
  }
*/

for (let [btn,btnName] of buttonNames) {
    console.log(`${btn} ${btn.name} ${btnName}`)
    // [object Object] btn1 Button 1
    // [object Object] btn2 Button 2
}

for (let btnName of buttonNames.values()) {
  console.log(btnName)
  // Button 1
  // Button 2
}

for (let btnName of buttonNames.keys()) {
  console.log(btnName)
  // { name: 'btn1' }
  // { name: 'btn2' }
}
```

### Array entries iteration

```js

var arr = [10, 20, 30]

for (let [idx, val] of arr.entries()) {
  console.log(`[${idx}]: ${val}`)
}

```

The iteration-consumption protocol expects an iterable, but the reason we can provide a direct iterator is that an iterator is just an iterable of itself! When creating an iterator instance from an existing iterator, the iterator itself is returned.

