# should.js

[![Join the chat at https://gitter.im/shouldjs/should.js](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/shouldjs/should.js)

[![Build Status](https://travis-ci.org/shouldjs/should.js.svg?branch=master)](https://travis-ci.org/shouldjs/should.js)

[![Selenium Test Status](https://saucelabs.com/browser-matrix/shouldjs.svg)](https://saucelabs.com/u/shouldjs)

_should_ is an expressive, readable, framework-agnostic assertion library. The main goals of this library are __to be expressive__ and __to be helpful__. It keeps your test code clean, and your error messages helpful.

By default (when you `require('should')`) _should_ extends the `Object.prototype` with a single non-enumerable getter that allows you to express how that object should behave. It also returns itself when required with `require`.

It is also possible to use should.js without getter (it will not even try to extend Object.prototype), just `require('should/as-function')`. Or if you already use version that auto add getter, you can call `.noConflict` function.

**Results of `(something).should` getter and `should(something)` in most situations are the same**

### Upgrading instructions

Please check [wiki page](https://github.com/shouldjs/should.js/wiki/Breaking-changes) for upgrading instructions.

### FAQ

You can take look in [FAQ](https://github.com/shouldjs/should.js/wiki/FAQ).

## Example
```javascript
var should = require('should');

var user = {
    name: 'tj'
  , pets: ['tobi', 'loki', 'jane', 'bandit']
};

user.should.have.property('name', 'tj');
user.should.have.property('pets').with.lengthOf(4);

// If the object was created with Object.create(null)
// then it doesn't inherit `Object.prototype`, so it will not have `.should` getter
// so you can do:
should(user).have.property('name', 'tj');

// also you can test in that way for null's
should(null).not.be.ok();

someAsyncTask(foo, function(err, result){
  should.not.exist(err);
  should.exist(result);
  result.bar.should.equal(foo);
});
```
## To begin

 1. Install it:

    ```bash
    $ npm install should-without-global --save-dev
    ```

 2. Require it and use:

    ```js
    var should = require('should-without-global');

    (5).should.be.exactly(5).and.be.a.Number();
    ```

    ```js
    var should = require('should/as-function');

    should(10).be.exactly(5).and.be.a.Number();
    ```

 3. For TypeScript users:
  
    ```js
    import * as should from 'should-without-global';

    (0).should.be.Number();
    ```

## In browser

Well, even when browsers by complaints of authors have 100% es5 support, it does not mean it has no bugs. Please see [wiki](https://github.com/shouldjs/should.js/wiki/Known-Bugs) for known bugs.

If you want to use _should_ in browser, use the `should.js` file in the root of this repository, or build it yourself. To build a fresh version:

```bash
$ npm install
$ npm run browser
```

The script is exported to `window.should`:

```js
should(10).be.exactly(10)
```

You can easy install it with npm or bower:

```sh
npm install should-without-global -D
# or
bower install shouldjs/should.js
```

## API docs

Actual api docs generated by jsdoc comments and available at [http://shouldjs.github.io](http://shouldjs.github.io).

## Usage examples

Please look on usage in [examples](https://github.com/shouldjs/examples)

## .not

`.not` negates the current assertion.

## .any

`.any` allow for assertions with multiple parameters to assert any of the parameters (but not all). This is similar to the native JavaScript [array.some](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/some).

# Assertions
## chaining assertions

Every assertion will return a `should.js`-wrapped Object, so assertions can be chained.
To help chained assertions read more clearly, you can use the following helpers anywhere in your chain: `.an`, `.of`, `.a`, `.and`, `.be`, `.have`, `.with`, `.is`, `.which`. Use them for better readability; they do nothing at all.
For example:
```js
user.should.be.an.instanceOf(Object).and.have.property('name', 'tj');
user.pets.should.be.instanceof(Array).and.have.lengthOf(4);
```
Almost all assertions return the same object - so you can easy chain them. But some (eg: `.length` and `.property`) move the assertion object to a property value, so be careful.

## Adding own assertions

Adding own assertion is pretty easy. You need to call `should.Assertion.add` function. It accept 2 arguments:

1. name of assertion method (string)
2. assertion function (function)

What assertion function should do. It should check only positive case. `should` will handle `.not` itself.
`this` in assertion function will be instance of `should.Assertion` and you **must** define in any way this.params object
 in your assertion function call before assertion check happen.

`params` object can contain several fields:

- `operator` - it is string which describe your assertion
- `actual` it is actual value, you can assume it is your own this.obj if you need to define you own
- `expected` it is any value that expected to be matched this.obj

You can assume its usage in generating AssertionError message like: expected `obj`? || this.obj not? `operator` `expected`?

In `should` sources appeared 2 kinds of usage of this method.

First not preferred and used **only** for shortcuts to other assertions, e.g how `.should.be.true()` defined:

```javascript
Assertion.add('true', function() {
    this.is.exactly(true);
});
```
There you can see that assertion function do not define own `this.params` and instead call within the same assertion `.exactly`
that will fill `this.params`. **You should use this way very carefully, but you can use it**.

Second way preferred and i assume you will use it instead of first.

```javascript
Assertion.add('true', function() {
    this.params = { operator: 'to be true', expected: true };

    should(this.obj).be.exactly(true);
});
```
in this case this.params defined and then used new assertion context (because called `.should`). Internally this way does not
 create any edge cases as first.

```javascript
Assertion.add('asset', function() {
    this.params = { operator: 'to be asset' };

    this.obj.should.have.property('id').which.is.a.Number();
    this.obj.should.have.property('path');
})

//then
> ({ id: '10' }).should.be.an.asset();
AssertionError: expected { id: '10' } to be asset
    expected '10' to be a number

> ({ id: 10 }).should.be.an.asset();
AssertionError: expected { id: 10 } to be asset
    expected { id: 10 } to have property path
```

## Additional projects

* [`should-sinon`](https://github.com/shouldjs/sinon) - adds additional assertions for sinon.js
* [`should-immutable`](https://github.com/shouldjs/should-immutable) - extends different parts of should.js to make immutable.js first-class citizen in should.js
* [`should-http`](https://github.com/shouldjs/http) - adds small assertions for assertion on http responses for node only
* [`should-jq`](https://github.com/shouldjs/jq) - assertions for jq (need maintainer)
* [`karma-should`](https://github.com/seegno/karma-should) - make more or less easy to work karma with should.js
* [`should-spies`](https://github.com/shouldjs/spies) - small and dirty simple zero dependencies spies


## Contributions

[Actual list of contributors](https://github.com/visionmedia/should.js/graphs/contributors) if you want to show it your friends.

To run the tests for _should_ simply run:

    $ npm test

See also [CONTRIBUTING](./CONTRIBUTING.md).

## OMG IT EXTENDS OBJECT???!?!@

Yes, yes it does, with a single getter _should_, and no it won't break your code, because it does this **properly** with a non-enumerable property.

Also it is possible use it without extension. Just use `require('should/as-function')` everywhere.

## License

MIT. See LICENSE for details.
