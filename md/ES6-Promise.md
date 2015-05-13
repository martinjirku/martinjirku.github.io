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

# Executor function

The Promise will pass to the executor function two parameters, which are functions:

  - `resolve()`
  - `reject()`

These to function should be called and when an asynchronous event was resolved, or rejected.

The widely use approach used by jQuery.defered, angular.$q.defered (which is basically based on kriskowal's Q, search on github) is not compliant with ES6 specification. However angular uses the compliant version of promise as well (see [angular doc](https://docs.angularjs.org/api/ng/service/$q)).
My proposal as a good practice is a usage the ES6 compliant within the angular code as well, and not to use jQuery.defered version.
It is possible to send by the `resolve()` only one argument, however, we can send an array and in a `then()` method We can use `([ParameterOne, ParameterTwo, ParameterThree])=>{};` notation;

# Resources

  - [ECMAScript Language Specification - draft](http://people.mozilla.org/~jorendorff/es6-draft.html)
  - [Mozilla developer network - Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/prototype)
  - [html5rocks.com - promises] (http://www.html5rocks.com/en/tutorials/es6/promises/)
  - experience
