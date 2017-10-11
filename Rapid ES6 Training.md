# Rapid ES6 Training

## New ES6 Syntax

### let, const and Block Scoping

* variable productId gets hoisted and set to undefined in the following example:

    ```javascript
    'use strict';
    console.log(productId);     //undefined
    var productId = 12;
    ```

* `let` bindings are not subject to Variable Hoisting, which means that `let` declarations do not move to the top of the current execution context.

* `let` allows you to declare variables that are limited in scope to the block, statement, or expression on which it is used. This is unlike the var keyword, which defines a variable globally, or locally to an entire function regardless of block scope.

* by using let we make sure variable declaration takes place before it gets used. so it lets avoid bugs and many developers use `let` instead of `var` now:

    ```javascript
    'use strict';
    console.log(productId);     // ReferenceError: productId is not defined
    let productId = 12;

    'use strict';
    let productId = 12;         // 12
    console.log(productId);

    'use strict';
    let productId;              // undefined
    console.log(productId);
    ```
* now we have block scoping in JavaScript and the variables declared inside the block get disappeared at the end of the block:

    ```javascript
    'use strict';
    let productId = 12;
    {
        let productId = 2000;
    }
    console.log(productId);     // 12

    'use strict';
    {
        let productId = 2000;
    }
    console.log(productId);     // Reference Error: productId is not defined

    'use strict';
    function updateProductId() {
        productId = 12;
    }
    let productId = null;
    updateProductId();
    console.log(productId);     // 12     the function is called after variable declaration

    'use strict';
    let productId = 42;
    for (let productId = 0; productId < 10; productId++) {
    }
    console.log(productId);     // 42
    ```

* Variables declared by `let` have as their scope the block in which they are defined, as well as in any contained sub-blocks. In this way, `let` works very much like var. The main difference is that the scope of a var variable is the entire enclosing function:

    ```javascript
    function varTest() {
    var x = 1;
    if (true) {
        var x = 2;          // same variable!
        console.log(x);     // 2
    }
    console.log(x);         // 2
    }

    function letTest() {
    let x = 1;
    if (true) {
        let x = 2;          // different variable
        console.log(x);     // 2
    }
    console.log(x);         // 1
    }
    ```

* in the following example a closure gets set over the variable `i` and at the end of the loop `i` is set to `2`. we can solve this with `let`:

    ```javascript
    'use strict';
    let updateFunctions = [];
    for (var i = 0; i < 2; i++) {
        updateFunctions.push(function () { return i; });
    }
    console.log(updateFunctions[0]());      // 2

    'use strict';
    let updateFunctions = [];
    for (let i = 0; i < 2; i++) {
        updateFunctions.push(function () { return i; });
    }
    console.log(updateFunctions[0]());      // 0
    ```

    **note:** A closure is an inner function that has access to the outer (enclosing) function's variables-scope chain. The closure has three scope chains: it has access to its own scope (variables defined between its curly brackets), it has access to the outer function's variables, and it has access to the global variables.

* The `const` declaration creates a read-only reference to a value. It does not mean the value it holds is immutable, just that the variable identifier cannot be reassigned.

* we should initialize constant variable when we declare them. we can't change it later:

    ```javascript
    'use strict';
    const MARKUP_PCT = 100;
    console.log(MARKUP_PCT);        // 100

    'use strict';
    const MARKUP_PCT;
    console.log(MARKUP_PCT);        // SyntaxError: Unexpected token ;

    'use strict';
    const MARKUP_PCT = 100;
    MARKUP_PCT = 10;
    console.log(MARKUP_PCT);        // TypeError: Assignment to constant variable.

    'use strict';
    const MARKUP_PCT = 100;
    if (MARKUP_PCT > 0) {
        const MARKUP_PCT = 10;
    }
    console.log(MARKUP_PCT);        // 100       due to block scoping
    ```

### Arrow Functions

* Two factors influenced the introduction of arrow functions:

    1. shorter functions:
        * in arrow functions we can leave off the `function` keyword
        * we don't need the `return` keyword for a single expression
        * if there is no input we use empty parentheses and when we have one argument we can leave off the parentheses:

        ```javascript
        'use strict';
        var getPrice = () => 5.99;
        console.log(typeof getPrice);       // function

        'use strict';
        var getPrice = () => 5.99;
        console.log(getPrice());            // 5.99

        'use strict';
        var getPrice = count => count * 4.00;
        console.log(getPrice(2));           // 8

        'use strict';
        var getPrice = (count, tax) => count * 4.00 * (1 + tax);
        console.log(getPrice(2, .07));      // 8.56

        'use strict';
        var getPrice = (count, tax) => {
            var price = count * 4.00;
            price *= (1 + tax);
            return price;
        }
        console.log(getPrice(2, .07));      // 8.56
        ```

    1. non-binding of `this`:
        * Until arrow functions, every new function defined its own `this` value (e.g. a new object in the case of a constructor, undefined in strict mode function calls, the base object if the function is called as an "object method", etc.)
        * An arrow function does not create its own this, the this value of the enclosing execution context is used.

        ```javascript
        // in this example document is calling the function
        // so 'this' is set to 'document'
        'use strict';
        document.addEventListener('click', function () {
            console.log(this);              // #document
        });

        'use strict';
        document.addEventListener('click', () => console.log(this));  // Window {...}
        });


        // in this example 'this' is being set to the object
        // that is calling the function (invoice)
        'use strict';
        var invoice = {
            number: 123,
            process: function () {
                console.log(this);      // Object{
            }                           //   number: 123
        };                              // }
        invoice.process();
        ```
