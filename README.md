proposal-logical-assignment
===========================

A Stage 0 proposal to combine Logical Operators and Assignment Expressions:

```js
// "Or Or Equals" (or, the Mallot operator :wink:)
a ||= b;
a || (a = b);

// "And And Equals"
a &&= b;
a && (a = b);

// Eventually....
// "Nullish Coalescing Equals"
a ??= b;
a ?? (a = b);
```

Motivation
----------

Conveinence operators, inspired by [Ruby's](https://docs.ruby-lang.org/en/2.5.0/syntax/assignment_rdoc.html#label-Abbreviated+Assignment). We already have a dozen [mathematical assignment operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Assignment_Operators), but we don't ones for the often used logical operators.

```js
function example(a = b) {
  // Default assignment only works for `undefined`.
  // But I want any falsey to deafult
  if (!a) {
    a = b;
  }
}

function numeric(a = b) {
  // Maybe I want to keep numeric 0, but default nullish
  if (a == null) {
    a = b;
  }
}
```

If statements work, but terseness would be nice.

```js
function example(a = b) {
  // Ok, but it triggers setter.
  a = a || b;

  // No setter, but sometimes flagged as a lint error!
  a || (a = b);
}
```

With this, we get terseness and we don't have to suffer from setter calls.

Semantics
---------

The logical assignment operators function a bit differntly than their mathematical assignment friends. While math assignment operators _always_ trigger a set operation, logical assignment embraces their short-ciruiting semantics to avoid it when possible.

```js
let x = 0;
const obj = {
  get x() {
    return x;
  },
  
  set x(value) {
    console.log('setter called');
    x = value;
  }
};

// This always logs "setter called"
obj.x += 1;
assert.equal(obj.x, 1);

// Logical operators do not call setters unnecessarily
// This will not log.
obj.x ||= 2;
assert.equal(obj.x, 1);

// But setters are called if the operator does not short circuit
// "setter called"
obj.x &&= 3;
assert.equal(obj.x, 3);
```

Related
-------

- [Ruby's logical operators](https://docs.ruby-lang.org/en/2.5.0/syntax/assignment_rdoc.html#label-Abbreviated+Assignment)
- My very first [Babel PR](https://github.com/babel/babel/pull/516) (back when it was still 6to5). 😄
