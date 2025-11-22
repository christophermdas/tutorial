# The Complete Guide to the JavaScript Language

This document is a comprehensive, in-depth guide to the JavaScript language, designed for both beginners and experienced developers. It covers everything from the fundamental building blocks to the most advanced, modern features.

---

# Part 1: JavaScript Fundamentals

---

## Chapter 1: Getting Started

### What is JavaScript?
JavaScript (JS) is a high-level, interpreted programming language that is one of the core technologies of the World Wide Web, alongside HTML and CSS. Initially used for client-side scripting in web browsers, it has since evolved into a versatile language used for server-side development (Node.js), mobile apps (React Native), desktop apps (Electron), and more.

### Setting Up Your Environment

You don't need any special tools to start with JavaScript.

*   **Browser Console:** Every modern web browser has a built-in JavaScript console. You can open it by right-clicking on a webpage, selecting "Inspect," and then clicking the "Console" tab.
*   **Node.js:** For running JavaScript outside the browser, you can install Node.js from [nodejs.org](https://nodejs.org/). This allows you to run `.js` files directly from your terminal (`node your_script.js`).

---

## Chapter 2: Variables and Scope

Variables are containers for storing data values.

### Declaring Variables: `var`, `let`, `const`

Modern JavaScript provides three keywords for declaring variables, each with different scoping rules.

*   `var`: The original way to declare variables. It is **function-scoped**.
*   `let`: Introduced in ES6 (2015). It is **block-scoped**. This is the preferred way to declare a variable whose value can change.
*   `const`: Also introduced in ES6. It is **block-scoped** and stands for "constant." The value cannot be reassigned after it's declared. This is the preferred default.

```javascript
// var is function-scoped
function varTest() {
  var x = 1;
  if (true) {
    var x = 2;  // same variable!
    console.log(x);  // 2
  }
  console.log(x);  // 2
}

// let is block-scoped
function letTest() {
  let x = 1;
  if (true) {
    let x = 2;  // different variable
    console.log(x);  // 2
  }
  console.log(x);  // 1
}
```

### Hoisting
`var` declarations are "hoisted" to the top of their scope, meaning you can use a variable before it has been declared. `let` and `const` are also hoisted, but they are not initialized, which creates a "Temporal Dead Zone" where you cannot access them before the declaration.

```javascript
console.log(myVar); // undefined (due to hoisting)
var myVar = 5;

// console.log(myLet); // ReferenceError: Cannot access 'myLet' before initialization
let myLet = 10;
```

---

## Chapter 3: Data Types

JavaScript has several primitive data types and one complex data type.

### Primitive Types
1.  **String**: A sequence of characters.
    ```javascript
    let name = "Alice";
    let greeting = 'Hello, world!';
    ```
2.  **Number**: Represents both integer and floating-point numbers.
    ```javascript
    let age = 30;
    let price = 19.99;
    ```
3.  **Boolean**: Represents `true` or `false`.
    ```javascript
    let isActive = true;
    ```
4.  **`null`**: Represents the intentional absence of any object value. It is a primitive value, but `typeof null` confusingly returns `"object"`.
    ```javascript
    let user = null;
    ```
5.  **`undefined`**: A variable that has been declared but not assigned a value has the type `undefined`.
    ```javascript
    let city; // city is undefined
    ```
6.  **Symbol**: A unique and immutable primitive value, often used as an identifier for object properties.
    ```javascript
    const id = Symbol('id');
    ```
7.  **BigInt**: For integers of arbitrary length.
    ```javascript
    const bigNumber = 1234567890123456789012345678901234567890n;
    ```

### The `object` Type
An object is a collection of key-value pairs. Arrays, functions, and maps are all specialized types of objects.

---

## Chapter 4: Operators

JavaScript has a wide range of operators to perform computations.

*   **Arithmetic:** `+`, `-`, `*`, `/`, `%` (modulus), `**` (exponentiation), `++` (increment), `--` (decrement).
*   **Assignment:** `=`, `+=`, `-=`, `*=`, `/=`.
*   **Comparison:** `==` (loose equality, with type coercion), `===` (strict equality, no type coercion), `!=`, `!==`, `>`, `<`, `>=`, `<=`.
*   **Logical:** `&&` (AND), `||` (OR), `!` (NOT).
*   **Ternary Operator:** A shortcut for an `if` statement.
    ```javascript
    const age = 20;
    const message = age >= 18 ? "Adult" : "Minor";
    ```
*   **Nullish Coalescing Operator (`??`):** Returns the right-hand side if the left-hand side is `null` or `undefined`.
    ```javascript
    let volume = 0;
    const setting = volume ?? 50; // setting will be 0
    const oldDefault = volume || 50; // oldDefault would be 50, which is often not desired
    ```

---

# Part 2: Control Flow and Functions

---

## Chapter 5: Control Flow

*   **`if...else`**: Executes a block of code if a condition is true.
*   **`switch`**: Selects one of many code blocks to be executed.
*   **Loops**:
    *   `for`: Loops through a block of code a number of times.
    *   `while`: Loops while a condition is true.
    *   `for...in`: Loops through the properties of an object.
    *   `for...of`: Loops over iterable objects like arrays and strings. (Modern standard for iteration).

    ```javascript
    const fruits = ["apple", "banana", "cherry"];
    for (const fruit of fruits) {
      console.log(fruit);
    }
    ```

---

## Chapter 6: Functions

Functions are blocks of code designed to perform a particular task.

### Arrow Functions
Arrow functions offer a shorter syntax and a non-binding of `this`.

```javascript
// Traditional
function greet(name) {
  return "Hello, " + name;
}

// Arrow
const greetArrow = name => `Hello, ${name}`;
```

### `this` Keyword
*   **In a regular function**, `this` refers to the object that called the function.
*   **In an arrow function**, `this` refers to the `this` of the enclosing (parent) scope.

### Closures
A closure is a function that has access to the variables in its parent scope, even after the parent function has closed.

```javascript
function createCounter() {
  let count = 0;
  return function() {
    count++;
    return count;
  };
}

const counter1 = createCounter();
console.log(counter1()); // 1
console.log(counter1()); // 2
```

---

# Part 3: Working with Data

---

## Chapter 7: Built-in Methods for Data Types

### String Methods
*   `length`: Property to get the length.
*   `slice(start, end)`: Extracts a part of a string.
*   `toUpperCase() / toLowerCase()`: Converts to upper/lower case.
*   `trim()`: Removes whitespace from both ends.
*   `split(separator)`: Splits a string into an array of substrings.
*   `includes(substring)`: Checks if a string contains a substring.

### Array Methods
Arrays in JavaScript come with a rich set of built-in methods.

#### Methods that Mutate the Original Array
*   `push(item)`: Adds an item to the end.
*   `pop()`: Removes the last item.
*   `shift()`: Removes the first item.
*   `unshift(item)`: Adds an item to the beginning.
*   `splice(start, deleteCount, item1, ...)`: Adds/Removes items from an array.
*   `sort()`: Sorts the array.

#### Methods for Iteration (Do Not Mutate)
*   `forEach(callback)`: Executes a provided function once for each array element.
*   `map(callback)`: Creates a new array with the results of calling a provided function on every element.
*   `filter(callback)`: Creates a new array with all elements that pass the test implemented by the provided function.
*   `reduce(callback, initialValue)`: Executes a reducer function on each element, resulting in a single output value.

```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2); // [2, 4, 6, 8, 10]
const evens = numbers.filter(n => n % 2 === 0); // [2, 4]
const sum = numbers.reduce((total, n) => total + n, 0); // 15
```

### Object Methods
*   `Object.keys(obj)`: Returns an array of a given object's own enumerable property names.
*   `Object.values(obj)`: Returns an array of a given object's own enumerable property values.
*   `Object.entries(obj)`: Returns an array of a given object's own enumerable string-keyed property `[key, value]` pairs.
*   `Object.assign(target, ...sources)`: Copies all enumerable own properties from one or more source objects to a target object.

---

# Part 4: Asynchronous JavaScript

---

## Chapter 8: The Asynchronous Model

JavaScript is single-threaded, but it handles long-running operations (like network requests) without freezing the user interface by using an **event loop**.

```
                        +----------------+
                        |   Call Stack   |
                        +----------------+
                                ^
                                | (Push/Pop)
+------------------------------------------------------------------+
|                                                                  |
|   +----------------+     +----------------+     +--------------+ |
|   | Web APIs       |---->| Callback Queue |---->| Event Loop   | |
|   | (setTimeout,   |     | (onClick,      |     | (Pushes to   | |
|   |  fetch)        |     |  onLoad)       |     |  Call Stack) | |
|   +----------------+     +----------------+     +--------------+ |
|                                                                  |
+------------------------------------------------------------------+
```

## Chapter 9: Handling Asynchronicity

### Callbacks
The original method, where a function is passed as an argument to be executed after an operation completes. Can lead to "Callback Hell."

### Promises
A `Promise` is an object representing the eventual completion or failure of an asynchronous operation.

```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("Success!");
  }, 1000);
});

myPromise
  .then(result => console.log(result)) // "Success!"
  .catch(error => console.error(error));
```
*   `Promise.all(promises)`: Fulfills when all promises fulfill.
*   `Promise.race(promises)`: Fulfills or rejects as soon as one of the promises fulfills or rejects.
*   `Promise.allSettled(promises)`: Fulfills after all promises have settled (either fulfilled or rejected).

### `async/await`
Modern syntactic sugar over promises that makes asynchronous code look synchronous.

*   `async` keyword before a function makes it return a promise.
*   `await` keyword pauses the function execution until a promise is settled.

```javascript
async function fetchData() {
  try {
    const response = await fetch('https://api.example.com/data');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Could not fetch data:", error);
  }
}
```

---

# Part 5: OOP and Modules

---

## Chapter 10: ES6 Classes
Classes are a template for creating objects. They are syntactic sugar over JavaScript's existing prototype-based inheritance.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    console.log(`${this.name} makes a noise.`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // call the parent constructor
    this.breed = breed;
  }

  speak() {
    console.log(`${this.name} barks.`);
  }
}

const myDog = new Dog('Buddy', 'Golden Retriever');
myDog.speak(); // 'Buddy barks.'
```

## Chapter 11: Modules
Modules allow you to split your code into separate, reusable files.

### Named Exports
```javascript
// 📁 utils.js
export const PI = 3.14;
export function double(n) {
  return n * 2;
}

// 📁 main.js
import { PI, double } from './utils.js';
console.log(double(PI));
```

### Default Export
A file can have only one default export.
```javascript
// 📁 User.js
export default class User {
  // ...
}

// 📁 main.js
import MyUser from './User.js'; // Can be named anything
```
