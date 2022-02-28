# Iteration protocols

- The iterable protocol
- The iterator protocol

## The iterable protocol

- 主要在描述 `@@iterator` method
- In order to be iterable, an object must implement the `@@iterator` (`Symbol.iterator`) method
- Calling the `@@iterator` method returns an "iterator", confirming to the iterator protocol
- The `@@iterator` method can be an ordinary function or a generator function

## The iterator protocol

- 主要在描述 iterator 與其 `next` method 所應具備之行為
- The iterator protocol defines a standard way to produce a sequence of values
- Iterator: an object that implements a `next` method

## References

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols
