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
        * Until arrow functions, every new function defined its own `this` value (e.g. a new object in the case of a constructor, `undefined` in strict mode function calls, the base object if the function is called as an "object method", etc.)
        * An arrow function does not create its own this, the this value of the enclosing execution context is used.

            ```javascript
            // in this example 'document' is calling the function
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
                    console.log(this);          // Object{
                }                               //   number: 123
            };                                  // }
            invoice.process();

            'use strict';
            var invoice = {
                number: 123,
                process: () => console.log(this)
            };
            invoice.process();                  // Window {...}

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

    'use strict';
    var baseTax = 0.07;
    var getTotal = function(price, tax = price * baseTax ) {
        console.log(price + tax);
    };
    getTotal(5.00);             // 5.35

    'use strict';
    var generateBaseTax = () => 0.07;
    var getTotal = function(price, tax = price * generateBaseTax() ) {
        console.log(price + tax);
    };
    getTotal(5.00);             // 5.35

    'use strict';
    var getTotal = function(price = adjustment, adjustment = 1.00) {
        console.log(price + adjustment);
    };
    getTotal();                 // SyntaxError: Use before declaration

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

    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(categories);
    };
    showCategories(123, 'search', 'advertising');   // ['search', 'advertising']

    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(categories);
    };
    showCategories(123);                            // []

    // length shows the number of parameters to the function, 
    // but it will ignore the rest parameter
    'use strict';
    var showCategories = function (productId, ...categories) {
    };
    console.log(showCategories.length);             // 1

    'use strict';
    var showCategories = function (productId, ...categories) {
        console.log(arguments.length);
    };
    showCategories(123, 'search', 'advertising');   // 3

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

    'use strict';
    var prices = [12, 20, 18];
    var newPriceArray = [...prices];
    console.log(newPriceArray);         // [12, 20, 18]

    'use strict';
    var newPriceArray = Array(...[,,]);
    console.log(newPriceArray);         // [undefined, undefined]

    'use strict';
    var newPriceArray = [...[,,]];
    console.log(newPriceArray);         // [undefined, undefined]

    'use strict';
    var maxCode = Math.max(..."43210");
    console.log(maxCode);               // 4

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

    'use strict';
    var field = 'dynamicField';
    var price = 5.99;
    var productView = {
        [field + "-001"]: price
    };
    console.log(productView);       // {dynamicField-001: 5.99}

    'use strict';
    var method = 'doIt';
    var productView = {
        [method + "-001"]() {
            console.log("in a method");
        }
    };
    productView['doIt-001']();      // in a method

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

    'use strict';
    var categories = [,,];
    for (var item of categories) {              // undefined
        console.log(item);                      // undefined
    }

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

    'use strict';
    var value = 0O10;
    console.log(value);             // 8
    ```

* we can get a binary value by replacing `o` with `b` or `B`:

    ```javascript
    'use strict';
    var value = 0b10;
    console.log(value);             // 2

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

    'use strict';
    let salary = ['32000', '50000'];
    let [ low, average, high ] = salary;
    console.log(high);                  // undefined

    'use strict';
    let salary = ['32000', '50000', '75000'];
    let [ low, , high ] = salary;
    console.log(high);                  // 75000

    'use strict';
    let salary = ['32000', '50000', '75000'];
    let [ low, ...remaining ] = salary;
    console.log(remaining);             // ["50000", "75000"]

    'use strict';
    let salary = ['32000', '50000'];
    let [ low, average, high = '88000' ] = salary;
    console.log(high);                  // 88000

    'use strict';
    let salary = ['32000', '50000', ['88000', '99000'] ];
    let [low, average, [actualLow, actualHigh]] = salary;
    console.log(actualLow);             // 88000

    'use strict';
    let salary = ['32000', '50000'];
    let low, average, high;
    [ low, average, high = '88000' ] = salary;
    console.log(high);                  // 88000

    'use strict';
    function reviewSalary([low, average], high = '88000') {
        console.log(average);
    }
    reviewSalary(['32000', '50000']);   // 50000

    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let { low, average, high } = salary;
    console.log(high);                  // 75000

    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let { low: newLow, average: newAverage, high: newHigh } = salary;
    console.log(newHigh);               // 75000

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

    'use strict';
    let salary = {
        low: '32000',
        average: '50000',
        high: '75000'
    };
    let newLow, newAverage, newHigh;
    ({ low: newLow, average: newAverage, high: newHigh } = salary);
    console.log(newHigh);               // 75000

    'use strict';
    let [maxCode, minCode] = 'AZ';
    console.log(`min: ${minCode} max: ${maxCode}`);     // min: Z max: A
    ```

### Advanced Destructuring

Examples:

```javascript
'use strict';
let [high, low] = [,];                        // high: undefined
console.log(`high: ${high} low: ${low}`);     // low: undefined

'use strict';                                 // Runtime Error: Unable to get property
let [high, low] = undefined;                  // 'Symbol.iterator' of undefined or null
console.log(`high: ${high} low: ${low}`);     // reference

'use strict';                                 // Runtime Error: Unable to get property
let [high, low] = null;                       // 'Symbol.iterator' of undefined or null
console.log(`high: ${high} low: ${low}`);     // reference

'use strict';
try {
  let [ high, low, ] = undefined;
}
catch (e) {
  console.log(e.name);                        // TypeError
}

'use strict';
let [ high, low, ] = [500, 200];
console.log(`high: ${high} low: ${low}`);     // high: 500 low: 200

'use strict';
for (let [a, b] of [[5, 10]]) {
  console.log(`${a} ${b}`);                   // 5 10
}

'use strict';
let count = 0;
for (let [a, b] of [[5, 10]]) {
  console.log(`${a} ${b}`);                   // 5 10
  count++;                                    // 1
}
console.log(count);

'use strict';
try {
  throw [123, 'message'];
}
catch ([invoiceNum, errorMessage]) {
  console.log(`${invoiceNum} ${errorMessage}`); // 123 message
}

'use strict';
function getResult() {
let high, low;
return { high, low } = { high: 500, low: 200 };
}
console.log(getResult());                     // {high: 500, low: 200}

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

