---
title: What Comes With ECMAScript2021?
date: 2020-06-20
permalink: /posts/2020/06/what-comes-with-ecmascript2021/
tags:
  - javascript
---

ECMAScript (or shortly ES) is a general-purpose programming language, standardized by Ecma International according to the document ECMA-262. JavaScript is the most popular programming language in which these standards have been applied. Currently, ECMAScript is commonly used for client-side scripting on the World Wide Web, and it is increasingly being used for writing applications and services using JavaScript/TypeScript.

The most important technologies where ECMAScript is implemented are JavaScript, SpiderMonkey, V8, ActionScript, JScript, QtScript, InScript, Google Apps Script. In short, ECMAScript is at the core of these technologies. Therefore, the emergence of these standards brings us great, new features. ES is developing since 1997 and now has 12 version so far. We all know.
ES12 version is expected to be released in June 2021. So, what features have the version ES12 or ECMAScript 2021? In this article, we will see the possible latest features of JavaScript in ES2021.

Here is a brief overview of the new features we can expect in ES2021 or ES12.

## Stage 4 Plans

Stage 4 means a feature will be in the next version.

- String.prototype.replaceAll
- Promise.any()
- WeakRefs and Finalizers
- Logical assignment operators
- Numeric separators

## 1. String.prototype.replaceAll

JavaScript already has a replace() method. It can be using for to replace a string with another string.

```js
const str = "Hate programming.";
const newStr = str.replace("Hate", "Love");
console.log(newStr); 

// OUTPUT: Love programming.
// <3
```

The new replaceAll method does not bring groundbreaking changes, but it's a nice addition. .replaceAll() works like .replace().

The replaceAll() method returns a new string with all matches of a pattern replaced by a replacement. The pattern can be a string or a RegExp, and the replacement can be a string or a function to be called for each match.

```js
// Replacing all occurrences of x with a
// ecmxscript becomes ecmascript
'ecmxscript'.replaceAll('x', 'a');
const p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';
console.log(p.replaceAll('dog', 'monkey'));
// expected output: "The quick brown fox jumps over the lazy monkey. If the monkey reacted, was it really lazy?"
// global flag required when calling replaceAll with regex
const regex = /Dog/ig;
console.log(p.replaceAll(regex, 'ferret'));
// expected output: "The quick brown fox jumps over the lazy ferret. If the ferret reacted, was it really lazy?"
```

## 2. Promise.any()

A new promise method comes: Promise.any().
This new method takes multiple promises and resolves once any of the promises are resolved. Promise.any() returns a Promise p. How it is settled, depends on the parameter promises.

```js
Syntax: 
Promise.any<T>(promises: Iterable<Promise<T>>): Promise<T>
Example:
const promise1 = Promise.reject(0);
const promise2 = new Promise((resolve) => setTimeout(resolve, 10, 'fast'));
const promise3 = new Promise((resolve) => setTimeout(resolve, 100, 'slow'));
const promises = [promise1, promise2, promise3];  Promise.any(promises).then((value) => console.log(value));
// Whichever resolves first is taken by Promise.any()
// In this case, 
(async function() {
  const result = await Promise.any([promise1, promise2, promise3]);
  console.log(result);
})();
```
## 3. WeakRefs and Finalizers

This feature is about shorthand for weak references, and its main usage is to hold a weak reference to another object.
Proposal WeakRefs can be viewed here: https://github.com/tc39/proposal-weakrefs

## 4. Logical assignment operators

This [proposal](https://github.com/tc39/proposal-logical-assignment) introduces the following assignment operators:

- a ||= b equavalent is a || (a = b)
- a &&= b equavalent is a && (a = b)
- a ??= b equavalent is a ?? (a = b)

```js
// Usage of a ||= b
var a = 1;
var b = 2;
a ||= b;
console.log(x);
// 1
// Usage of a &&= b
var x = 1;
var y = 2;
x &&= y;
console.log(x); 
// 2
// Usage of a ??= b
var x;
var y = 2;
x ??= y;
console.log(x); 
// 5
```

## 5. Numeric Seperator

This feature enables developers to make their numeric literals more readable by creating a visual separation between groups of digits.

### Regular Number Literals

```js
let budget = 1_000_000_000_000;
// What is the value of `budget`? It's 1 trillion!
// 
// Let's confirm:
console.log(budget === 10 ** 12); // true
```
### Binary Literals

```js
let nibbles = 0b1010_0001_1000_0101;
// Is bit 7 on? It sure is!
// 0b1010_0001_1000_0101
//           ^
//
// We can double check: 
console.log(!!(nibbles & (1 << 7))); // true
```

### Hex Literals

```js
// Messages are sent as 24 bit values, but should be 
// treated as 3 distinct bytes:
let message = 0xA0_B0_C0;
// What's the value of the upper most byte? It's A0, or 160.
// We can confirm that:
let a = (message >> 16) & 0xFF; 
console.log(a.toString(16), a); // a0, 160
// What's the value of the middle byte? It's B0, or 176.
// Let's just make sure...
let b = (message >> 8) & 0xFF;
console.log(b.toString(16), b); // b0, 176
// What's the value of the lower most byte? It's C0, or 192.
// Again, let's prove that:
let c = message & 0xFF;
console.log(c.toString(16), b); // c0, 192
```

### BigInt Literals

Numeric Separators are also available within BigInt literals.

```js
// Verifying max signed 64 bit numbers:
const max = 2n ** (64n - 1n) - 1n;
console.log(max === 9_223_372_036_854_775_807n);
```
---

You can get detailed information about all Stage 4 and Stage 3 features from here: https://github.com/tc39/proposals


