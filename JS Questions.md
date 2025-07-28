# JavaScript Interview Questions

### 1.Explain the difference between var, let, and const
> E1. var
Scope: Function-scoped

Hoisting: Yes, hoisted to the top of its scope and initialized with undefined

Redeclaration: Allowed within the same scope

Use case: Mostly legacy code — avoid using var in modern JavaScript.


function testVar() {
  console.log(a); // undefined (hoisted)
  var a = 10;
  console.log(a); // 10
}
testVar();

🟩 2. let
Scope: Block-scoped ({ ... })

Hoisting: Yes, but not initialized — accessing before declaration gives a ReferenceError

Redeclaration: Not allowed in the same scope

Use case: Use when the variable will change (e.g., loops, conditionals)

function testLet() {
  // console.log(b); // ReferenceError
  let b = 20;
  console.log(b); // 20
}
testLet();

🟦 3. const
Scope: Block-scoped

Hoisting: Yes, but not initialized (same as let)

Redeclaration: Not allowed in the same scope

Reassignment: ❌ Not allowed – the reference cannot change

Use case: Use when the value shouldn't change, especially for constants and function expressions


const c = 30;
// c = 40; // TypeError

const obj = { name: "Manju" };
obj.name = "Nath"; // ✅ allowed – object contents are mutable

### 2. What are closures? How are they used in real-world code?
A closure is a function that remembers the variables from its outer (enclosing) scope, even after that scope has finished executing.

🔧 How it works:
When a function is defined inside another function, the inner function has access to:

Its own variables

Variables from the outer function

Global variables

This inner function "closes over" the variables from its parent scope — this is a closure.

🧠 Example:
function outer() {
  let count = 0;

  return function inner() {
    count++;
    console.log(count);
  };
}

const counter = outer(); // outer() runs and returns inner()
counter(); // 1
counter(); // 2
counter(); // 3
Here:

inner() is a closure

It retains access to count even after outer() has finished executing

### 3. How does JavaScript’s event loop work?
🧠 In Simple Terms:
JavaScript has one main thread that runs your code.
But it can do things asynchronously (without blocking).
How? That’s where the event loop comes in!

🏗️ Key Components:
Call Stack 📚
Where function calls are executed one at a time.

Web APIs / Background Tasks 🌐
Browser-provided APIs like setTimeout, fetch, DOM events, etc.

Callback / Task Queues 📥

Callback Queue (Macrotasks): e.g., setTimeout, DOM events

Microtask Queue: e.g., Promise.then, async/await
(runs before the callback queue!)

Event Loop 🔁
A loop that keeps checking:

Is the call stack empty?

If yes, it moves tasks from queues into the stack.

### 4. What are promises, async/await? Explain with examples.
