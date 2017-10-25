# JavaScript Notes

## Pure functions in JavaScript

A pure function doesn’t depend on and doesn’t modify the states of variables out of its scope.

Concretely, that means a pure function always returns the same result given same parameters. Its execution doesn’t depend on the state of the system.

### Examples

Here we modify the attributes of the given object. Hence we modify the object which lies outside of the scope of our function: the function is impure:

```javascript
var values = { a: 1 };

function impureFunction ( items ) {
  var b = 1;

  items.a = items.a * b + 2;

  return items.a;
}

var c = impureFunction( values );
// Now `values.a` is 3, the impure function modifies it.
```

Now we simply modify the parameter which is in the scope of the function, nothing is modified outside:

```javascript
var values = { a: 1 };

function pureFunction ( a ) {
  var b = 1;

  a = a * b + 2;

  return a;
}

var c = pureFunction( values.a );
// `values.a` has not been modified, it's still 1
```

Here, b is not in the scope of the function. The result will depend on the context: surprises expected:

```javascript
var values = { a: 1 };
var b = 1;

function impureFunction ( a ) {
  a = a * b + 2;

  return a;
}

var c = impureFunction( values.a );
// Actually, the value of `c` will depend on the value of `b`.
// In a bigger codebase, you may forget about that, which may
// surprise you because the result can vary implicitly.
```

```javascript
var values = { a: 1 };
var b = 1;

function pureFunction ( a, c ) {
  a = a * c + 2;

  return a;
}

var c = pureFunction( values.a, b );
// Here it's made clear that the value of `c` will depend on
// the value of `b`. No sneaky surprise behind your back.
```
