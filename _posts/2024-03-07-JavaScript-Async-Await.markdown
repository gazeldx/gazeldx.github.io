---
layout: post
title:  "JavaScript Await Async"
date:   2023-03-07 16:46:32
categories: JavaScript
---

## Async Await in JavaScript

```javascript
'use strict'

const delay = (time) => new Promise((resolve, reject) => setTimeout(resolve, time));

async function start1() {
  bar(); // Add a `await` before `bar()` to see what will happen.
  foo();
}

function foo() {
  console.log("==== start foo() ====");
  setTimeout(() => console.log("timeout in foo()"), 5000);
  console.log("==== end foo() ====");
}

async function bar() {
  console.log("==== start bar() ====");
  await delay(3000);
  console.log("==== end bar() ====");
}

start1();
```
For the above code, guess what it will print?

```javascript
'use strict'

const delay = (time) => new Promise((resolve, reject) => setTimeout(resolve, time));

async function start1() {
  bar(); // Add a `await` before `bar()` to see what will happen.
  foo();
}

function foo() {
  console.log("==== start foo() ====");
  setTimeout(() => console.log("timeout in foo()"), 5000);
  console.log("==== end foo() ====");
}

async function bar() {
  console.log("==== start bar() ====");
  await delay(3000);
  console.log("==== end bar() ====");
}

start1();
```

Actually, `await delay(3000);` will block the next line `console.log("==== end bar() ====");`,
but it will not block `foo()` unless it is `await bar();`.
