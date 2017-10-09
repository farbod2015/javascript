# Rapid JavaScript Training

* `!!` is a common way to convert a value to its boolean equivalent: `!!value`, another ways is `Boolean(value)`
* string has properties and functions: 
  * `length` is property: `str.length`
  * `trim` is function: `str.trim()`
* Variables created without the keyword `var`, are always global, even if they are created inside a function.
* JavaScript code compiles twice. in the first pass it checks for variables, functions, etc. and initializes the variables with undefined and put them along with functions at the top of the code
* `undefined` and `null`:
   ```javascript
   typeof undefined = undefined
   typeof null = object     // empty object
   undefined == null        //  true
   undefined === null       //  false
   var level = undefined;   // ++level  ==>  NaN
   var level = null;        // ++level  ==>  1
   ```
* in the browser the global object is `Window` but in node it is global:
   ```javascript
   this === window          // true
   ```
   floating point calculations are inaccurate in JavaScript. for example: `5.1+3.3 = 8.399999999999`. so this comparison will result in false: `5.1+3.3 == 8.4`. to solve this problem when using floating point operations use `toFixed` function to round the number. Example: if `total = 5.1+3.3` then `total.toFixed(2)` is equal to `8.40`




#### addition:

```javascript
"PRD" + 2000          // "PRD2000"
2000 + "PRD"          // "2000PRD"
"2000" + "500"        // "2000500"
2000 + "500"          // "2000500"
"PRD" + undefined     // "PRDundefined"
2000 + null           // 2000    null is zero in numeric contexts
"PRD" + null          // PRDnull
2000 + NaN            // NaN
"PRD" + NaN           // "PRDNaN"
"300" - "200"         // 100
```



#### subtraction:

```javascript
"PRD300" - "ITEM200"     // NaN
300 - undefined          // NaN
300 - null               // 300
300 - obj                // 200          obj *  
300 - NaN                // NaN
300 - ""                 // 300
```

```javascript
// *:
var obj = {
    valueOf: function () { return 100; }
};
```

 

#### multiplication:

```javascript
20 * NaN           // NaN
20 * undefined     // NaN
20 * null          // 0
20 * NaN           // NaN
4 * obj            // 400         see above (obj) * 
4 * Infinity       // Infinity
-4 * Infinity      // -Infinity
4 * "XYZ"          // NaN
```



#### division:

```javascript
9 / 0        // Infinity
-9 / 0       // -Infinity
9 / "3"      // 3
```




#### modulus:

```javascript
9 % 4             // 1
9 % "  4  "       // 1
```


#### Boolean:

```javascript
!99                  // false
!0                   // true
!""                  // true
!"A"                 // false
!" "                 // false
!new Object          // false        new Object returns true
!null                // true
!undefined           // true
!NaN                 // true
obj && 99            // 99           if the first operand is an object the second operand is returned
obj && 0             // 0
obj && obj           // obj
null && 99           // null         null is returned if either of the operands in null
"Z" && null          // null
"Z" && undefined     // undefined
"Z" && NaN           // NaN          NaN is returned if either of the operands in NaN
false && ++value     //              value is not incremented because the second operand 
                     //              is ignored. if the first operand is true or false 
                     //              the second operand is not going to be looked at
obj || 99            //              if the first operand is an object that object is returned
```




#### Equality Operation:

```javascript
true == 2      // false (1 == 2)       if boolean value is compared with numbers, it
               //                      converts to 1 for true and 0 for false
42 == '42'     // true  (42 == 42)     if string is compare with numbers it converts 
               //                      to number
42 == obj      // true  (42 == 42)     if object is compared with number it converts 
               //                      to number (getting the value of valueOf property, 
               //                      if it doesn't have valueOf it get the value of 
               //                      toString function) *
null == 0      // false                comparing undefined/null to any values other 
               //                      than null/undefined will result in false
NaN == NaN     // false                                                         
55 === '55'    // false                the type is different                   
55 === 55      // true                                                         
false === 0    // false                                                        
55 !== 55      // false                                                        
```

```javascript
// *:
var obj = {
    valueOf: function () { return 42; }
}
```



#### Relational Operations:

```javascript
"beta" < "Alpha"                                // true
"Beta" < "alpha"                                // true     The uppercase characters always 
                                                //          come before the lowercase characters
"Beta".toLowerCase() < "alpha".toLowerCase()    // false
'42' < 55                                       // true
'42' < '142'                                    // false
```

------



## Reference Types:

1. Including: Array, Date, RegEx, Object
2. Arrays have properties (e.g. length) and functions (e.g. cat, slice, splice, etc.). one integer argument for Array constructor is for length but other than that initial elements of the array
3. Data constructor takes seconds (start time is Jan 1970) as argument

------



## Object:

1. two ways for creating objects:
  * using the constructor function for objects:
    ```javascript
    var project = new Object();
    ```
  * using JSON:
    ```javascript
    var project = {};
    ```
2. we can add new property to an object by:
  * simply specifying a name and a value pair on it
    ```javascript
    obj.name = 'phoenix'
    ```
  * using `defineProperty()` function
  * using `defineProperties()` function
3. two ways to access object property: 
  * use dot notation (`obj.name`)
  * use brackets with property name as string (`obj['name']`)
4. every object has prototype property but we don't always have access to it
  * prototype property is an object
  * when we create an object using JSON notation or new Object, our prototype for the object gets set to `Object.prototype`
  * every object have a prototype. The prototype is an object so it can have prototype too. so every time that a property or function is accessed for an object, if it does not have the property or function itself JavaScript will look at its prototype; and if the prototype doesn't have it'll look for the prototype of the prototype and so on.
  * we can use `Object.create(obj)` to create an object with obj as its prototype:
    ```javascript
    var project = {
      securityLevel: 2
    };
    var secretProject = Object.create(project); //project is set as the prototype for secretProject object
    ```
5. each property is paired with a descriptor:
  * Property descriptors present in objects come in two main flavors:
    * **Data descriptor**: is a property that has a value, which may or may not be writable
    * **Accessor descriptor**: is a property described by a getter-setter pair of functions.
  * A descriptor must be one of these two flavors; it cannot be both.
  * Both data and accessor descriptors are objects
  * The descriptor share the following required keys:
    * **configurable**: true if and only if the type of this property descriptor may be changed and if the property may be deleted from the corresponding object (defaults to false).
    * **enumerable**: true if and only if this property shows up during enumeration of the properties on the corresponding object (defaults to false).
      A data descriptor also has the following optional keys:
    * **value**: The value associated with the property. Can be any valid JavaScript value (number, object, function, etc) (defaults to undefined).
    * **writable**: true if and only if the value associated with the property may be changed with an assignment operator (defaults to false).
  * An accessor descriptor also has the following optional keys:
    get: A function which serves as a getter for the property, or undefined if there is no getter. The function return will be used as the value of the property (defaults to undefined).
    set: A function which serves as a setter for the property, or undefined if there is no setter. The function will receive as only argument the new value being assigned to the property (defaults to undefined).
  * A property can be defined by defineProperty function
    ```javascript
    var task = {};
    Object.defineProperty(task, {
      'text': {
        value: 'new task',
        writable: true
      }
    });
    ```
  * We can get the property descriptor by `Object.getOwnPropertyDescriptor` function:
    ```javascript
    var descriptor = Object.getOwnPropertyDescriptor(task, 'text');
    ```
6. Miscellaneous object functions:
  * `Object.prototype.hasOwnProperty()`: Returns a boolean indicating whether an object contains the specified property as a direct property of that object and not inherited through the prototype chain.
  * `Object.prototype.isPrototypeOf()`: Returns a boolean indicating whether the object this method is called upon is in the prototype chain of the specified object.

------



## Functions:

1. Naming function expression:
  * function expressions with no name appear as anonymous function in stack traces so it'll make it difficult for debugging. the solution is to assign a name to the function expression. so if the function throws an error the name will appear in stack trace.
  * if you want to create a recursive function and refer to the current function inside the function body, you need to create a named function expression.
2. Constructor Functions:
  * ES5 does not have classes but it has constructor functions
  * the name of these functions always start with capital letters (e.g. Object)
  * to create a constructor functions just set it to a regular function expression:
    ```javascript
    var Employee = function (name) {
      this.name = name;
    };
    var newEmployee = new Employee('John'); // newEmployee is an object of type Employee
    ```
  * within constructor function we can access the `this` keyword. `this` refers to the current object. think of `this` as referring to an object that gets passed into the function behind the scenes.
  * `new` creates an empty object an passes it to the function as `this`
  * the prototypes of all instances of a constructor function are identically equal (no duplicates). but if we create a function inside the constructor function for each instance a duplicate of that function is created. to avoid this we put all the functions inside the prototype
    ```javascript
    var Employee = function (name) {
      this.name = name;
    };
    Employee.prototype.giveRaise = function () {
    };
    ```
  * when working with JSON object we can't have access to the `.prototype` of the object directly (unless we use the hack `__proto__`), but in constructor functions we do have access (e.g. Employee.prototype)
  * constructor functions get an empty prototype unlike JSON objects that get the prototype of the Object constructor function
3. The `this` Keyword:
  * in the global space `this` is set to the object `Window` if we are working in a browser (Node works on a server so doesn't have the window object)
    ```javascript
    var name = 'Jeff';
    console.log(this.name); // we get 'Jeff'
    ```
  * when we call a function that has no object attached to it `this` inside that function refers to global (`Window`) object
    ```javascript
    var employee = {
      name: 'Jeff',
      updateSalary: function () {
        var fn = function () {
          console.log(this);
        };
        fn(); // this function call has no object attached this refers to Window object
      }
    };
    employee.updateSalary();
    ```
  * `new` creates a new object and assigns the new object to `this` inside the constructor function:
    ```javascript
    var Address = function (line1) {
      this.line1 = line1;
      console.log(this); // Address {line1:"123 State St."}
    };
    var addr = new Address('123 State St.');
    ```
4. Calling Functions:
  * we use `call` and `apply` to get control over what `this` is set to within a function
  * we can use the `call` function to pass an object to a function as its `this` object
    ```javascript
    var updateZipCode = function () {
      console.log(this);
    };
    updateZipCode.call(obj); 
    ```
  * if the function has arguments we pass to `call` the object first then the arguments, comma separated:
    ```javascript
    var updateZipCode = function (newZip, country) {
      console.log(newZip + ' ' + country);
    };
    var zipCode = {
      zip: '11787'
    };
    updateZipCode.call(zipCode, '11888', 'us'); 
    ```
  * `apply` does the same as `call` just instead of comma separated arguments it get an array of arguments:
    ```javascript
    updateZipCode.apply(zipCode, ['11888', 'us']); 
    ```
5. Closure:
  * Nested functions have access to variables declared in their outer scope.
  * A closure is a function having access to the parent scope, even after the parent function has closed. Functions normally get created, they have a lifecycle, and then they get garbage collected, but a closure is a function that persists and we can hold onto that function.
  * Common ways to use closure:
    1. where we return a function from another function. Example:
      ```javascript
      var salaryUpdater = function (salary) {
        var currentSalary = salary;
        var generator = function () {
          currentSalary += 10000;
          returncurrentSalary;
        };
        return generator;
      };
      varupdateFn = salaryUpdater(50000);        
      ```
      another example:
      ```javascript
        var add = (function () {
          var counter = 0;
          return function () {return counter += 1;}
        })();
      ```
    2. as an event handler
    3. as a set interval function
6. IIFEs: Immediately Invoked Function Expressions
  * it is a function that gets executed immediately
    ```javascript
    (function () {
      console.log('executed!');
    })(); // or }());
    ```
  * it prevents the code from polluding the global namespace
  * we can use unary operators insteat of parantheses:
    ```javascript
    +function () {
      console.log('executed!');
    }();
    ```
7. Recursion:
  * is a function's ability to call itself
  * the following example shows a common problem that happens in recursion:
    * this is the recursive function and the input object:
      ```javascript
      var orgChart = {
        name: 'Michael', subordinates: [
          {
            name: 'Andy', subordinates: [
              {
                name: 'Dwight', subordinates: []
              },
              {
                name: 'Kevin', subordinates: []
              }
            ]
          }
        ]
      };
      var fn = function (topEmployee) {
        console.log(topEmployee.name);
        for(var i = 0; i < topEmployee.subordinates.length; i++)
          fn(topEmployee.subordinates[i]);
      };
      ```
      using the functoin this would work fine:
      ```javascript
      fn(orgChart);
      ```
      but if we assign the function variable to another variable and assign something else to it and then call the function using the new variable:
      ```javascript
      var fn2 = fn;
      fn = null;
      fn2(orgChart);
      ```
      we get this error:
      ```javascript
      Michael
      Uncaught TypeError: fn is not a function
      ```
      to resolve this we should name the function and call this name recursively:
      ```javascript
      var fn = function showAllEmployees(topEmployee) {
        console.log(topEmployee.name);
        for(var i = 0; i < topEmployee.subordinates.length; i++)
          showAllEmployees(topEmployee.subordinates[i]);
      };
      ```

------



## Programming the BOM and DOM:

1. BOM stands for browser object model and that is a set of objects that the browser exposes to JavaScript
2. DOM is the document object model and that is the document that the browser is showing and it exposes objects to JavaScript so we can work with the document
3. The window Object and Timers:
  * some properties of the window object:
    * `window.screenLeft`
    * `window.screenTop`
    * `window.innerWidth`
    * `window.outerWidth`
    * `window.open`
  * timers are part of the browsers and can be accessed by the window object
  * timers mean the `setInterval` and the `setTimeout` function calls. these are global functions so don't need the `Window`.
    * ``setTimeout()``: Calls a function or executes a code snippet after specified delay.
    * ``clearTimeout()``: Clears the delay set by `setTimeout()`.
    * ``setInterval()``: Calls a function or executes a code snippet repeatedly, with a fixed time delay between each call to that function.
    * ``clearInterval()``: Cancels repeated action which was set up using `setInterval()`.
    * example:
      ```javascript
      console.log(new Date().getSeconds());
      var id = setTimeout(function () {
        console.log(new Date().getSeconds());
      }, 1000);
      clearTimeout(id);
      console.log(new Date().getSeconds());

      var id = setInterval(function () {
        var secs = new Date().getSeconds();
        console.log(secs);
        if (secs === 10)
          clearInterval(id);
      }, 1000);
      ```
4. System Dialogs:
  * These are the simple dialogs that the browser puts up that accept user input
  * They are very useful for testing, debugging, and utility applications 
  * These are styled differently in all the different major browsers, so if you wanted a consistent look across browsers, you wouldn't use these
  * the three system dialogs are:
    * `alert`:
      ```javascript
      alert('Hello World!');
      ```
    * `confirm`:
      ```javascript
      if (confirm('Delete EVERYTHING?!')) {
      console.log('You asked for it!')
      }
      else {
        console.log('Maybe next time...');
      } 
      ```
    * `prompt`:
      ```javascript
      var result = prompt('Your name?');
      console.log(result);
      ```
5. The location Object:
  * this if a property of the global Object which lets us look at the browser's URL location and we can also modify it as well
  * some location properties are:
    * `location.href`
    * `location.host`
    * `location.port` 
    * `location.protocol`
    * `location.assign`
6. Document Basics:
  * we can get access to HTML document using document property in JavaScript:
  * some properties and functions:
    * `document.getElementByID(...)`
    * `document.getElementsByTagName(...)`
    * `document.getElementsByClassName(...)`
    * `[element].getAttribute(...)`
    * `[element].setAttribute(...)`
    * `[element].childNodes`
7. Query Selectors:
  * Returns the first Element within the document that matches the specified selector, or group of selectors (CSS selectors separated by commas)
  * examples:
    ```javascript
    document.querySelector('article') // get element by ID
    document.querySelector('#article') // get element by ID
    document.querySelector('.special') // get element by class name
    document.querySelectorAll('.special') // get all elements by class name
    ```

------



## Event Handlers:

1. Events
  * User Interface Events: are fired by the browser or the user interacting with the -browser:
    * ``load``: when the page is loaded
    * ``unload``: when the page unloads
    * ``abort``: if loading gets interrupted
    * ``error``: if something goes wrong in the browser
    * ``select``: is use for selecting text by mouse, keyboard, or other devices
    * ``resize``: when the viewport resizes
    * ``scroll``: when the user scrolls the webpage
  * Mouse and Mouse Wheel Events: each of these have unique properties:
    * ``click``: for a mouse click or a touch on touch screen
    * ``dblclick``
    * ``mousedown``
    * ``mouseenter``: when the mouse cursor enters the rectangular area of the DOM element
    * ``mouseleave``: when the mouse cursor leaves the rectangular area of the DOM element
    * ``mousemove``
    * ``mouseout``
    * ``mouseover``
    * ``mouseup``
    * ``mousewheel``: to handle the mouse wheel
  * Keyboard and Text Events:
    * ``keydown``
    * ``keypress``
    * ``keyup``
    * ``textinput``: would be used on a text field on a form
  * Focus Events:
    * ``blur``: means a control has lost focus
    * ``focus``
    * ``focusin``
    * ``focusout``
  * HTML5 Events:
    * ``contexmenu``: for bringing up what would usually be a right click
    * ``beforeunload``: an event we can check to make sure the user really wants to unload a page
  * Touch Events:
    * ``touchstart``: where you put your finger down
    * ``touchmove``
    * ``touchend``
    * ``touchcancel``: when a touch is canceled before ending
2. The Event Object:
  * is an object created by the JavaScript runtime engine which gets passed to an event handler
  * Event object key properties: these are only readonly
    * ``bubbles``: true or false based upon whether or not this event is allowed to bubble up to the parents
    * ``Cancelable``: will let us know whether or not we can cancel any kind of propagation of this event
    * ``CurrentTarget``: is the DOM element that we're currently looking at
    * ``DefaultPrevented``: is a Boolean value telling us if prevent default was called
    * ``Detail``: holds extra information about the event
    * ``EventPhase``: will be 1, 2, or 3:
      * One means it's at the capture phase, which is the opposite of bubbling
      * Two means we're at the target
      * Three means that we're in the process of bubbling
    * ``preventDefault()``: to prevent the browser from executing any kind of default behavior, for example:
      * if we had a Submit button on a form, we could prevent that Submit button from actually doing the submission
      * if we had a link with an anchor tag, we can prevent the browser from following that link
    * ``stopImmediatePropagation()``: will stop all event capture and bubbling and will also make sure that no more handlers get called
    * ``stopPropagation()``: will stop all event capture and bubbling
    * ``target``: is the original target of this event, so if we click on a button and the event starts to bubble up from parent to parent to parent, target will be set to that original button
    * ``trusted``: will be true if the event comes from the browser and it will be false if the event is triggered through JavaScript code from a developer
    * type: is simply the name of the event, for example, it would be the string click for a click event
3. Handling events: 
  * we can add `on` to any event to use it (e.g. `onclick`)
  * it's best to put all the JavaScripts in a JavaScript file and put nothing in an HTML handler
  * example:
    ```html
    <!-- body of HTML -->
    <inputid="submit1" type="button"/>
    ```
    ```javascript
    // JavaScript file
    var button = document.getElementById('submit1');
    button.onclick = function () {
      console.log('ButtonClicked');
    }
    ```

4. Event Listeners: are the most modern way of handling events
  * Above example with event listener:
    ```html
    <!-- body of HTML -->
    <input id="submit1" type ="button" />
    ```
    ```javascript
    // JavaScript file
    var button = document.getElementById('submit1');
    button.addEventListener('click', function () {
      console.log('Button Clicked');
    });
    ```
  * we can work with multiple handlers on a single DOM element
  * we use `removeEventListener` to remove a handler function and we have to actually pass the handler function that we want to remove, so the handler function needs to have name:
    ```html
    <!-- body of HTML -->
    <input id="submit1" type ="button" />
    ```
    ```javascript
    // JavaScript file
    var button = document.getElementById('submit1');

    var submitHandler = function formSubmitHandler() {
      console.log('in formSubmitHandler()');
    };

    button.addEventListener('click', submitHandler);
    button.removeEventListener('click', submitHandler);
    ```

5. **Event propagation**: An overarching term that includes the three different phases of DOM Events (watch vido: https://youtu.be/BtOrr7oTH_8)
  * the phases are:
    * **Capturing Phase**: When an event occurs in the DOM, notification of the event is passed starting at the top of the DOM tree and passing down through all parent element nodes all the way to the target node where the event occurred.
    * **Target Phase**: After the capturing phase occurs, the Target phase occurs. The target phase only includes a notification of Node where the event took place.
    * **Bubbling Phase**: This is the final phase to occur, although many people think this is the first phase. In the bubbling phase a notice is passed from the target Node up through all of the parent Nodes all the way back to the top root of the DOM.
  * many times controls are going to overlap on a webpage. When using a listener, it's always important to know whether or not a parent is going to receive the event first before it's child (in the capturing phase) or if the child will receive first and then the parent (in the bubbling phase). To specify in which phase an element is going to receive the event we use the third argument of the `addEventListener` function. If it is true that means the element will receive the event in the capturing phase and if it is false it will receive it in the bubbling phase. We can also use `stopPropagation` function to stop the propagation and notifying any more parents/children:
    ```html
    <!-- body of HTML -->
    <div id="div1" style="width:200px; height:200px; background-color: blue;">
      <div id="div2" style="width:100px; height:100px; background-color: red;"></div>
    </div>
    ```
    ```javascript
    // JavaScript file
    var div1 = document.getElementById('div1');
    var div2 = document.getElementById('div2');
    var clickHandler = function divClickHandler(event) {
      console.log(this.id);
      event.stopPropagation();
      event.preventDefault();
    };
    div1.addEventListener('click', clickHandler, true);
    div2.addEventListener('click', clickHandler, true);
    ```


------



## Built-in Objects and Functions

1. Global Functions:
   * Parsing numbers:
       ```javascript
       parseInt('1234')        // 1234
       parseInt('b1234')       // NaN
       parseInt('12z34')       // 12      Stops at the first non-integer character
       parseInt('1234.9')      // 1234    No rounding. just stops at the decimal point
       parseInt(' ')           // NaN
       parseInt('C000', 16)    // 49152   base 16
       parseFloat('123.9')     // 123.9
       parseFloat('z123.9')    // NaN
       parseFloat('1239e-1')   // 123.9
       ```

   * `isFinite()`:
      ```javascript
      isFinite(Number.POSITIVE_INFINITY)       // false
      isFinite(42)                             // ture
      isFinite(9 / 0)                          // false
      ```

   * ​`inNaN()`:
      ```javascript
      isNaN(NaN)             // true
      isNaN(9 / 0)           // false    it is infinity
      ```

   * `encodeURI()`: encodes a URI by replacing each instance of certain characters by one, two, three, or four escape sequences representing the UTF-8 encoding of the character:
      ```javascript
      var path = '\\start\\';
      console.log(encodeURI(path));     // %5Cstart%5C

      var path = '\\start\\+';
      console.log(encodeURI(path));     // %5Cstart%5C+     does not encode '+' *
      ```
      \* `encodeURI` does not encode these characters: 
         `A-Z a-z 0-9 ; , / ? : @ & = + $ - _ . ! ~ * ' ( ) #`

   * `encodeURIComponent()`:
      ```javascript
      var path = '\\start\\+';
      console.log(encodeURIComponent(path));     // %5Cstart%5C+     encodes '+' *
      ```
      \* `encodeURI` does not encode these characters: 
         `A-Z a-z 0-9 - _ . ! ~ * ' ( )`

   * `decodeURI()`:
      ```javascript
      var path = '%5Cstart%5C+';
      console.log(decodeURI(path));     // \start\+
      ```

   * `decodeURIComponent()`:
      ```javascript
      var path = '%5Cstart%5C%2B';
      console.log(decodeURIComponent(path));     // \start\+
      ```

   * `eval()`: evaluates JavaScript code represented as a string:
      ```javascript
      var globalVar = 'foo';
      var code = 'console.log(globalVar)';
      eval(code);       // foo
      ```

      ​