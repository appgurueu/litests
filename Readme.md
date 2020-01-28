# Litest - Lite lit tests (`litest`)

An extremely small unit testing library.

## About

Litest is licensed under the MIT license and has been written by Lars Mueller alias LMD or appgurueu.

## Features

* Lightweight
* Maintainable code
* Good performance
* Easy to use
* Basic functionality

## API

The API **can read Lua 5.4** compatible Luon, but **writes Lua 5.1** compatible Luon.
Methods may raise errors for invalid objects (for instance functions) or invalid Luon.

### Import

```javascript
const litest=require("litest");
```

#### Expectations

Litest works using expectations - objects which verify whether given output is valid using `expectation.isValid(out)`.

###### `ValueExpectation`

* `constructor(value)`: Creates an expectation
* `isValid(out)`: Returns whether the given `out` equals the value
  
###### `AnyOfExpectation`

* `constructor(values)`: Creates ValueExpectations for each value
* `isValid(out)`: Checks if any value matches `out`

#### Testers

Testers carry out test series and display total results.

##### `Tester`

* `constructor(func)`: create a Tester for a function
* `test(input, expectation)`: test whether a certain expectation is met
* `testAll(tests, expectation_constructor)`: test whether expectations are met for all tests
  * if an object is passed, input are keys & out are values
  * if an array is passed, all i-th values are input and all (i+1)-th ones are desired output 
* `testEquals(input, value)`: shorthand for `test(input, new ValueExpectation(value))`
* `testEqualsAll(tests, value)`: also a shorthand - for `testAll(tests, ValueExpectation)`
* `passedTests()`: how many tests have passed is displayed

#### `BulkTester`

* `constructor(func)`: create a BulkTester for a namespace/object containing multiple functions

#### Example

[Luon](https://github.com/appgurueu/luon) uses Litest; below is an excerpt:

```javascript
const litest = require("litest.js");
const luon = require("./index.js");

new litest.BulkTester(luon).testEqualsAll([
    "read",
    {
        "true": true,
        "false": false,
        "nil": undefined,
        "'\\226\\130\\172'": "€",
        "'\\xF0\\x90\\x8D\\x88'": "𐍈", // two character utf 16
        /* UTF-8 escape sequences to UTF-16 conversion */
        "{a=1}": {
            a: 1
        },
        "{[ [[yay]]]=true}": {
            yay: true
        },
        "{   much    =   'spacing'   }": {
            much: "spacing"
        }
    },
    "readRemoveComments", {
        "10--comment": 10,
        "10--[[comment]]0": 100,
    },
    "removeCommentsText",
    {
        "some--comment": "some",
        "some--[[multi-line comment]]": "some",
        "some--[===[multi-line comment]===]": "some",
    },
    "removeSpacingText",
    {
        "some tests": "sometests",
        "'some tests'": "'some tests'",
        "[===[some tests]===]": "[===[some tests]===]"
    },
    "writeText",
    [
        [true, false, true, undefined], "{true,false,true,nil}",
        "€", '"€"'
    ],
    "writeCompressedText",
    [
        "test'test", '"test\'test"'
    ],
    "writeBeautifiedText",
    [
        [true, false, true, undefined], "{\n  true,\n  false,\n  true,\n  nil\n}",
        [
            [1, 2],
            [3, 4]
        ], "{\n  {\n    1,\n    2\n  },\n  {\n    3,\n    4\n  }\n}"
    ]
]);
```