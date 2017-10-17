$x$
# Rapid ES6 Training

## New ES6 Syntax

### let, const and Block Scoping

* variable productId gets hoisted and set to `undefined` in the following example:

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
    ```
    ```javascript
    'use strict';
    let productId = 12;         // 12
    console.log(productId);
    ```
    ```javascript
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
    ```
    ```javascript
    'use strict';
    {
        let productId = 2000;
    }
    console.log(productId);     // Reference Error: productId is not defined
    ```
    ```javascript
    'use strict';
    function updateProductId() {
        productId = 12;
    }
    let productId = null;
    updateProductId();
    console.log(productId);     // 12     the function is called after variable declaration
    ```
    ```javascript
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
    ```
    ```javascript
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
    ```
    ```javascript
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
    ```
    ```javascript
    'use strict';
    const MARKUP_PCT;
    console.log(MARKUP_PCT);        // SyntaxError: Unexpected token ;
    ```
    ```javascript
    'use strict';
    const MARKUP_PCT = 100;
    MARKUP_PCT = 10;
    console.log(MARKUP_PCT);        // TypeError: Assignment to constant variable.
    ```
    ```javascript
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
            ```
            ```javascript
            'use strict';
            var getPrice = () => 5.99;
            console.log(getPrice());            // 5.99
            ```
            ```javascript
            'use strict';
            var getPrice = count => count * 4.00;
            console.log(getPrice(2));           // 8
            ```
            ```javascript
            'use strict';
            var getPrice = (count, tax) => count * 4.00 * (1 + tax);
            console.log(getPrice(2, .07));      // 8.56
            ```
            ```javascript
            'use strict';
            var getPrice = (count, tax) => {
                var price = count * 4.00;
                price *= (1 + tax);
                return price;
            }
            console.log(getPrice(2, .07));      // 8.56
            ```

    1. non-binding of `this`:
        * Until arrow functions, every new function defined its own `this` value (e.g. a new object in the case of a constructor, `undefined` in strict mode function calls, the base object if the function is called as an "object method", etc.)
        * An arrow function does not create its own this, the this value of the enclosing execution context is used.

            ```javascript
            // in this example 'document' is calling the function
            // so 'this' is set to 'document'
            'use strict';
            document.addEventListener('click', function () {
                console.log(this);              // #document
            });
            ```
            ```javascript
            'use strict';
            document.addEventListener('click', () => console.log(this));  // Window {...}
            });
            ```
            ```javascript
            // in this example 'this' is being set to the object
            // that is calling the function (invoice)
            'use strict';
            var invoice = {
                number: 123,
                process: function () {
                    console.log(this);          // Object{
                }                               //   number: 123
            };                                  // }
            invoice.process();
            ```
            ```javascript
            'use strict';
            var invoice = {
                number: 123,
                process: () => console.log(this)
            };
            invoice.process();                  // Window {...}
            ```
            ```javascript
            'use strict';
            var invoice = {
                number: 123,
                process: function () {
                    return () => console.log(this.number);
                }
            };
            invoice.process()();                // 123
            ```

        * we cannot `bind` a new object to an arrow function (JavaScript engine will ignore the bind). also calls to `call` and `apply` are going to be useless:

            ```javascript
            'use strict';
            var invoice = {
                number: 123,
                process: function () {
                    return () => console.log(this.number);
                }
            };
            var newInvoice = {
                number: 456
            };
            invoice.process().bind(newInvoice)();   // 123
            ```
            ```javascript
            'use strict';
            var invoice = {
                number: 123,
                process: function () {
                    return () => console.log(this.number);
                }
            };
            var newInvoice = {
                number: 456
            };
            invoice.process().call(newInvoice)();   // 123
            ```

        * we can't put the arrow symbol on a new line:

            ```javascript
            'use strict';
            var getPrice = ()
                    => 5.99;
            console.log(this.number);   // SyntaxError: unexpected token =>
            ```

        * we do not have access to prototype field when we declare an arrow function:

            ```javascript
            'use strict';
            var getPrice = () => 5.99;
            console.log(getPrice.hasOwnProperty("prototype"));  // false
            ```

### Default Function Parameters

* in ES5 if we didn't specify a parameter its value would be set to `undefined` but now we have the ability to set a default:

    ```javascript
    'use strict';
    var getProduct = function(productId = 1000) {
        console.log(productId);
    };
    getProduct();               // 1000
    ```

* if we pass `undefined` as parameter, JavaScript will use the default value if there is one:

    ```javascript
    'use strict';
    var getProduct = function(productId = 1000, type = 'software') {
        console.log(productId + ', ' + type);
    };
    getProduct(undefined, 'hardware');      // 1000, hardware
    ```

* when we are creating a default we have access to other parameters and the variables and functions that are in the context (outside of the function):

    ```javascript
    'use strict';
    var getTotal = function(price, tax = price * 0.07 ) {
        console.log(price + tax);
    };
    getTotal(5.00);             // 5.35
    ```
    ```javascript
    'use strict';
    var baseTax = 0.07;
    var getTotal = function(price, tax = price * baseTax ) {
        console.log(price + tax);
    };
    getTotal(5.00);             // 5.35
    ```
    ```javascript
    'use strict';
    var generateBaseTax = () => 0.07;
    var getTotal = function(price, tax = price * generateBaseTax() ) {
        console.log(price + tax);
    };
    getTotal(5.00);             // 5.35
    ```
    ```javascript
    'use strict';
    var getTotal = function(price = adjustment, adjustment = 1.00) {
        console.log(price + adjustment);
    };
    getTotal();                 // SyntaxError: Use before declaration
    ```
    ```javascript
    'use strict';
    var getTotal = function(price = adjustment, adjustment = 1.00) {
        console.log(price + adjustment);
    };
    getTotal(5.00);             // 6
    ```

* it's not best practice to use `arguments` within a function but it will refer to the number of arguments that are actually passed to the function

    ```javascript
    'use strict';
    var getTotal = function(price, tax = 0.07 ) {
        console.log(arguments.length);
    };
    getTotal(5.00);             // 1
    ```

* default parameters work even when we are creating a dynamic function:

    ```javascript
    'use strict';
    var getTotal = new Function("price = 20.00", "return price;");
    console.log(getTotal());    // 20
    ```

### Rest and Spread

* **Rest:** refers to gathering up parameters and putting them all into a single array

    ```javascript
    // here ... is the rest symbol. that will just gather
    // up all the remaining parameters to the function and
    // put them into categories
    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(categories instanceof Array);
    };
    showCategories(123, 'search', 'advertising');   // true
    ```
    ```javascript
    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(categories);
    };
    showCategories(123, 'search', 'advertising');   // ['search', 'advertising']
    ```
    ```javascript
    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(categories);
    };
    showCategories(123);                            // []
    ```
    ```javascript
    // length shows the number of parameters to the function,
    // but it will ignore the rest parameter
    'use strict';
    var showCategories = function (productId, ...categories) {
    };
    console.log(showCategories.length);             // 1
    ```
    ```javascript
    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(arguments.length);
    };
    showCategories(123, 'search', 'advertising');   // 3
    ```
    ```javascript
    'use strict';
    var showCategories = new Function("...categories", "return categories;");
    console.log(showCategories('search', 'advertising'));
    // ['search', 'advertising']
    ```

* **Spread:** refers to spreading out the elements of an array or a string

    ```javascript
    'use strict';
    var prices = [12, 20, 18];
    var maxPrice = Math.max(...prices);
    console.log(maxPrice);              // 20
    ```
    ```javascript
    'use strict';
    var prices = [12, 20, 18];
    var newPriceArray = [...prices];
    console.log(newPriceArray);         // [12, 20, 18]
    ```
    ```javascript
    'use strict';
    var newPriceArray = Array(...[,,]);
    console.log(newPriceArray);         // [undefined, undefined]
    ```
    ```javascript
    'use strict';
    var newPriceArray = [...[,,]];
    console.log(newPriceArray);         // [undefined, undefined]
    ```
    ```javascript
    'use strict';
    var maxCode = Math.max(..."43210");
    console.log(maxCode);               // 4
    ```
    ```javascript
    'use strict';
    var codeArray = ["A", ..."BCD", "E"];
    console.log(codeArray);             // ["A","B","C","D","E"]
    ```

### Object Literal Extensions

* in ES6 we don't have to specify both the key and the value for object literals:

    ```javascript
    'use strict';
    var price = 5.99, quantity = 30;
    var productView = {
        price,
        quantity
    };
    console.log(productView);           // {price: 5.99, quantity: 30}
    ```

* we no longer need to specify the `function` keyword to add a function to an object literal:

    ```javascript
    'use strict';
    var price = 5.99, quantity = 10;
    var productView = {
        price,
        quantity,
        calculateValue() {
            return this.price * this.quantity
        }
    };
    console.log(productView.calculateValue());  // 59.900000000000006
    ```
* just like the arrow function, `this` inside the function in object literals refer to the context; not referring to the object that contains the function:

    ```javascript
    'use strict';
    var price = 5.99, quantity = 10;
    var productView = {
        price: 7.99,
        quantity: 1,
        calculateValue() {
            return this.price * this.quantity
        }
    };
    console.log(productView.calculateValue());  // 59.900000000000006
    ```

* it is valid to have a space in property name. it just needs to wrap the name in `""` and access it with `[]` notation:

    ```javascript
    'use strict';
    var price = 5.99, quantity = 10;
    var productView = {
        price,
        quantity,
        "calculate value"() {
            return this.price * this.quantity
        }
    };
    console.log(productView["calculate value"]());  // 59.900000000000006
    ```

* we can create property names dynamically using a variable name or an entire expression as a field inside `[]`:

    ```javascript
    'use strict';
    var field = 'dynamicField';
    var price = 5.99;
    var productView = {
        [field]: price
    };
    console.log(productView);       // {dynamicField: 5.99}
    ```
    ```javascript
    'use strict';
    var field = 'dynamicField';
    var price = 5.99;
    var productView = {
        [field + "-001"]: price
    };
    console.log(productView);       // {dynamicField-001: 5.99}
    ```
    ```javascript
    'use strict';
    var method = 'doIt';
    var productView = {
        [method + "-001"]() {
            console.log("in a method");
        }
    };
    productView['doIt-001']();      // in a method
    ```
    ```javascript
    'use strict';
    var ident =
    'productId';
    var productView = {
        get [ident] () { return true; },
        set [ident] (value) { }
    };
    console.log(productView.productId);     // true
    ```

### for ... of Loops

* The `for...of` statement creates a loop iterating over iterable objects (including `Array`,`Map`, `Set`, `String`, `TypedArray`, arguments object and so on), invoking a custom iteration hook with statements to be executed for the value of each distinct property.

    ```javascript
    'use strict';
    var categories = ['hardware', 'software', 'vaporware'];     // hardware
    for (var item of categories) {                              // software
        console.log(item);                                      // vaporware
    }
    ```
    ```javascript
    'use strict';
    var categories = [,,];
    for (var item of categories) {              // undefined
        console.log(item);                      // undefined
    }
    ```
    ```javascript
    'use strict';
    var codes = "ABCDF";
    var count = 0;
    for (var code of codes) {
        count++;
    }
    console.log(count);             // 5
    ```

### Octal and Binary Literals

* octal values begin with number `0` followed by the letter `o` or `O` (would be better to use lowercase since it is next to a zero):

    ```javascript
    'use strict';
    var value = 0o10;
    console.log(value);             // 8
    ```
    ```javascript
    'use strict';
    var value = 0O10;
    console.log(value);             // 8
    ```

* we can get a binary value by replacing `o` with `b` or `B`:

    ```javascript
    'use strict';
    var value = 0b10;
    console.log(value);             // 2
    ```
    ```javascript
    'use strict';
    var value = 0B10;
    console.log(value);             // 2
    ```

### Template Literals

* by template literals we mean string templates with interpolated variable and expressions. Interpolation occurs when we run into `${}`:

    ```javascript
    'use strict';
    let invoiceNum = '1350';
    console.log(`Invoice Number: ${invoiceNum}`);   // Invoice Number: 1350
    ```
    ```javascript
    'use strict';
    let invoiceNum = '1350';
    console.log(`Invoice Number: \${invoiceNum}`);  // Invoice Number: ${invoiceNum}
    ```

* when we create a template literal, whitespace such as newlines and tabs are maintained:

    ```javascript
    'use strict';
    let message = `A                // A
    B                               // B
    C`;                             // C
    console.log(message);
    ```

* we are allowed to have expressions within `{}`:

    ```javascript
    'use strict';
    let invoiceNum = '1350';
    console.log(`Invoice Number: ${"INV-" + invoiceNum}`);
    // Invoice Number: INV-1350
    ```

* interpolation takes place first before a function call:

    ```javascript
    'use strict';
    function showMessage(message) {
        let invoiceNum = '99';
        console.log(message);
    }
    let invoiceNum = '1350';
    showMessage(`Invoice Number: ${invoiceNum}`);   // Invoice Number: 1350

    ```

* **Tagged Template Literals:** a more advanced form of template literals are tagged template literals. Tags allow you to parse template literals with a function. The first argument of a tag function contains an array of string values. The remaining arguments are related to the expressions. In the end, your function can return your manipulated string:

    ```javascript
    'use strict';
    function processInvoice(segments) {
        console.log(segments);          // ["template"]
    }
    processInvoice `template`;
    ```
    ```javascript
    'use strict';
    function processInvoice(segments, ...values) {
        console.log(segments);          // ["Invoice: ", " for ", ""]
        console.log(values);            // [1350, 2000]
    }
    let invoiceNum = '1350';
    let amount = '2000';
    processInvoice `Invoice: ${invoiceNum} for ${amount}`;
    ```

### Destructuring

* destructuring is a JavaScript expression that makes it possible to unpack values from arrays, or properties from objects, into distinct variables.

    ```javascript
    'use strict';
    let salary = ['32000', '50000', '75000'];
    let [ low, average, high ] = salary;
    console.log(average);               // 50000
    ```
    ```javascript
    'use strict';
    let salary = ['32000', '50000'];
    let [ low, average, high ] = salary;
    console.log(high);                  // undefined
    ```
    ```javascript
    'use strict';
    let salary = ['32000', '50000', '75000'];
    let [ low, , high ] = salary;
    console.log(high);                  // 75000
    ```
    ```javascript
    'use strict';
    let salary = ['32000', '50000', '75000'];
    let [ low, ...remaining ] = salary;
    console.log(remaining);             // ["50000", "75000"]
    ```
    ```javascript
    'use strict';
    let salary = ['32000', '50000'];
    let [ low, average, high = '88000' ] = salary;
    console.log(high);                  // 88000
    ```
    ```javascript
    'use strict';
    let salary = ['32000', '50000', ['88000', '99000'] ];
    let [low, average, [actualLow, actualHigh]] = salary;
    console.log(actualLow);             // 88000
    ```
    ```javascript
    'use strict';
    let salary = ['32000', '50000'];
    let low, average, high;
    [ low, average, high = '88000' ] = salary;
    console.log(high);                  // 88000
    ```
    ```javascript
    'use strict';
    function reviewSalary([low, average], high = '88000') {
        console.log(average);
    }
    reviewSalary(['32000', '50000']);   // 50000
    ```
    ```javascript
    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let { low, average, high } = salary;
    console.log(high);                  // 75000
    ```
    ```javascript
    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let { low: newLow, average: newAverage, high: newHigh } = salary;
    console.log(newHigh);               // 75000
    ```
    ```javascript
    // JavaScript compiler considers {} a block of code
    // so we get a syntax error for the destructuring
    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let newLow, newAverage, newHigh;
    { low: newLow, average: newAverage, high: newHigh } = salary;
    console.log(newHigh);               // Syntax Error
    ```
    ```javascript
    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let newLow, newAverage, newHigh;
    ({ low: newLow, average: newAverage, high: newHigh } = salary);
    console.log(newHigh);               // 75000
    ```
    ```javascript
    'use strict';
    let [maxCode, minCode] = 'AZ';
    console.log(`min: ${minCode} max: ${maxCode}`);     // min: Z max: A
    ```

### Advanced Destructuring

* Examples:

    ```javascript
    'use strict';
    let [high, low] = [,];                        // high: undefined
    console.log(`high: ${high} low: ${low}`);     // low: undefined
    ```
    ```javascript
    'use strict';                                 // Runtime Error: Unable to get property
    let [high, low] = undefined;                  // 'Symbol.iterator' of undefined or null
    console.log(`high: ${high} low: ${low}`);     // reference
    ```
    ```javascript
    'use strict';                                 // Runtime Error: Unable to get property
    let [high, low] = null;                       // 'Symbol.iterator' of undefined or null
    console.log(`high: ${high} low: ${low}`);     // reference
    ```
    ```javascript
    'use strict';
    try {
        let [ high, low, ] = undefined;
    }
    catch (e) {
        console.log(e.name);                        // TypeError
    }
    ```
    ```javascript
    'use strict';
    let [ high, low, ] = [500, 200];
    console.log(`high: ${high} low: ${low}`);     // high: 500 low: 200
    ```
    ```javascript
    'use strict';
    for (let [a, b] of [[5, 10]]) {
        console.log(`${a} ${b}`);                   // 5 10
    }
    ```
    ```javascript
    'use strict';
    let count = 0;
    for (let [a, b] of [[5, 10]]) {
        console.log(`${a} ${b}`);                   // 5 10
        count++;                                    // 1
    }
    console.log(count);
    ```
    ```javascript
    'use strict';
    try {
        throw [123, 'message'];
    }
    catch ([invoiceNum, errorMessage]) {
        console.log(`${invoiceNum} ${errorMessage}`); // 123 message
    }
    ```
    ```javascript
    'use strict';
    function getResult() {
        let high, low;
        return { high, low } = { high: 500, low: 200 };
    }
    console.log(getResult());                     // {high: 500, low: 200}
    ```
    ```javascript
    'use strict';
    let nums = {
        high: 1000,
        low: 20,
        average: 400
    };
    let high, low, average;
    ({ high, low } = { average } = nums);
    console.log(`${high} ${low} ${average}`);     // 1000 20 400
    ```

## ES6 Modules and Classes

for this section we use the following `HTML` for testing our examples. as you can see we use two scripts: one for `traceur` as our transpiler, and the other ES6 module loader to load base.js which is what we use in our examples:

```html
<!DOCTYPE html>
<html>
<head>
    <script src="traceur.min.js"></script>
    <script src="es6-module-loader-dev.js"></script>
<body>
    <script>
        System.import('./base.js');
    </script>
</body>
</head>
</html>
```

### Module Basics

* a module get executed once at its initial load:

    ```javascript
    // File base.js:
    console.log('in base.js');      // in base.js
    ```

* the ES6 module loader automatically puts the module in strict mode and that is part of the ES6 specification:

    ```javascript
    // File base.js:
    projectId = 99;                 // Runtime Error: Variable undefined
    console.log('in base.js');      // in strict mode
    ```

* `export` and `import` commands are the basic way to communicate between modules and share information:

    ```javascript
    // File base.js:
    import { projectId } from 'module1.js';
    console.log(projectId);         // 99

    // File module1.js:
    export let projectId = 99;
    ```
    ```javascript
    // File base.js:
    import { projectId, projectName } from 'module1.js';
    console.log(`${projectName} has id: ${projectId}`);

    // File module1.js:
    export let projectId = 99;              // BuildIt has id: 99
    export let projectName = 'BuildIt';
    ```
    ```javascript
    // File base.js:
    import { projectId as id, projectName } from 'module1.js';
    console.log(`${projectName} has id: ${id}`);

    // File module1.js:
    export let projectId = 99;              // BuildIt has id: 99
    export let projectName = 'BuildIt';
    ```
    ```javascript
    // File base.js:
    import { projectId as id, projectName } from 'module1.js';
    console.log(`${projectName} has id: ${projectId}`);

    // File module1.js:
    export let projectId = 99;     // Runtime error: projectId is undefined
    export let projectName = 'BuildIt';
    ```

* `import` statement gets hoisted:

    ```javascript
    // File base.js:
    console.log('starting in base');
    import { projectId } from 'module1.js';
    console.log('ending in base');

    // File module1.js:            // in module1
    export let projectId = 99;     // starting in base
    console.log('in module1');     // ending in base
    ```

* if we don't use `{}` when importing a variable, the default export gets imported:

    ```javascript
    // File base.js:
    import someValue from 'module1.js';
    console.log(someValue);
                                    // BuildIt
    export let projectId = 99;
    let projectName = 'BuildIt';
    export default projectName;
    ```
    ```javascript
    // File base.js:
    import someValue from 'module1.js';
    console.log(someValue);
                                    // undefined
    let projectId = 99;
    let projectName = 'BuildIt';
    export { projectId, projectName };
    ```
    ```javascript
    // File base.js:
    import someValue from 'module1.js';
    console.log(someValue);
                                    // 99
    let projectId = 99;
    let projectName = 'BuildIt';
    export { projectId as default, projectName };
    ```

* we can explicitly say that we want the default export and give it an alias name. We need to use `{}` again:

    ```javascript
    // File base.js:
    import { default as myProjectName } from 'module1.js';
    console.log(myProjectName);
                                    // BuildIt
    export let projectId = 99;
    let projectName = 'BuildIt';
    export default projectName;
    ```

* when we import everything with the `*` character we need to give it an alias and tha will become an object whose properties are all the exports (access with `values.projectId``values.projectName`):

    ```javascript
    // File base.js:
    import * as values from 'module1.js';
    console.log(values);
                                    // { projectId: 99, projectName: 'BuildIt'}
    let projectId = 99;
    let projectName = 'BuildIt';
    export { projectId, projectName };
    ```

### Name Exports in Modules

* the exports are usually referred to Named Exports when we export values and functions from a module

* we can't change the value of a named export variable, but if it's an object we can modify its properties:

    ```javascript
    // File base.js:
    import { projectId } from 'module1.js';
    projectId = 8000;
    console.log(projectId);
                                    // Runtime error: projectId is read-only
    export let projectId = 99;
    ```
    ```javascript
    // File base.js:
    import { project } from 'module1.js';
    project.projectId = 8000;
    console.log(project.projectId);
                                    // 8000
    export let project = {
        projectId: 99
    };
    ```

* when we import an object and modify it the two module stay in sync:

    ```javascript
    // File base.js:
    import { project, showProject } from 'module1.js';
    project.projectId = 8000;
    showProject();
    console.log(project.projectId);                 // 8000
                                                    // 8000
    export let project = { projectId: 99 };
    export function showProject() {
        console.log(project.projectId);
    };
    ```

* we can also import functions, but only the name of the function gets imported not the actual function. So if for that name the function changes, the imported name will call the new function:

    ```javascript
    // File base.js:
    import { showProject, updateFunction } from 'module1.js';
    showProject();
    updateFunction();                   // in original
    showProject();                      // in updated

    export function showProject() { console.log('in original'); }
    export function updateFunction() {
        showProject = function () { console.log('in updated'); };
    };
    ```

### Class Fundamentals

* The class syntax is not introducing a new object-oriented inheritance model to JavaScript. JavaScript classes provide a much simpler and clearer syntax to create objects and deal with inheritance (prototypes).

* You can think of a class as being somewhat a constructor function:

    ```javascript
    class Task {

    }
    console.log(typeof Task);                   // function
    ```
    ```javascript
    class Task {

    }
    let task = new Task();
    console.log(typeof task);                   // object
    ```
    ```javascript
    class Task {

    }
    let task = new Task();
    console.log(task instanceof Task);          // true
    ```

* methods in class don't need function keyword:

    ```javascript
    class Task {
        showId() {
            console.log('99');
        }
    }
    let task = = new Task();
    task.showId();                              // 99
    ```

* adding a method to a class is similar to adding a method to the prototype object:

    ```javascript
    class Task {
        showId() {
            console.log('99');                  // true
        }
    }
    let task = new Task();
    console.log(task.showId === Task.prototype.showId);
    ```

* we can also have constructors in a class and by creating an instance of the class the constructor is called:

    ```javascript
    class Task {
        constructor() {
            console.log('constructing Task');
        }
        showId() {
            console.log('99');
        }
    }
    let task = new Task();                      // constructing Task
    ```

* when we are working with object literals we would separate properties with comma, but we should leave those commas out when working with classes:

    ```javascript
    class Task {
        constructor() {
            console.log('constructing Task');
        },
        showId() {
            console.log('99');
        }
    }
    let task = new Task();                      // Syntax error
    ```

* we can't declare a property inside a class:

    ```javascript
    class Task {
        let taskId = 9000;
        constructor() {
            console.log('constructing Task');
        }
        showId() {
            console.log('99');
        }
    }
    let task = new Task();                      // Syntax error
    ```

* An important difference between function declarations and class declarations is that function declarations are hoisted and class declarations are not:

    ```javascript
    let task = new Task();                      // Error: Use before declaration
    class Task {
        constructor() {
            console.log('constructing Task');
        }
    }
    ```

* A class expression is another way to define a class. Class expressions can be named or unnamed

    ```javascript
    let newClass = class Task {
        constructor() {
            console.log('constructing Task');
        }
    };
    new newClass();                             // constructing Task
    ```

* an important difference between constructor function and class is that we can't call the `call` function to change 'this' object of the class

    ```javascript
    let Task = function () {
        console.log('constructing Task');
    };
    let task = {};
    Task.call(task);                            // constructing Task
    ```
    ```javascript
    class Task {
        console.log('constructing Task');
    };
    let task = {};                              // Error: class constructor cannot be
    Task.call(task);                            // called with the new keyword
    ```

* classes don't get placed on the window object, so we are not polluting the global namespace:

    ```javascript
    function Project() { };

    console.log(window.Project === Project);    // true
    ```
    ```javascript
    class Task { }

    console.log(window.Task === Task);          // false
    ```

### extends and super

* The `extends` keyword is used in class declarations or class expressions to set the prototype and create a class which is a child of another class.

    ```javascript
    class Project {
        constructor() {
            console.log('constructing Project');
        }
    }

    class SoftwareProject extends Project {
    }

    let p = new SoftwareProject();              // constructing Project
    ```
    ```javascript
    class Project {
        constructor(name) {
            console.log('constructing Project: ' + name);
        }
    }
    class SoftwareProject extends Project {
    }

    let p = new SoftwareProject('Mazatlan');    // constructing Project: Mazatlan
    ```

* The `super` keyword is used to call functions on an object's parent. Also, within a constructor or method of a class we can call `super` to explicitly access a function on the prototype

* When used in a constructor, the `super` keyword appears alone and must be used before the `this` keyword is used

* if an extended class needs to have a constructor it needs to call `super`. That's how JavaScript knows when to instantiate a new prototype object:

    ```javascript
    class Project {
        constructor() {
            console.log('constructing Project');
        }
    }
    class SoftwareProject extends Project {
        constructor() {
            super();
            console.log('constructing SoftwareProject');
            }
    }                                           // constructing Project
    let p = new SoftwareProject();              // constructing SoftwareProject
    ```

* even if the parent class doesn't have a constructor we still need to instantiate it as the prototype and it is done in the constructor of the child class by using `super`

    ```javascript
    class Project {
        //constructor() {
        // console.log('constructing Project');
        //}
    }
    class SoftwareProject extends Project {
        constructor() {
            //super();
            console.log('constructing SoftwareProject');
        }
    }
    let p = new SoftwareProject();              // ReferenceError: this is not defined
    ```

* the child class extends the parent class which becomes the prototype that does have the parent methods:

    ```javascript
    class Project {
    getTaskCount() {
    return 50;
    }
    }
    class SoftwareProject extends Project {
    }
    let p = new SoftwareProject();
    console.log(p.getTaskCount());              // 50
    ```
    ```javascript
    class Project {
        getTaskCount() {
            return 50;
        }
    }
    class SoftwareProject extends Project {
        getTaskCount() {
            return 66;
        }
    }
    let p = new SoftwareProject();
    console.log(p.getTaskCount());              // 66
    ```

* in the following example JavaScript engine is going to look in the prototype chain for a getTaskCount and it finds it in project:

    ```javascript
    class Project {
        getTaskCount() {
            return 50;
        }
    }
    class SoftwareProject extends Project {
        getTaskCount() {
            return super.getTaskCount() + 6;
        }
    }
    let p = new SoftwareProject();
    console.log(p.getTaskCount());              // 56
    ```

* we can use `super` with object literals when we set an object as the prototype for another object:

    ```javascript
    let project = {
        getTaskCount() { return 50; }
    };
    let softwareProject = {
        getTaskCount() {
        return super.getTaskCount() + 7;
        }
    }
    Object.setPrototypeOf(softwareProject, project);
    console.log(softwareProject.getTaskCount());    //57
    ```

### Properties for Class Instances

* we can initialize instance variables with the `this` keyword in the constructor of the class:

    ```javascript
    class Project {
        constructor() { this.location = 'Mazatlan'; }
    }
    class SoftwareProject extends Project {
        constructor() {
            super();
        }
    }
    let p = new SoftwareProject();
    console.log(p.location);                    // Mazatlan
    ```

* if we use `const`, `let`, or `var` for a variable it goes out of the scope instantly and it won't be attached to the instance:

    ```javascript
    class Project {
        constructor() { let location = 'Mazatlan'; }
    }
    class SoftwareProject extends Project {
        constructor() {
            super();
        }
    }
    let p = new SoftwareProject();
    console.log(p.location);                    // Mazatlan
    ```
    ```javascript
    class Project {
        constructor() { this.location = 'Mazatlan'; }
    }
    class SoftwareProject extends Project {
        constructor() {
            super();  // 'super' should be called before 'this'
            this.location = this.location + ' Beach';
        }
    }
    let p = new SoftwareProject();
    console.log(p.location);                    // Mazatlan Beach
    ```

### Static Members

* by declaring a `static` method, it gets attached directly to the class as a constructor function. We can access `static` methods using the class name (not the instance of the class):

    ```javascript
    class Project {
        static getDefaultId() {
            return 0;
        }
    }
    console.log(Project.getDefaultId());    // 0
    ```
    ```javascript
    class Project {
        static getDefaultId() {
            return 0;
        }
    }
    var p = new Project();                  // Error: Object doesn't support
    console.log(p.getDefaultId());          // property or method getDefaultId
    ```

* we do not have `static` properties:

    ```javascript
    class Project {
        static let id = 0;
    }
    console.log(Project.id);                // Syntax Error: ( expected
    ```

* another way of creating a `static` property is by attaching it directly to the class:

    ```javascript
    class Project {

    }
    Project.id = 99;
    console.log(Project.id);                // 99
    ```

### new.target

* The `new.target` property lets you detect whether a function or constructor was called using the new operator. In constructors and functions instantiated with the new operator, `new.target` returns a reference to the constructor or function. In normal function calls, `new.target` is `undefined`.

* In class constructors, `new.target` refers to the constructor that was directly invoked by `new`. This is also the case if the constructor is in a parent class and was delegated from a child constructor.

    ```javascript
    class Project {
        constructor() {
            console.log(typeof new.target);
        }
    }
    var p = new Project();          // function
    ```
    ```javascript
    class Project {
        constructor() {
            console.log(new.target);
        }
    }
    var p = new Project();          // constructor() { console.log(new.target); }

* it's useful when working with inheritance and extending classes. It is useful to refer back to the original class that's getting created

    ```javascript
    class Project {
        constructor() {
            console.log(new.target);
        }
    }
    class SoftwareProject extends Project {
        constructor() {
            super();
        }
    }
    var p = new SoftwareProject();      // constructor() { super(); }
    ```

* JavaScript will create the default constructor if we don't create it:

    ```javascript
    class Project {
        constructor() {
            console.log(new.target);
        }
    }
    class SoftwareProject extends Project {     // constructor(...args) {
    }                                           //     super(...args);
    var p = new SoftwareProject();              // }
    ```

* we can call the `static` functions of the class which is initialized using `new.target`:

    ```javascript
    class Project {
        constructor() {
            console.log(new.target.getDefaultId());
        }
    }
    class SoftwareProject extends Project {
        static getDefaultId() { return 99; }
    }
    var p = new SoftwareProject();              // 99
    ```

## New Types and Object Extensions

### Symbols

* this is a brand new type that doesn't even exist in ES5. The purpose of the `Symbol` is to generate a unique identifier, but as developers we never get access to that identifier

    ```javascript
    let eventSymbol = Symbol('resize event');
    console.log(typeof eventSymbol);            // symbol
    //
    ```
    ```javascript
    let eventSymbol = Symbol('resize event');
    console.log(eventSymbol.toString());        // Symbol(resize event)
    //
    ```

* one way to use symbols is to create a constant. We can guarantee that this constant will have a unique value

    ```javascript
    const CALCULATE_EVENT_SYMBOL = Symbol('calculate event');
    console.log(CALCULATE_EVENT_SYMBOL.toString());
    ```

* every time that we call `Symbol` even if it is a string that we have used before we get a unique symbol

    ```javascript
    let s = Symbol('event');
    let s2 = Symbol('event');
    console.log(s === s2);                      // false
    ```

* there is a built-in symbol registry and we get access to that by `Symbol.for`. So if we already have a registered symbol ('event' in the following example) that would be returned otherwise a new symbol will be generated:

    ```javascript
    let s = Symbol.for('event');
    console.log(s.toString());                  // Symbol(event)
    ```
    ```javascript
    let s = Symbol('event');
    let s2 = Symbol('event');
    console.log(s === s2);                      // true
    ```
    ```javascript
    let s = Symbol.for('event');
    let s2 = Symbol.for('event2');
    console.log(s === s2);                      // false
    ```

* we can get the human readable debugging string that was initially used to create the symbol by `Symbol.keyFor`:

    ```javascript
    let s = Symbol.for('event');
    let description = Symbol.keyFor(s);
    console.log(description);                   // event

    ```

* the majority of the cases that symbols are used for is as a property, whether in an object or in a class (to create a property using an expression we put it in `[]`):

    ```javascript
    let article = {
        title: 'Whiteface Mountain',
        [Symbol.for('article')]: 'My Article'
    };
    let value = article[Symbol.for('article')];
    console.log(value);                         // My Article
    ```

* we can't get access to properties that are created using `Symbol` by `getOwnPropertyNames` method. Instead we should use `getOwnPropertySymbols`:

    ```javascript
    let article = {
        title: 'Whiteface Mountain',
        [Symbol.for('article')]: 'My Article'
    };
    console.log( Object.getOwnPropertyNames(article) );         // ['title']
    ```
    ```javascript
    let article = {
        title: 'Whiteface Mountain',
        [Symbol.for('article')]: 'My Article'
    };
    console.log( Object.getOwnPropertySymbols(article) );       // [Symbol(article)]
    ```

### Well-known Symbols

* there are some symbols that are use for meta programming. Meta programming involves looking more deeply into objects, functions, and even how the JavaScript engine operates; and there are several symbols tha we can use to access some unique features of ES6. These symbols are called well-known symbols

* here is some examples of well-known symbols All these symbols are placed directly on the `Symbol` class:

    ```javascript
    et Blog = function () {
    };
    let blog = new Blog();
    console.log( blog.toString() );                 // [object Object]

    let Blog = function () {
    };
    Blog.prototype[Symbol.toStringTag] = 'Blog Class';
    let blog = new Blog();
    console.log( blog.toString() );                 // [object Blog Class]
    ```
    ```javascript
    let values = [8, 12, 16];
    console.log([].concat(values));                 // [8, 12, 16]

    let values = [8, 12, 16];
    values[Symbol.isConcatSpreadable] = false;
    console.log([].concat(values));                 // [ [8, 12, 16] ]
    ```
    ```javascript
    let values = [8, 12, 16];
    let sum = values + 100;
    console.log(sum);                               // 8,12,16100

    let values = [8, 12, 16];
    values[Symbol.toPrimitive] = function (hint) {
        console.log(hint);
        return 42;
    };
    let sum = values + 100;                         // default
    console.log(sum);                               // 142
    ```

### Object

* several new methods have been added to `Object`:

    ```javascript
    let article = {
        title: 'Whiteface Mountain',
        [Symbol.for('article')]: 'My Article'
    };
    console.log( Object.getOwnPropertySymbols(article) );       // [Symbol(article)]

    ```
    ```javascript
    let a = {
        x: 1
    };
    let b = {
        y: 2
    };
    Object.setPrototypeOf(a, b);
    console.log(a.y);                               // 2
    ```
    ```javascript
    let a = { a: 1 }, b = { b: 2 };
    let target = {};
    Object.assign(target, a, b);
    console.log(target);                            // {a: 1, b: 2}
    ```
    ```javascript
    let a = { a: 1 }, b = { a: 5, b: 2 };
    let target = {};
    Object.assign(target, a, b);
    console.log(target);                            // {a: 5, b: 2}
    ```
    ```javascript
    // property's enumerable should be set to true to work with assign
    let a = { a: 1 }, b = { a: 5, b: 2 };
    Object.defineProperty(b, 'c', {
        value: 10,
        enumerable: false
    });
    let target = {};
    Object.assign(target, a, b);
    console.log(target);                            // {a: 5, b: 2}
    ```
* `assign` method is not going to walk the prototype chain for properties:

    ```javascript
    let a = { a: 1 }, b = { a: 5, b: 2 }, c = { c: 20 };
    Object.setPrototypeOf(b, c);
    let target = {};
    Object.assign(target, a, b);
    console.log(target);
    ```

* `Object.is` is another way to compare now that fixes some issues with `===`:

    ```javascript
    let amount = NaN;
    console.log(amount === amount);                 // false

    let amount = NaN;
    console.log(Object.is(amount, amount));         // true
    ```
    ```javascript
    let amount = 0, total = -0;
    console.log(amount === total);                  // true

    let amount = 0, total = -0;
    console.log(Object.is(amount, total));          // false
    ```

### String Extensions

* examples:

    ```javascript
    let title = 'Santa Barbara Surf Riders';
    console.log(title.startsWith('Santa'));         // true

    let title = 'Santa Barbara Surf Riders';
    console.log(title.endsWith('Rider'));           // false

    let title = 'Santa Barbara Surf Riders';
    console.log(title.includes('ba'));              // true
    ```
    ```javascript
    // unicode astral plane values
    var title = "Surfer's \u{1f3c4} Blog";
    console.log(title);                             // Surfer's 🏄 Blog
    ```
    ```javascript
    var surfer = "\u{1f3c4}";                       // 2 (for emoji JavaScript engine still
    console.log(surfer.length);                     // considers its actual two symbols)

    var surfer = "\u{1f30a}\u{1f3c4}\u{1f433}";
    console.log(Array.from(surfer).length);         // 3
    console.log(surfer);                            // 🌊🏄🐳
    ```
    ```javascript
    var title = "Mazatla\u0301n";
    console.log(title + ' ' + title.length);        // Mazatlán 9

    var title = "Mazatla\u0301n";
    console.log(title + ' ' + title.normalize().length);    // Mazatlán 8

    var title = "Mazatla\u0301n";
    console.log(title.normalize().codePointAt(7).toString(16));     // 6e (unicode for á)
    ```
    ```javascript
    console.log(String.fromCodePoint(0x1f3c4));     // 🏄
    ```
    ```javascript
    let wave = '\u{1f30a}';
    console.log(wave.repeat(10));                   // 🌊🌊🌊🌊🌊🌊🌊🌊🌊🌊
    ```

* `String.raw` does the interpolation but it doesn't process any other characters in the string

    ``` javascript
    let title = 'Surfer';
    let output = String.raw`${title} \u{1f3c4}\n`;
    console.log(output);                            // Surfer \u{1f3c4}\n
    ```

### Number Extensions

* examples:

    ```javascript
    console.log(Number.parseInt === parseInt);      // true

    console.log(Number.parseFloat === parseFloat);  // true

    let s = 'NaN';
    console.log(isNaN(s));                          // true
    console.log(Number.isNaN(s));                   // false

    let s = '8000';
    console.log(isFinite(s));                       // true
    console.log(Number.isFinite(s));                // false

    let sum = 408.2;
    console.log(Number.isInteger(sum));             // false

    console.log(Number.isInteger(NaN));             // false
    console.log(Number.isInteger(Infinity));        // false
    console.log(Number.isInteger(undefined));       // false
    console.log(Number.isInteger(3));               // true
    ```

* a **safe integer** is an integer that can be accurately represented using floating point notation. Floating point values lose their precision after a certain amount of time and the highest and lowest integer that can be shown happen to be 2^53:

    ```javascript
    let a = Math.pow(2, 53) - 1;
    console.log(Number.isSafeInteger(a));           // true
    a = Math.pow(2, 53);
    console.log(Number.isSafeInteger(a));           // false

    console.log(Number.EPSILON);                    // 2.220446049250313e-16
    console.log(Number.MAX_SAFE_INTEGER);           // 9007199254740991
    console.log(Number.MIN_SAFE_INTEGER);           // -9007199254740991
    ```

### Math Extensions

* some extensions to math object:
  * **Hyperbolic Functions:**
    * `cosh()`
    * `acosh()`
    * `sinh()`
    * `asinh()`
    * `tanh()`
    * `atanh()`
    * `hypot()`

  * **Arithmetic Functions:**
    * `cbrt()`: cube root
    * `clz32()`: count leading zeros (32 bit integers)
    * `expm1()`: equal to `exp(x) - 1`
    * `log2()`: log base 2
    * `log10()`: log base 10
    * `log1p()`: equal to `log(x + 1)`
    * `imul()`: 32 bit integer multiplication

  * **Miscellaneous Functions:**
    * `sign()`: the number's sign: 1, -1, 0, -0, NaN
    * `trunc()`: the integer part of a number
    * `fround()`: round to nearest 32 bit floating-point value

* examples:

    ```javascript
    console.log(Math.sign(0));                      // 0
    console.log(Math.sign(-0));                     // -0 (0 in Edge)
    console.log(Math.sign(-20));                    // -1
    console.log(Math.sign(20));                     // 1
    console.log(Math.sign(NaN));                    // NaN

    console.log(Math.cbrt(27));                     // 3

    console.log(Math.trunc(27.1));                  // 27
    console.log(Math.trunc(-27.9));                 // -27
    ```

### RegExp Extensions

* for backward compatibility reasons there is a new flag (`u`) that we need to use if we are going to work with these astral plane unicode characters

    ```javascript
    let pattern = /\u{1f3c4}/;
    console.log(pattern.test('🏄'));                // false

    let pattern = /\u{1f3c4}/u;
    console.log(pattern.test('🏄'));                // true
    ```
    ```javascript
    // astral plane character is wrongly considered as 2 characters
    let pattern = /^.Surfer/;
    console.log(pattern.test('🏄Surfer'));          // false

    let pattern = /^.Surfer/u;
    console.log(pattern.test('🏄Surfer'));          // true
    ```

* the `y` flag performs the search from the last index and the last index only

    ```javascript
    let pattern = /900/y;
    console.log(pattern.lastIndex);                 // 0
    console.log(pattern.test('800900'));            // false

    let pattern = /900/y;
    pattern.lastIndex = 3;
    console.log(pattern.test('800900'));            // true

    let pattern = /900/yg;
    console.log(pattern.flags);                     // gy (Order will be "gimuy")
    ```

### Function Extensions

* the function has one important extension which is the `name` property that can be useful for logging, etc.

    ```javascript
    let fn = function calc() {
        return 0;
    };
    console.log(fn.name);                           // calc

    let fn = function() {
        return 0;
    };
    console.log(fn.name);                           // fn

    let fn = function() {
        return 0;
    };
    let newFn = fn;
    console.log(newFn.name);                        // fn
    ```
    ```javascript
    class Calculator {
        constructor() {
        }
        add() {
        }
    }
    let c = new Calculator();
    console.log(Calculator.name);                   // Calculator
    console.log(c.add.name);                        // add
    ```

* `Function.name` is not writable and we can't change it directly, however we can configure it with `Object.defineProperty()`

## Iterators, Generators and Promises

### Iterators

* arrays now have a property called `Symbol.iterator`. One of the major functions of symbols is to add properties to classes while guaranteeing that the property name is unique, so `Symbol.iterator` will be unique.

    ```javascript
    let ids = [9000, 9001, 9002];
    console.log(typeof ids[Symbol.iterator] );      // function

    let ids = [9000, 9001, 9002];
    let it = ids[Symbol.iterator]();
    console.log(it.next());                         // {done: false, value: 9000}

    let ids = [9000, 9001, 9002];
    let iter = ids[Symbol.iterator]();
    iter.next();
    iter.next();
    console.log(iter.next());                       // {done: false, value: 9002}

    let ids = [9000, 9001, 9002];
    let iter = ids[Symbol.iterator]();
    iter.next();
    iter.next();
    iter.next();
    console.log(iter.next());                       // {done: true, value: undefined}
    ```

* iterators are built into arrays but we can also create our own iterators:

    ```javascript
    let idMaker = {
        [Symbol.iterator]() {
            let nextId = 8000;
            return {
                next() {
                    return {
                        value: nextId++,
                        done: false
                    };
                }
            };
        }
    };
    let it = idMaker[Symbol.iterator]();
    console.log(it.next().value);                   // 8000
    console.log(it.next().value);                   // 8001
    ```

* `for ... of` are meant to work with iterators:

    ```javascript
    let idMaker = {
        [Symbol.iterator]() {
            let nextId = 8000;
            return {
                next() {
                    return {
                        value: nextId++,
                        done: false
                    };
                }
            };
        }
    };
    for (let v of idMaker) {                        // 8000
        if (v > 8002) break;                        // 8001
        console.log(v);                             // 8002
    }
    ```
    ```javascript
    let idMaker = {
        [Symbol.iterator]() {
            let nextId = 8000;
            return {
                next() {
                    let value = nextId>8002 ? undefined : nextId++;
                    let done = !value;
                    return { value, done };
                }
            };
        }
    };
    for (let v of idMaker) {                        // 8000
        console.log(v);                             // 8001
    }                                               // 8002
    ```

* spread operator works off of iterator:

    ```javascript
    let ids = [8000, 8001, 8002];
    function process(id1, id2, id3) {
        console.log(id3);                           // 8002
    }
    process(...ids);
    ```

### Generators

* Generator is a special type of function that doesn't run all the way through necessarily and it is able to yield and be called multiple times throughout all of your code. It doesn't exist on the stack the way most functions do and we actually use an iterator to call a generator multiple times

* when we run a generator the result is actually an iterator and it starts off in a pause state:

    ```javascript
    function *process() {
        yield 8000;
        yield 8001;
    }
    let it = process();
    console.log(it.next());                 // {done: false, value: 8000}
    ```
    ```javascript
    function *process() {
        yield 8000;
        yield 8001;
    }
    let it = process();
    it.next();
    console.log(it.next());                 // {done: false, value: 8001}
    ```
    ```javascript
    function *process() {
        yield 8000;
        yield 8001;
    }
    let it = process();
    it.next();
    it.next();
    console.log(it.next());                 // {done: true, value: undefined}
    ```
    ```javascript
    function *process() {
        let nextId = 7000;
        while(true)
            yield(nextId++);
    }
    let it = process();
    it.next();
    console.log(it.next().value);           // 7001
    ```
    ```javascript
    function *process() {
        let nextId = 7000;
        while(true)
            yield(nextId++);
    }
    for (let id of process()) {             // 7000
        if (id > 7002) break;               // 7001
        console.log(id);                    // 7002
    }
    ```

### Yielding in Generators

* we don't have to specify any values for yield:

    ```javascript
    function *process() {
        yield;
    }
    let it = process();
    console.log(it.next());                 // {done: false, value: undefined}
    ```

* We can pass an expression as yield value but we need to run `.next()` first to kick off the generator:

    ```javascript
    function *process() {
        let result = yield;
        console.log(`result is ${result}`);
    }
    let it = process();
    it.next();
    it.next(200);                           // result is 200
    ```
    ```javascript
    function *process() {
        let result = yield;
        console.log(`result is ${result}`);
    }
    let it = process();
    it.next();                              // result is 200
    console.log(it.next(200));              // {done: true, value: undefined}
    ```
    ```javascript
    function *process() {
        let newArray = [yield, yield, yield];
        console.log(newArray[2]);
    }
    let it = process();
    it.next();
    it.next(2);
    it.next(4);
    it.next(42);                            // 42
    ```
    ```javascript
    function *process() {
        let value = 4 * yield 42;
        console.log(value);
    }
    let it = process();
    it.next();
    it.next (10);                           // Syntax Error
    ```
    ```javascript
    function *process() {
        let value = 4 * (yield 42);
        console.log(value);
    }
    let it = process();
    it.next();
    it.next (10);                           // 40
    ```
    ```javascript
    function *process() {
        yield 42;
        yield [1,2,3];
    }
    let it = process();
    console.log(it.next().value);           // 42
    console.log(it.next().value);           // [1,2,3]
    console.log(it.next().value);           // undefined
    ```

* we can use iterator delegation (`yield*`) to take something that is iterable and temporarily replaces the iterator for process. Once that iterator is fully consumed the previous iterator will take over again:

    ```javascript
    function *process() {
        yield 42;
        yield* [1,2,3];
    }
    let it = process();
    console.log(it.next().value);           // 42
    console.log(it.next().value);           // 1
    console.log(it.next().value);           // 2
    console.log(it.next().value);           // 3
    console.log(it.next().value);           // undefined
    ```

### `throw` and `return`




    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```
    ```javascript
    ```








