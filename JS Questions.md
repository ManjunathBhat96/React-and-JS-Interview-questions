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

