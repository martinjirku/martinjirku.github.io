# Promise

Promise in ES6 is an instrinsic Global object. It can be called only as constructor, not as a function (if so, it will throw an exception). Promise should be a subclassable object, so we can iuse the _extend_ clause of a class definition, however in a constructor of the new object we still need to call `super.call` to initialize instance and internal state of Promise.

# new Promise(executor)
New instance of Promise object will contain these methods and properties:

  - `catch(onReject)` - called on rejection
  - `constructor` - :) obvious
  - `then(onFulfilled, onRejected)` - if then is specified, then promis will be `this` value.[*](http://people.mozilla.org/~jorendorff/es6-draft.html#sec-promise.prototype.then)
  - PromiseState - possible values are: "pending", "fulfilled", and "rejected"
  - PromiseResult - the value with which promise has been fulfilled or rejected.

# Promise static methods

  - `all(iterable)` - run the callback when all promises in an Array are resolved
  - `reject(reason)` - rejects the promise
  - `resolve(value|promise|thenable)` - resolve the promise

# Resources

  - [ECMAScript Language Specification - draft](http://people.mozilla.org/~jorendorff/es6-draft.html)
  - [Mozilla developer network - Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/prototype)
  - [html5rocks.com - promises] (http://www.html5rocks.com/en/tutorials/es6/promises/)
