# Building Applications with React and Redux in ES6

## Environment Setup

* this course uses the following version:
  * **React**: 15.0.2
  * **Redux**: 3.5.2
  * **REACT/ROUTER**: 2.4.0
  * **webpack**: 1.13
  * **BABEL**: 6.*

* here is the link to this course's [starter kit](https://github.com/coryhouse/pluralsight-redux-starter)

* this is the link to [react-slingshot](https://github.com/coryhouse/react-slingshot) (starter kit)

## React Component Approaches

* there are at least four ways to create React components:
  * ES5 createClass
  * ES6 class
  * ES5 stateless function
  * ES6 stateless function
  * Many more ...

* ES5 createClass component example:

  ```javascript
  var HelloWorld = React.createClass({
    render: function () {
      return (
        <h1>Hello World</h1>
      );
    }
  });
  ```

* ES6 Class component example:

  ```javascript
  class HelloWorld extends React.Component {
    constructor(props) {
      super(props);
    }
    render() {
      return (
        <h1>Hello World</h1>
      );
    }
  }
  ```

* unlike ES5 createClass, ES6 Class does not autobind class' `this` to the functions. So we need to know how the context of 'this' changes depending on the caller.

* we can bind `this` to the function by calling `.bind` inline within the render function, but for performance reasons, it would be better to do this in the constructor

* ES5 stateless functional component example:

  ```javascript
  var HelloWorld = function(props) {
    return (
      <h1>Hello World</h1>
    );
  });
  ```

* ES6 stateless functional component example:

  ```javascript
  const HelloWorld = (props) => {
    return (
      <h1>Hello World</h1>
    );
  });
  ```

* in ES6 `var` keyword should be avoided and instead we should use `let` and `const`. Use `const` to make sure your component doesn't accidentally get reassigned

* we can use the `function` keyword instead of the arrow function

* there are 9 key benefits in using stateless functional component:
  1. there is no class needed
  1. avoid the `this` keyword
  1. enforced best practices: stateless functional components help programmatically enforce keeping our component pure. You're forced to put state management where it belongs in higher level container components
  1. high signal-to-noise ratio: it requires less typing so creates less noise
  1. enhanced code completion/intellisense: if you destructure your props in ES6 then all the data you use is now specified in a single simple function argument. This means you also get improved code completion and IntelliSense support compared to class-based components
  1. easy to understand: When you see a stateless component, you know it's simply a function that takes props and spits out HTML. Even if it contains a lot of markup, it's conceptually simple, it's a pure function. This leads to the next 
  1. with React's stateless functional components, each component can be easily tested in isolation. No mocking, state manipulations, special libraries, or tricky test harnesses are needed
  1. performance: stateless functional component will soon offer improved performance as well. Since there're no state or lifecycle methods to worry about, the React team plans to avoid unnecessary checks and memory allocations in future releases. So improved performance with superior syntax, testability, and readability

* When Should I Use Each Style?

  * **Classes**:
    1. **State:** You need to use a class component anytime that you need to utilize local state. You cannot have local state in a stateless functional component.
    1. **References:** Second, if you need to get a reference to the underlying DOM, then you'll need to use a class component. A stateless functional component doesn't actually create a component instance. So ref will always return `null` within a stateless component.
    1. **Lifecycle method:** if you need the lifecycle method hook, such as `componentWillMount`, `componentDidMount`, and so on, then you'll also need to use traditional class-based components
    1. **Child function:** although you can nest functions within functions in JavaScript, doing so on a stateless functional component can hurt performance; because every render creates a new instance of that function. So if you need to create child functions, then consider using a class component instead of arrow functions or declaring nested functions in your stateless component's render method.

  * **Stateless functions**: Everywhere else. If you don't need the things that are listed above, then use a stateless functional component

* two core types of React components are: **container** and **presentation components**

<center>

|Container|Presentation|
|--------:|:-------------|
|Little to no markup|Nearly all markup|
|Pass data and actions down|Receive data and actions via props|
|Knows about Redux|Doesn't know about Redux|
|Often stateful|Typically functional components|

</center>

* **Container** components are the components that will typically connect to our Redux store and then pass that data down to our child components
