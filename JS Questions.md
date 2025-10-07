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

### 4. What are promises Explain with examples.
JavaScript runs in a single thread. If you do something that takes time (like fetching data from a server), it would block everything unless you do it asynchronously.

Promises let you:

Wait for things (like data) to finish
Handle success or failure in a cleaner way
Chain multiple steps without writing deeply nested callbacks (a.k.a. “callback hell”)

Example
console.log("1");

setTimeout(() => {
  console.log("2 (Macrotask)");
}, 0);

Promise.resolve().then(() => {
  console.log("3 (Microtask)");
});

console.log("4");
Output:
1
4
3 (Microtask)
2 (Macrotask)

✅ 1. Promise.all – Wait for all to succeed

const getProfile = () => Promise.resolve("👤 User Profile");
const getOrders = () => Promise.resolve("🛒 Orders");
const getNotifications = () => Promise.resolve("🔔 Notifications");

Promise.all([getProfile(), getOrders(), getNotifications()])
  .then(([profile, orders, notifications]) => {
    console.log("All data loaded:");
    console.log(profile);       // 👤 User Profile
    console.log(orders);        // 🛒 Orders
    console.log(notifications); // 🔔 Notifications
  })
  .catch((err) => {
    console.error("Failed to load data:", err);
  });

  #using fetch 
  Promise.all([
  fetch("https://jsonplaceholder.typicode.com/users/1"),         // User
  fetch("https://jsonplaceholder.typicode.com/posts?userId=1"),  // Posts
  fetch("https://jsonplaceholder.typicode.com/todos?userId=1")   // Todos
])
  .then(async ([userRes, postsRes, todosRes]) => {
    const user = await userRes.json();
    const posts = await postsRes.json();
    const todos = await todosRes.json();

    console.log("👤 User:", user.name);
    console.log("📝 Posts count:", posts.length);
    console.log("✅ Todos count:", todos.length);
  })
  .catch((err) => {
    console.error("🚫 One or more requests failed:", err);
  });
🔥 If any one of the promises fails, the whole .catch() is triggered.

🔐 2. Promise.allSettled – Wait for all to finish, regardless of success or failure

const getProfile = () => Promise.resolve("✅ Profile loaded");
const getOrders = () => Promise.reject("❌ Orders API failed");
const getNotifications = () => Promise.resolve("✅ Notifications loaded");

Promise.allSettled([getProfile(), getOrders(), getNotifications()])
  .then((results) => {
    results.forEach((result, i) => {
      console.log(`Result ${i + 1}:`, result.status, result.value || result.reason);
    });
  });
👍 Use this when you want results from all, even if some failed.

🏁 3. Promise.race – Takes the first one to finish

const slow = () => new Promise(res => setTimeout(() => res("Slow"), 2000));
const fast = () => new Promise(res => setTimeout(() => res("Fast"), 500));

Promise.race([slow(), fast()])
  .then((result) => {
    console.log("Winner:", result); // Fast
  });
🏎 Use when first response wins, like timeout fallback logic.

🟢 4. Promise.any – Takes the first successful promise

const p1 = Promise.reject("❌ First failed");
const p2 = Promise.reject("❌ Second failed");
const p3 = Promise.resolve("✅ Third succeeded");

Promise.any([p1, p2, p3])
  .then((result) => {
    console.log("First successful:", result); // ✅ Third succeeded
  })
  .catch((err) => {
    console.error("All failed");
  });
✅ Useful when only one success is needed.

🧠 Summary Table
Method	Waits for All	Fails Fast	Returns All Results	Use When…
Promise.all	✅	✅	❌	All must succeed
Promise.allSettled	✅	❌	✅	You want all results regardless
Promise.race	❌	❌	❌	First to finish matters
Promise.any	❌	❌	❌	Only one success needed

### 5. What is debouncing vs throttling?

👉 Debounce delays the function execution until after the user has stopped triggering the event for a certain time.


function debounce(fn, delay) {
  let timeout;
  return function (...args) {
    clearTimeout(timeout);
    timeout = setTimeout(() => fn.apply(this, args), delay);
  };
}

// Usage
const searchHandler = debounce(() => {
  console.log("API call made after user stops typing");
}, 500);

input.addEventListener("input", searchHandler);
📍 Used for: search boxes, resize events, form validation.

🕒 Throttling Explained
👉 Throttle ensures the function executes at most once every X milliseconds, even if triggered multiple times.


function throttle(fn, limit) {
  let lastCall = 0;
  return function (...args) {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      fn.apply(this, args);
    }
  };
}

// Usage
const scrollHandler = throttle(() => {
  console.log("Scroll position checked every 200ms");
}, 200);

window.addEventListener("scroll", scrollHandler);
📍 Used for: scroll events, window resizing, button mashing prevention.
✅ Debouncing vs Throttling — Quick Summary
Feature	Debounce	Throttle
Definition	Executes after a pause	Executes at regular intervals
Trigger Style	Waits until event stops	Executes every X ms, no matter how often
Use Case	Auto-suggest, search input, resize events	Scroll position updates, API polling
Real Analogy	Wait until user stops typing to send API	Allow 1 tweet per minute max

### 6. Explain shallow copy vs deep copy

Type	What It Does
Shallow Copy	Copies only the first level; nested objects/arrays are still linked
Deep Copy	Copies everything recursively; nested objects/arrays are completely independent

📦 Example: Shallow Copy

const original = {
  name: "Manjunath",
  address: { city: "Bengaluru" }
};

const shallowCopy = { ...original };

shallowCopy.name = "Ajay";           // ✅ changes only the copy
shallowCopy.address.city = "Mysuru"; // ❗ changes both copy and original

console.log(original.address.city);  // ❗ "Mysuru" — unexpected mutation
💡 ...spread, Object.assign(), Array.slice() → all create shallow copies.

📦 Example: Deep Copy

const original = {
  name: "Manjunath",
  address: { city: "Bengaluru" }
};

// Deep Copy using structuredClone (best modern method)
const deepCopy = structuredClone(original);

deepCopy.address.city = "Mysuru";

console.log(original.address.city); // ✅ "Bengaluru" — safe!
✅ The nested object is fully independent now.


### 7. Explain ployfill and implement it for mpa and filter
A polyfill is a custom implementation of a native JavaScript feature — used when that feature doesn’t exist in older browsers or to show understanding of how it works internally.

1️⃣ Polyfill for Array.prototype.map()

👉 map() takes a callback and returns a new array with results of calling that callback on each element.

✅ Native Example
const arr = [1, 2, 3];
const doubled = arr.map(num => num * 2);
console.log(doubled); // [2, 4, 6]

🧩 Polyfill Implementation
Array.prototype.myMap = function(callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  const result = [];
  for (let i = 0; i < this.length; i++) {
    // skip empty slots in sparse arrays
    if (this.hasOwnProperty(i)) {
      result.push(callback.call(thisArg, this[i], i, this));
    }
  }
  return result;
};

// ✅ Example
const nums = [1, 2, 3];
const doubled = nums.myMap(x => x * 2);
console.log(doubled); // [2, 4, 6]

2️⃣ Polyfill for Array.prototype.filter()

👉 filter() creates a new array with all elements that pass a test.

✅ Native Example
const arr = [1, 2, 3, 4];
const even = arr.filter(num => num % 2 === 0);
console.log(even); // [2, 4]

🧩 Polyfill Implementation
Array.prototype.myFilter = function(callback, thisArg) {
  if (typeof callback !== "function") {
    throw new TypeError(callback + " is not a function");
  }

  const result = [];
  for (let i = 0; i < this.length; i++) {
    if (this.hasOwnProperty(i)) {
      if (callback.call(thisArg, this[i], i, this)) {
        result.push(this[i]);
      }
    }
  }
  return result;
};

// ✅ Example
const arr = [1, 2, 3, 4, 5];
const even = arr.myFilter(num => num % 2 === 0);
console.log(even); // [2, 4]

### 8. Explain call apply and bind
1️⃣ call() — Call immediately, pass arguments individually
person.greet.call(anotherPerson, "Bengaluru", "India");
// Output: Hi, I’m Rahul from Bengaluru, India


✅ Usage:

Executes the function immediately

Sets the this context to anotherPerson

Arguments are passed comma-separated

2️⃣ apply() — Call immediately, pass arguments as an array
person.greet.apply(anotherPerson, ["Mysuru", "India"]);
// Output: Hi, I’m Rahul from Mysuru, India


✅ Usage:

Executes the function immediately

Same as call(), but arguments are passed as an array

Handy when you already have data in array form

3️⃣ bind() — Doesn’t call immediately, returns a new function
const boundGreet = person.greet.bind(anotherPerson, "Hubballi", "India");
boundGreet(); 
// Output: Hi, I’m Rahul from Hubballi, India


✅ Usage:

Returns a new function with fixed this

Can be called later

Useful in event handlers, callbacks, React class components, etc.
💡 Practical Example (React-like use case)
const button = {
  name: "Save",
  handleClick: function() {
    console.log(`${this.name} button clicked`);
  }
};

const anotherButton = { name: "Delete" };

setTimeout(button.handleClick, 1000); 
// ❌ undefined button clicked (lost context)

setTimeout(button.handleClick.bind(anotherButton), 1000);
// ✅ Delete button clicked

👉 Here, bind() is used to preserve the context for later execution.

### 9. What is this?
The this keyword refers to the object that is currently executing the function.

Its value depends on how a function is called, not where it’s written.

Inside an Object Method
const user = {
  name: "Manjunath",
  greet: function() {
    console.log(`Hello, ${this.name}`);
  }
};
user.greet(); // Hello, Manjunath
✅ Here this refers to the object before the dot → user.

Losing this Context (Common Bug)
const user = {
  name: "Manjunath",
  greet: function() {
    console.log(this.name);
  }
};

const greetFn = user.greet;
greetFn(); // ❌ undefined (or error)

👉 Because greetFn is called without an object, this defaults to global (window) — and window.name is not “Manjunath”.

✅ Fix using .bind():
const boundGreet = user.greet.bind(user);
boundGreet(); // ✅ Manjunath

Inside Arrow Functions
Arrow functions do not have their own this.
They inherit this from the surrounding (lexical) scope.

const user = {
  name: "Manjunath",
  greet: () => {
    console.log(this.name);
  }
};

user.greet(); 