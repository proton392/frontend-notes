# Generators

## Generator functions

- Can return (yield) multiple values, one after another
- Work great with iterables (見以下 "Generator objects")

When a generator function is called, it doesn't run its code; it returns a "generator object"

## Generator objects

- Main method: `next()`
  - When called, it runs the execution until the nearest `yield <value>` statement
    - `value` can be omitted, then it's `undefined`
  - The result of `next()` is always an object with two properties: `{ value: ..., done: ... }`
- Generator objects are iterable
  - Generator object 有 `Symbol.iterator` method, 因此為 iterable
  - 可以對其使用 `for...of`, spread syntax 等 syntax
- Generator object 有遵循 iterator protocol 之 `next` method, 因此本身也能作為 iterator object, 見以下

### Using generators for iterables

Example:

```js
let range = {
  from: 1,
  to: 5,

  [Symbol.iterator]: function* () {
    for (let value = this.from; value <= this.to; value++) {
      yield value;
    }
  },
};

const iteratorObject = range[Symbol.iterator]();
console.log(iteratorObject.next()); // { value: 1, done: false }
```

## Generator composition (`yield*`)

- Using `yield*` to "embed" (compose) one generator into another (that is, insert a flow of one generator into another)
- `yield*` delegates the execution to another generator

Example:

```js
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generateAlphaNum() {
  // 0...9
  yield* generateSequence(48, 57);

  // A...Z
  yield* generateSequence(65, 90);

  // a...z
  yield* generateSequence(97, 122);
}

const str = [...generateAlphaNum()]
  .map((code) => String.fromCodePoint(code))
  .join("");

console.log(str); // 0...9A...Za...z
```

The result is the same as if we inlined the code from nested generators:

```js
// Paste JS here
function* generateSequence(start, end) {
  for (let i = start; i <= end; i++) yield i;
}

function* generateAlphaNum() {
  // yield* generateSequence(48, 57);
  for (let i = 48; i <= 57; i++) yield i;

  // yield* generateSequence(65, 90);
  for (let i = 65; i <= 90; i++) yield i;

  // yield* generateSequence(97, 122);
  for (let i = 97; i <= 122; i++) yield i;
}

const str = [...generateAlphaNum()]
  .map((code) => String.fromCodePoint(code))
  .join("");

console.log(str); // 0...9A...Za...z
```

## `yield` is a two-way street

A generator and the calling code (the outer code) can exchange results via `next`/`yield` calls

`yield` can...

- Return the result to the outside
- Pass the value inside the generator by calling `generatorObject.next(value)`
  - `value` becomes the result of `yield`
  - 先賦值給上次暫停處的 `yield`, 再繼續執行
    - The first call `generatorObject.next()` should be always made without an argument

Example:

```js
function* generator() {
  const char = "A";

  const result = yield char + "B"; // 2. result="C"

  yield result + "D";
}

const generatorObject = generator();

console.log(generatorObject.next());
// 1. { value: "AB", done: false }

console.log(generatorObject.next("C"));
// 3. { value: "CD", done: false }

console.log(generatorObject.next());
// 4. { value: undefined, done: true }
```

## `generator.throw`

The outer code can throw an error into the generator

To pass an error into a `yield`, use `generatorObject.throw(err)`

In that case, the `err` is thrown in the line with that `yield`

Example:

```js
function* generator() {
  const char = "A";

  try {
    yield char + "B"; // 2. Error is thrown in this line
    yield "C";
  } catch (e) {
    console.log(e); // 3. Some error message
  }

  yield "D";
}

const generatorObject = generator();

console.log(generatorObject.next());
// 1. { value: "AB", done: false }

console.log(generatorObject.throw("Some error message"));
// 4. { value: "D", done: false }

console.log(generatorObject.next());
// 5. { value: undefined, done: true }
```

If we don't catch the error, then it "falls out" the generator into the calling code (`generatorObject.throw`); we can catch it there:

```js
function* generator() {
  const char = "A";
  yield char + "B"; // 2. Error is thrown in this line
  yield "C";
  yield "D";
  yield "E";
}

const generatorObject = generator();

console.log(generatorObject.next());
// 1. { value: "AB", done: false }

try {
  console.log(generatorObject.throw("Some error message"));
} catch (e) {
  console.log(e); // 3. Some error message
}

console.log(generatorObject.next());
// 4. { value: undefined, done: true }
```

## `generator.return`

`generator.return(value)` finishes the generator execution (`done: true`) and returns `value`

```js
function* generator() {
  yield "A";
  yield "B";
  yield "C";
}

const generatorObject = generator();

console.log(generatorObject.next());
// { value: "A", done: false }

console.log(generatorObject.return("foo"));
// { value: "foo", done: true }

console.log(generatorObject.next());
// { value: undefined, done: true }

console.log(generatorObject.return("bar"));
// { value: "bar", done: true }
```

## References

- https://javascript.info/generators
