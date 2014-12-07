# Assert

    Stability: 5 - Locked

此模块用于为程序编写单元测试，
通过 `require('assert')` 调用。

## assert.fail(actual, expected, message, operator)

抛出异常，控制台输出 `actual` 和 `expected` 的值，以 `operator` 分隔。

## assert(value, message), assert.ok(value, [message])

测试值是否为真，相当于 `assert.equal(true, !!value, message)`。

## assert.equal(actual, expected, [message])

测试是否相等, 相当于相等操作符 ( `==` )。

## assert.notEqual(actual, expected, [message])

测试是否不相等, 相当于不等操作符 ( `!=` )。

## assert.deepEqual(actual, expected, [message])

测试是否深度相等。

## assert.notDeepEqual(actual, expected, [message])

测试是否不深度相等。

## assert.strictEqual(actual, expected, [message])

Tests strict equality, as determined by the strict equality operator ( `===` )

## assert.notStrictEqual(actual, expected, [message])

Tests strict non-equality, as determined by the strict not equal operator ( `!==` )

## assert.throws(block, [error], [message])

Expects `block` to throw an error. `error` can be constructor, `RegExp` or
validation function.

Validate instanceof using constructor:

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      Error
    );

Validate error message using RegExp:

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      /value/
    );

Custom error validation:

    assert.throws(
      function() {
        throw new Error("Wrong value");
      },
      function(err) {
        if ( (err instanceof Error) && /value/.test(err) ) {
          return true;
        }
      },
      "unexpected error"
    );

## assert.doesNotThrow(block, [message])

Expects `block` not to throw an error, see `assert.throws` for details.

## assert.ifError(value)

Tests if value is not a false value, throws if it is a true value. Useful when
testing the first argument, `error` in callbacks.
