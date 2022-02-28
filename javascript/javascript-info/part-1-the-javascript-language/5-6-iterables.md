# Iterables

## Iterables and array-likes

- Iterables are objects that...
  - Implement the `Symbol.iterator` method
  - Can be used in `for...of`
  - Can be used with the spread syntax `...`
- Array-likes are objects that...
  - Have indexed properties and `length`

Arrays and strings are both iterable and array-like

### String is iterable

For a string, `for...of` loops over its characters; it works correctly with surrogate pairs

## Iterable example

```js
const range = {
  from: 1,
  to: 5,

  [Symbol.iterator]: function () {
    // Returns an iterator object
    return {
      current: this.from,
      last: this.to,

      next: function () {
        if (this.current <= this.last) {
          // { done: boolean, value: any }
          return { done: false, value: this.current++ };
        } else {
          return { done: true };
        }
      },
    };
  },
};

for (let num of range) {
  console.log(num);
}
```

We can merge the `range` object and the iterator object:

```js
// Using the `range` object itself as the iterator
const range = {
  from: 1,
  to: 5,

  [Symbol.iterator]: function () {
    this.current = this.from;
    return this;
  },

  next: function () {
    if (this.current <= this.to) {
      return { done: false, value: this.current++ };
    } else {
      return { done: true };
    }
  },
};
```

### Calling an iterator manually

```js
const str = "Hello";
const iterator = str[Symbol.iterator]();

while (true) {
  const result = iterator.next();
  if (result.done) break;
  console.log(result.value);
}
```

## `Array.from`

```txt
Array.from(iterable, ?mapFn, ?thisArg)
Array.from(arrayLike, ?mapFn, ?thisArg)
```

- Makes a real array from an iterable or array-like object
- Correctly works with surrogate pairs
  - Because it relies on the iterable nature

## References

- https://javascript.info/iterable
