---
title: 精读《Airbnb JavaScript Style Guide》
date: 2022-02-04 17:42:14
tags:
  - javascript
---

## Array

- To convert an iterable object to an array, use spreads ... instead of Array.from.

```javascript
const foo = document.querySelectorAll(".foo");

// good
const nodes = Array.from(foo);

// best
const nodes = [...foo];
```

- Use Array.from for converting an array-like object to an array.

```javascript
const arrLike = { 0: "foo", 1: "bar", 2: "baz", length: 3 };

// bad
const arr = Array.prototype.slice.call(arrLike);

// good
const arr = Array.from(arrLike);
```

- Use Array.from instead of spread ... for mapping over iterables, because it avoids creating an intermediate array.

```javascript
// bad
const baz = [...foo].map(bar);

// good
const baz = Array.from(foo, bar);
```

## Properties

- Use exponentiation operator \*\* when calculating exponentiations. eslint: no-restricted-properties

```javascript
// bad
const binary = Math.pow(2, 10);

// good
const binary = 2 ** 10;
```

## Variables

- Group all your consts and then group all your lets.

```javascript
// bad
let i,
  len,
  dragonball,
  items = getItems(),
  goSportsTeam = true;

// bad
let i;
const items = getItems();
let dragonball;
const goSportsTeam = true;
let len;

// good
const goSportsTeam = true;
const items = getItems();
let dragonball;
let i;
let length;
```

## Type Casting & Coercion

- Strings: eslint: no-new-wrappers

```javascript
// => this.reviewScore = 9;

// bad
const totalScore = new String(this.reviewScore); // typeof totalScore is "object" not "string"

// bad
const totalScore = this.reviewScore + ""; // invokes this.reviewScore.valueOf()

// bad
const totalScore = this.reviewScore.toString(); // isn’t guaranteed to return a string

// good
const totalScore = String(this.reviewScore);
```

- Numbers: Use Number for type casting and parseInt always with a radix for parsing strings. eslint: radix no-new-wrappers

> Why? The parseInt function produces an integer value dictated by interpretation of the contents of the string argument according to the specified radix. Leading whitespace in string is ignored. If radix is undefined or 0, it is assumed to be 10 except when the number begins with the character pairs 0x or 0X, in which case a radix of 16 is assumed. This differs from ECMAScript 3, which merely discouraged (but allowed) octal interpretation. Many implementations have not adopted this behavior as of 2013. And, because older browsers must be supported, always specify a radix.

```javascript
const inputValue = "4";

// bad
const val = new Number(inputValue);

// bad
const val = +inputValue;

// bad
const val = inputValue >> 0;

// bad
const val = parseInt(inputValue);

// good
const val = Number(inputValue);

// good
const val = parseInt(inputValue, 10);
```

- If for whatever reason you are doing something wild and parseInt is your bottleneck and need to use Bitshift for performance reasons, leave a comment explaining why and what you’re doing.

```javascript
// good
/**
 * parseInt was the reason my code was slow.
 * Bitshifting the String to coerce it to a
 * Number made it a lot faster.
 */
const val = inputValue >> 0;
```

- Booleans

```javascript
const age = 0;

// bad
const hasAge = new Boolean(age);

// good
const hasAge = Boolean(age);

// best
const hasAge = !!age;
```

## Naming Conventions

- Don’t save references to this. Use arrow functions or Function#bind.

```javascript
// bad
function foo() {
  const self = this;
  return function () {
    console.log(self);
  };
}

// bad
function foo() {
  const that = this;
  return function () {
    console.log(that);
  };
}

// good
function foo() {
  return () => {
    console.log(this);
  };
}
```

- You may optionally uppercase a constant only if it (1) is exported, (2) is a const (it can not be reassigned), and (3) the programmer can trust it (and its nested properties) to never change.
  - What about all const variables? - This is unnecessary, so uppercasing should not be used for constants within a file. It should be used for exported constants however.
  - What about exported objects? - Uppercase at the top level of export (e.g. EXPORTED_OBJECT.key) and maintain that all nested properties do not change.

> Why? This is an additional tool to assist in situations where the programmer would be unsure if a variable might ever change. UPPERCASE_VARIABLES are letting the programmer know that they can trust the variable (and its properties) not to change.

```javascript
// bad
const PRIVATE_VARIABLE = "should not be unnecessarily uppercased within a file";

// bad
export const THING_TO_BE_CHANGED = "should obviously not be uppercased";

// bad
export let REASSIGNABLE_VARIABLE = "do not use let with uppercase variables";

// ---

// allowed but does not supply semantic value
export const apiKey = "SOMEKEY";

// better in most cases
export const API_KEY = "SOMEKEY";

// ---

// bad - unnecessarily uppercases key while adding no semantic value
export const MAPPING = {
  KEY: "value",
};

// good
export const MAPPING = {
  key: "value",
};
```
