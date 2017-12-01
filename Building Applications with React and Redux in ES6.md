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

## React Core Concepts

This section is from the following modules of *Building Applications with React and Flux*:

* React Core Concepts
* React: Creating Components Introduction
* React Lifecycle
* React Composition
* Flux

**Why react?**

* Fast
* Composable
* Pluggable
* Isomorphic Friendly
* Simple
* Battle Proven

### React and MVC

* React doesn't dictate how you handle data flows, routing or other concerns in your applications.
* It focuses solely on being a simple, composable component library.
* some people say that React is the V in MVC, but in actuality, well-composed React Components will fill both the V and the C in MVC
* controller views promote reuse and separation of concern

### JSX Introduction

* React offers an optional XML-like syntax, called JSX, for markup. It looks almost identical to HTML. Example:
  ```jsx
  var React = require('react');

  var AboutPage = React.createClass({
    render: function() {
      return (
        <div>
          <h1>About</h1>
          <p>This is a React and Flux demo project.</p>
        </div>
      );
    }
  });
  ```
* There are some minor differences, such as using `className` instead of class, and `htmlFor` instead of for.
* JSX compiles down to JavaScript
  ```jsx
  // JSX
  <h1 color="red">Headinghere </h1>

  // Compiled version
  React.createElement("h1", {color: "red"}, "Heading here")
  ```
* It is totally optional, but most people prefer JSX because it looks like the final HTML, which makes it easier to write, easier to read

* The following are the JSX and JavaScript varion of the same code:

  ```jsx
  var createAuthorRow = function(author) {
    return (
      <tr key={author.id}>
        <td>{author.id}</td>
        <td>{author.firstName} {author.lastName}</td>
      </tr>
    );
  };
  return (
    <table className="table">
      <thead>
        <th>ID</th>
        <th>Name</th>
      </thead>
      <tbody>
        {this.props.authors.map(createAuthorRow)}
      </tbody>
    </table>
  );
  ```
  ```javascript
  var createAuthorRow = function(author) {
    return (
      React.createElemet("tr", {key: author.id},
        React.createElemet("td", null, author.id),
        React.createElemet("td", null, author.firstName, " ", author.lastName)
      )
    );
  };
  return (
    React.createElemet("table", {className: "table"},
      React.createElemet("thead", null,
        React.createElemet("th", null, "ID"),
        React.createElemet("th", null, "Name")
      ),
      React.createElemet("tbody", null,
        {this.props.authors.map(createAuthorRow)}
      )
    )
  );
  ```

### HTML in JS: A Justification

* Angular, Ember or Knockout frameworks effectively put JavaScript in your HTML, so they're doing the exact opposite of JSX.
* Question: instead of effectively putting JavaScript in your HTML, why not put your HTML in JavaScript instead? And that's just what React does with JSX.
* In JavaScript, we enjoy clear error messages that point us toward the line number where the error occurred, but think about HTML. When you make a typo in HTML, things typically just silently fail.
* Since JSX is just JavaScript, when you make a typo, the JSX often won't even compile, but if you make these same mistakes in HTML-oriented frameworks, it's very tricky to track down. 
* JSX honors the philosophy of "Fail fast, fail loudly."

### Virtual DOM

* in today's Two-Way Binding libraries, like Angular or Knockout, when a piece of data changes, the UI is redrawn
* updating the DOM is an expensive operation and requires a lot of processing because redrawing a large section of the DOM is inefficient
* React recognized this problem, which is why they created an abstraction over the DOM that they call the Virtual DOM
* React's Virtual DOM compares the current state of the DOM to the new desired state and determines the most efficient way to update the DOM
* When you update the state of a component, the comparison happens automatically in memory, so it's typically very fast.
* This approach avoids layout thrashing, which is when a browser has to recalculate the position of everything when a DOM element changes.
* Being efficient is increasingly important in a world where more people are using mobile devices. Mobile devices vary widely in their CPU power and conserving battery life is a concern as well.
* You can use `shouldComponentUpdate` on your components to improve the performance even more by telling React not to update the DOM at all even when certain data changes. This is useful when you know the DOM shouldn't change even though the data just changed.
* And you can also consider using immutable data structures to make React even faster. Immutable data structures can't be changed, instead, a fresh copy is made when you need to change data. This makes React even faster because it can do reference comparisons.
* React also offers Synthetic Events. Synthetic Events abstract away browser-specific event quirks and allow React to optimize the performance of attaching event handlers behind the scenes.
* The Virtual DOM also allows React to render on the server, where no DOM exists at all, and because the DOM is abstracted away, React can even be used for implementing components in native applications using React Native.

### React: Creating Components Introduction

* `React.createClass` allows us define a class which contains our component
* `render` is the function that we need to define our component. It is required on any React component
* `render` function is where we put our JSX
* whatever the `render` function returns is what will display on the screen
* use parentheses when returning something with multiple lines
* example:

  ```javascript
  // homePage.js
  "use strict";

  var React = require('react');

  var Home = React.createClass({
    render: function() {
      return (
        <div className="jumbotron">
          <h1>Pluralsight Administration</h1>
          <p>React, React Router, and Flux for ultra-responsive web apps.</p>
        </div>
      );
    }
  });

  module.exports = Home;
  ```
  ```javascript
  // main.js
  $ = jQuery = require('jquery');
  var React = require('react');
  var Home = require('./components/homePage');

  React.render(<Home />, document.getElementById('app'));
  ```
  ```html
  <!-- index.html -->
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <title>Pluralsight Administration</title>
      <link rel="stylesheet" href="css/bundle.css" />
    </head>
    <body>
      <div id="app"></div>
      <script src="scripts/bundle.js"></script>
    </body>
  </html>
  ```

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

* unlike ES5 createClass, ES6 Class does not autobind class' `this` to the functions. So we need to know how the context of `this` changes depending on the caller.

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
|--------:|:-----------|
|Little to no markup|Nearly all markup|
|Pass data and actions down|Receive data and actions via props|
|Knows about Redux|Doesn't know about Redux|
|Often stateful|Typically functional components|

</center>

* **Container** components are the components that will typically connect to our Redux store and then pass that data down to our child components

## Intro to Redux

### Do I Need Redux?

Now as data flows get more complex, you may find yourself displaying the same data in multiple places. You may have a large number of potential state changes that are hard to manage. You may find it helpful to handle state changes in a single spot for consistency, testability, and, your own sanity. This is where React with Redux really shines. 

Flux and Redux are really useful for applications that have:

* **complex data flows:** when you're writing an app that merely displays simple static data then Redux isn't likely to be useful
* **Inter-component communication:**  if you need to handle interactions between two components that don't have a parent-child relationship, Redux offers a clear and elegant solution.
* **Non-hierarchical data:** when you find two disparate components are manipulating the same data, Redux becomes really helpful. This scenario is often the case when your application has non-hierarchical data.
* **Many actions:** as your application offers an increasing number of actions, the structure and scalability of Redux can become really useful.
* **Same data used in multiple places:** the most obvious sign that you'll want something like Redux is if you're utilizing the same data in multiple places. If your components need to utilize the same data and they don't have a simple parent-child relationship, Redux helps solve that problem.

### Three Core Redux Principles

Redux has three core principles:

1. **One immutable store:** all your application state is placed in a single immutable store (the state can't be changed). Having one immutable store aids debugging, supports server rendering, and makes things like undo/redo easily possible.
1. **Actions trigger changes:** in Redux, the only way to mutate state is to emit an action, which describes a user's intent.
1. **Reducers update state:** state is changed by pure functions that are called reducers. In Redux, a reducer is just a function that _accepts the current state_ in an action and _returns a new state_.

### Redux Flow Overview

Let's look at how actions, reducers, the store, and container components will interact to create unidirectional data flows:

* An action describes user intent. It's an object with a type property and some data. You could pass multiple separate pieces of data here or in one or more objects:
  ```js
  { type: RATE_COURSE, rating: 5}
  ```
* This action will ultimately be handled by a reducer. A reducer is just a fancy name for a function that returns new state. So as you can see, the reducer receives the current state and an action, and then it returns a new state. Reducers typically contain a switch statement that checks the type of the action passed. This determines what new state should be returned
  ```js
  function appReducer(state = defaultState, action) {
    switch(action.type) {
      case RATE_COURSE:
      // return new state
    }
  }
  ```
* Once this new state is returned from a reducer, the store is updated. React re-renders any components that are utilizing the data. Your React components are connected to the store using React-Redux.

## Actions, Stores, and Reducers

### Actions

* In Redux, the events happening in the application are called actions.
* Actions are just plain objects containing a description of an event:
  ```js
  { type: RATE_COURSE, rating: rating }
  ```
* An action must have a type property. The data could be a complex object, a simple number, a Boolean, any value that's serializable. The only things that you shouldn't try passing around in your actions are things that won't serialize to JSON like functions or promises.
* Actions are made by convenience functions called action creators. Here, the action creator is called `RATE_COURSE`. Typically, the action creator has the same name as the action's type:
  ```js
  rateCourse(rating) {
    return { type: RATE_COURSE, rating: rating }
  }
  ```
* Action creators are considered convenience functions because they're not required. But I recommend following this simple convention. By using these action creators to create your actions, the spot where you dispatched the action does not need to know the action creator structure.

### Store

* In Redux, you create a store by calling createStore in your application's entry point. You pass the createStore function to your reducer function:
  ```js
  let store = createStore(reducer);
  ```
* The Redux store simply stores data while reducers, which we'll discuss in a moment, handle the state changes.
* There's only one store in Redux. Having a single source of truth makes the application easier to manage and understand.
* The Redux store API is very simple:
  * The store can dispatch an action: `store.dispatch(action)`
  * subscribe to a listener: `store.subscribe(listener)`
  * return its current state: `store.getState()`
  * replace a reducer: `replaceReducer(nextReducer)`
* This last feature is useful to support hot reloading.
* The most interesting omission here is that there is no API for changing data in the store. It means the only way that you can change the store is by dispatching an action and you can't change it directly.
* The store doesn't actually handle the actions that you dispatch. Actions are ultimately handled by reducers.

### What Is Immutability?

* if I can't mutate state, doesn't that mean that no data can ever change? Not at all. It just means that instead of changing your state object, you must return a new object that represents your application's new state.
* Some types of JavaScript are immutable already, such as `number`, `string`, `Boolean`, `undefined`, and `null`. In other words, every time you change the value of one of these types, a new copy is created.
* Mutable JavaScript types are things like `object`s, `array`s, and `function`s.
* Do I have to build a new copy of state by hand every time I want to change it? Thankfully, no. * There are some easy ways to create copies of objects in JavaScript. The approach I recommend is Object.assign:
  ```none
  Signature
  Object.assign(target, ...sources)
  ```
  ```js
  // Example
  Object.assign({}, state, {role: 'admin'});
  ```
* Also `Object.assign` is part of ES6, it's a feature that Babel can't transpile. So be sure that you include babel-polyfill at the root of your app like we did in the environment setup module.

### Why Immutability?

* There are three core benefits to having immutable state: clarity, performance, and what I like to call awesome sauce:
  1. **Clarity:** Redux's centralized immutable store means any time state changes in a Redux app, you know where and how it occurred. You know someone wrote some code in a reducer that returned a new copy of state. It means you're clear about what file to open to actually see state changes.
  1. **Performance:** If state were mutable, Redux would have to do an expensive operation to check every single property on your state object to determine if state had actually changed. But if state is immutable, Redux can simply do a reference comparison. If the old state isn't referencing the same object in memory, then React-Redux knows that the state has changed and notifies React of state changes. It uses the `shouldComponentUpdate` method to quickly bail out if nothing has changed. React-Redux includes a variety of complex performance optimizations behind the scenes that rely on immutable state.
  1. **Awesome Sauce (Amazing Debugging Experience):** Time travel debugging is a powerful way to see exactly how your application state is changing over time. This means that you can travel through time as you debug. So you can go back in history and see each specific state change as it occurred. And as you go back in time, you can undo specific state changes and see how that changes the final state. You can even turn off individual actions that occurred so you can see what the state would look like if a specific action in history had never happened. And, finally, you can play all your interactions back with the click of a button and even select the speed at which it plays back.

### Handling Immutability

* There are many ways to handle immutability for arrays but the most popular ways are:
  * `Object.assign`
  * the spread operator
* JavaScript doesn't have immutable data structures built in, so how do we enforce immutability? There are three approaches to consider:
  1. the simplest way is to just educate your team and trust them.
  1. If you want to put in a safety net, you can install `redux-immutable-state-invariant`. This library displays an error when you try to mutate state anywhere in your app.. However, one important note: Be sure you only run this in development because it does a lot of object copying, which would degrade performance in production.
  1. If you want to programmatically enforce immutability, you can consider a library like `immutable.js`. It creates immutable JavaScript data structures. This library also happens to be by Facebook but can be useful on any project.
* So we'll just use `redux-immutable-state-invariant` in this course.










### Reducers

* To change the store, you dispatch an action that is ultimately handled by a reducer.
* A reducer is a function that takes state and an action and returns new state:
  ```js
  function myReducer(state, action) {
    // return new state based on action passed
  }
  ```
* Here's an example of a reducer that's handling incrementing a counter:
  * The following version is wrong because it is mutating the state at `state.couter++`:
    ```js
    function myReducer(state, action) {
      switch(action.type) {
        case 'INCREMENT_COUNTER':
          state.counter++;
          return state;
      }
    }
    ```
  * Here is the correct example:
    ```js
    function myReducer(state, action) {
      switch(action.type) {
        case 'INCREMENT_COUNTER':
          return (Object.assign(
            {},
            state,
            {counter: state.counter + 1}
          ));
      }
    }
    ```
  * Reducers must be pure functions. This means they should produce no side effects. You know you have a pure function if calling it with the same set of arguments always returns the same value.
  * Because reducers are supposed to be pure functions, there are three things that you should never do in a reducer:
    1. mutate arguments
    1. perform side effects: like API calls and routing transitions
    1. call non-pure functions: a reducer's return value should depend solely on the values of its parameters. It shouldn't call non-pure functions like `date.now` or `math.random`.
  * it's technically possible to create multiple stores when working in Redux, but it's not recommended and only useful in rare instances. In short, assume you can only have one store when working in Redux. Only try creating another after very carefully investigating the implications.
  * When the store is created, Redux calls the reducers and uses their return values as initial state.
  * If we have multiple reducers, all of them get called when an action is dispatched. The switch statement inside each reducer looks at the action type to determine if it has anything to do.
  * it's important that all reducers return untouched state as the default if no switch case matched the action type passed.
  * the Redux FAQ recommends using reducer composition. This means a given action can be handled by more than one reducer.
  * Write independent small reducer functions that are each responsible for updates to a specific slice of state. We call this pattern 'reducer composition.' A given action could be handled by all, some, or none of them.

## Connecting React to Redux

### Container vs. Presentational Component

There are two component types in React:

<center>

|Container|Presentation|
|--------:|:-----------|
|Focus on how things work|Focus on how things look|
|Aware of Redux|Unaware of Redux|
|Subscribe to Redux State|Read data from `props`|
|Dispatch Redux actions|Invoke callbacks on `props`|
|Generated by `react-redux`|Written by hand|

</center>

### React-redux introduction

* React-Redux ties your React components together with Redux. It consists of two core items:
  1. **The Provider component**: attaches app to store
  1. **The `connect` function**: creates container components

* **The Provider component**:
  * is utilized at your application's root
  * it wraps your the application. This is how the Provider component attaches your app to the Redux store.
    ```js
    <Provider store={this.props.store}>
      <App/>
    </Provider>
    ```
  * Provider component uses React's context to make the store available to all of your container components in the application without having to pass it explicitly. You only need to use it once when you `render` the root component

* **`connect` function**:
  * container components aren't typically created by hand, Instead, they're created by React-Redux
  * The `connect` function that's provided by React-Redux generates the container components
  * This function wraps a component so it's connected to the Redux store
    ```js
    export default connect(
      mapStateToProps,
      mapDespatchToProps
    )(AuthorPage)
    ```
  * With this function, we can declare what parts of the store we'd like attached to our component as `props`. And we declare what actions we want to expose on `props` as well.

* React-Redux's `connect` function accepts two parameters, both of which are functions
  both of these parameters are optional:
  1. `mapStateToProps`: specifies the state that you want to expose to your component
  1. `mapDespatchToProps`: specifies the actions you'd like to expose

* Redux can give you performance improvements behind the scenes. It will make sure that your component only renders when the specific data you've connected changes. This helps avoid wasteful unnecessary re-renders and saves you from having to manually suppress renders via shouldComponentUpdate.

### mapStateToProps

* The first parameter in `connect` function is `mapStateToProps`:
  ```js
  connect(mapStateToProps, mapDispatchToProps)
  ```
* This function is useful for defining what part of the Redux store you want to expose on your component.
* When you define this function, the component will **subscribe** to Redux store updates. Any time it updates, `mapStateToProps` will be called.
* This function returns an object. Each property on the object you define will become a property on your container component.
* `mapStateToProps` function determines what state is available on your container component via `props`
* in the following example, this.props.appstate within the component has access to any state that is handled by appstate reducer:
  ```js
  function mapStateToProps(state) {
    return {
      appState: state
    }
  };
  ```
* If I only want to expose part of the store's state to the component, we can specify that specific pieces of state in the `return` section of the function and each object will become a prop on the component
* One important thing to note is every time the component is updated, the `mapStateToProps` function is called. So if you're doing something expensive in `mapStateToProps`, you'll want to use a library like `Reselect` for **memoizing**:
  * **Memoizing**: memoization is like caching for function calls. Each time a function is called,Reselect just checks whether it's already been called with the specified parameters, and if it has, it doesn't call the function. Instead, it just returns the memoized value
  * This is useful for increasing performance by avoiding unnecessary expensive operations
 
### mapDispatchToProps

* The second function that we pass to Connect is `mapDispatchToProps`
* This function lets us specify what actions we want to expose as `props`
* The `mapDispatchToProps` function receives `dispatch` as its lone parameter and it returns the **callback props** that you want to pass down:
  ```js
  function mapDispatchToProps(dispatch) {
    return {
      actions: bindActionCreators(actions, dispatch)
    };
  }
  ```
* `bindActionCreators` function is part of Redux

* Handling `mapDispatchToProps`: there are three ways to handle mapping your actions to props in Redux container components (this is simply a decision about how you want to expose your actions to your components):
  1. The first option is to ignore it since `mapDispatchToProps` is an optional parameter on the Connect function. When you omit it, then the `dispatch` function will be attached to your container component (props). This means you can call `dispatch` manually and pass it an action creator:
      ```js
      this.props.dispatch(loadCourses());
      ```
      It has tow downsides:
        * First, it requires more boilerplate each time you want to fire off an action because you have to explicitly call `dispatch` and pass it the action you'd like to fire.
        * Second, this means your child components need to reference Redux-specific concepts like the `dispatch` function, as well as your action creators. If you want to keep your child components as simple as possible and avoid tying them to Redux, then this approach is not ideal.

  1. The second option is to manually wrap your action creators in `dispatch` calls within the `mapDispatchToProps` function:
      * Here, we are wrapping `loadCourses` action creator in a function that calls `dispatch`.
      ```js
      function mapDispatchToProps(dispatch) {
        return {
          loadCourses: () => {
            dispatch(loadCourses());
          }
        };
      }

      //In component:
      this.props.loadCourses();
      ```
      * Compared to option 1, it keeps the calls in my actual component shorter at the cost of some extra coding here in `mapDispatchToProps`.

  1. Finally, you can use the `bindActionCreators` function, which is a convenience function that wraps your action creators in dispatch calls for you. `BindActionCreators` basically does what we're doing in option 2 automatically.
      ```js
      function mapDispatchToProps(dispatch) {
        return {
          actions: bindActionCreators(actions, dispatch)
        };
      }

      //In component:
      this.props.actions.loadCourses();
      ```

* The bottom line is approaches 2 and 3 both produce the same result. They wrap your actions in a `dispatch` call so that they're easy to pass down to child components. But there's a notable advantage to options 2 and 3 over option 1, and that is that with options 2 and 3, your child components don't have to know anything about Redux. Child components can simply call the actions that are passed down to them via props. Remember, with option 1, we had to import action creators into our child components so that we could call Redux's `dispatch` directly.

### A Chat with Redux

I find it helpful to think about the major players as people with different roles who interact with each other. Here's an example conversation that I played through my head:

* <span style="color:STEELBLUE">**Reach:**</span> Hey, CourseAction, someone clicked this "Save Course" button.
* <span style="color:darkred">**Action:**</span> thanks, React! I'll update an action so reducers that care can update state.
* <span style="color:YELLOWGREEN">**Reducer:**</span> Ah, thanks action. I see you passed me the current state and the action to perform. I'll make a new copy of state and return it.
* <span style="color:ORANGERED">**Store:**</span> Thank you for updating the state, Mr. Reducer. I'll make sure that all connected components are aware.
* <span style="color:DARKBLUE">**React-Redux:**</span> Whoa, thanks for the new data, Mr. Store. I'll now intelligently determine if I should tell React about this change so that it only has to bother with updating the UI if it's necessary.
* <span style="color:STEELBLUE">**React:**</span> Ooh! Shiny new data has been passed down via props from the store! I'll update the UI to reflect this!

And that's how data flows through Redux in a unidirectional manner.

## Redux Flow

* we can initialize state for the form in the `constructor` and set it later using `setState`.

### Binding in ES6

* in the following example, state isn't defined in `onTitleChange` because of the `this` context that's getting passed

  ```js
  // CoursesPage.js

  class CoursesPage extends React.Component {
    constructor(props, context) {
      super(props, context);

      this.state = {
        course: { title: "" }
      };
    }

    onTitleChange (event) {
      const course = this.state.course;
      course.title = event.target.value;
      this.setState({course: course});
    }

    onClickSave() {
      alert(`Saving ${this.state.course.title}`);
    }

    render() {
      return (
        <div>
          <h1>Courses</h1>
          <h2>Add Course</h2>
          <input
            type="text"
            onChange={this.onTitleChange}
            value={this.state.course.title} />

          <input
            type="submit"
            value="Save"
            onClick={this.onClickSave} />
          </div>
      );
    }
  }
  ```

* when you use React's createClass function, then functions are autobound for you, so you didn't have to use bind in instances like this. However, React doesn't autobind in ES6 classes, so we have to handle binding ourselves.
* The issue in this case is that the `this` context is currently wrong in our change handler. Our function is inheriting the `this` context of the caller, which in this case is the change handler.
* Every time that a change event occurs on the `input` (first `input` in the `render` function), it is passing the `this` context of the `input` over to our change handler for `onTitleChange`, and that's why `this` in `onTitleChange` function is not the `this` that we're expecting.
* It needs to be bound to the instance of our component. One way to fix it is doing the bind within `render`. we can say `this.onTitleChange.bind(this)`. This approach does work but the downside to this is performance. Every time that you do a bind in `render`, you're impacting performance because using bind in `render` causes a new function to be created on each `render`.
* For performance, we should avoid defining new functions in our `render` function. Instead, it's best to place your bind calls up here in the `constructor`.
* So to fix the issue, we can bind the `this` context up in our constructor. All we have to do is binding the `onTitleChange` and `onClickSave` to the `this` of our CoursesPage component. This will solve our problem.

  ```js
    constructor(props, context) {
      super(props, context);

      this.state = {
        course: { title: "" }
      };

      this.onTitleChange = this.onTitleChange.bind(this);
      this.onClickSave = this.onClickSave.bind(this);
    }
  ```

### React State vs. Redux State: When and Why?

Just about any app you write needs a way to manage state. Redux manages state and state transformations and is often used with React, but React has its own concept of state.

* **The Differences**:
  * React state is stored locally within a component. When it needs to be shared with other components, it is passed down through props. In practice, this means that the top-most component in your app needing access to a mutable value will hold that value in its state. If it can be mutated by subcomponents, you must pass a callback to handle the change into subcomponents.
  * When using Redux, state is stored globally in the Redux store. Any component that needs access to a value may subscribe to the store and gain access to that value. Typically, this is done using container components. This centralizes all data but makes it very easy for a component to get the state it needs, without surrounding components knowing of its needs.

* In [You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367), Redux author Dan Abromov argues that Redux is not always necessary when using React, despite the two often being used together.
* Assuming that your app does use Redux, you need to determine what parts of the state are stored in the Redux store, and what parts are stored in React component state. In this post, I’ll lay out a few of the factors that I use to determine where state will live.

* **Duration**:
  * Different pieces of state are persisted for different amounts of time:
    * **Short term**: data that will change rapidly in your app
    * **Medium term**: data that will likely persist for a while in your app
    * **Long term**: data that should last between multiple visits to your site
  * **Short-term state**: I think of short-term data as data that will likely change rapidly and may represent only part of an atomic change to the state. At the simplest level, this includes the characters present in a text field as the user types. I would also extend this to include data in an entire form; this data can be considered a work in progress until the form is submitted. **I like to store this type of data in local React state.**
  * **Medium-term state**: By medium-term state, I mean state that will stick around while the user navigates your app. This could be data loaded from an API, or any changes that you want to be persisted up until a page refresh. After submitting a form, **I would store the state in the Redux store**. As an example, if a user submits a form updating their profile information, it would make sense to store this in Redux.
  * **Long-term state**: This is state that should be persisted between refreshes of the page or between separate visits to the page. Since the Redux store will be created anew when refreshing, this type of data should be stored somewhere else—likely to a database on a server or into local storage in the browser.

* **Breadth of Use**: Another consideration is how many components in your React app need to access the state. The more state needs to be shared between different components in your app, the more benefit there is to using the Redux store. If the state feels isolated to a specific component or a small part of your app, React state may be a better option:
  * **Depth of passing down props**: React state should be stored in the most top-level component for which a subset of its subcomponents will need access to the state. Sometimes, this can mean many layers between the component storing the state and the subcomponents that render the data, and each layer means another prop must be propagated down the virtual DOM.
  This works great if you only need to pass the state down one or two levels into the subcomponents, but beyond that, it can feel tedious and require edits to many components every time you find a subcomponent needs access to new state. In cases like this, I find it much easier to store the state in Redux and use a container component to pluck the desired data from the store.
  * **Unrelated components needing the same state**: It could also be that multiple, relatively unrelated components need access to the same state. Take the above example of a form to update a user’s profile. The component here should receive the initial user profile in its props. You might also have a header component with a subcomponent that displays the user’s username or related data.
  You could, of course, take passing down props to the extreme, in which your top-level component knows about the user’s profile and passes it to the header, which passes it to a subcomponent (and maybe further), and also passes it down to the profile-editing component. However, this requires a lot of management between the whole virtual DOM.
  This type of solution is solved much more cleanly by storing the profile in the Redux store, and allowing container components around the header and profile-editing component to grab data from Redux’s store.
  * **Ability to Track Changes to the State**: Another time to choose Redux is when you need to track changes to state. Maybe you want to replay events. Maybe you want to implement undo/redo in your app. Maybe you just want to log how state is changing.
  In cases like these, Redux is a great solution; each action that is created is an artifact of how the state changes. Redux makes all these tasks simpler by centralizing them in a single store.

### Actions

* we can put our actions in a separate file in a separate folder called actions.
* action creators are exported functions that can accepts a payload.
* inside the function we define our action. Our action is an object that has a property of `type`. This `type` property is required:

  ```js
  // courseActions.js

  export function createCourse(course) {
    return { type: 'CREATE_COURSE', course};
  }
  ```

* in this example we had just hard coded the string, although we will move this out to a constant later.
* this function is called an action creator because it creates actions.
* The rest of the action's shape can be whatever works best for you. For this action, I'm just passing `course` data but I could have as many properties as I want.
* note that we are simply saying `course` here because in ES6, we can omit the right-hand side if it matches the left-hand side (`course: course`).

### Reducers

* Now that we've created our action, we need a function that will handle that action, and that's where reducers come in. A reducer is just a function that accepts a state and an action and then returns a new state.
* to set our initial state, we can use the default parameters feature that's part of ES6 (in this example an array):

  ```js
  // courseReducer.js
  
  export default function courseReducer(state = [], action) {
    switch(action.type) {
      case types.CREATE_COURSE:
        return [
          ...state,
          Object.assign({}, action.course)
        ];

      default:
        return state;
    }
  }
  ```

* the most common approach to structure inside the reducer is a `switch` statement
* in our example the only case that we're going to have to handle is CREATE_COURSE the type that we hardcoded
* any time you create a switch statement, it's a good idea to have a default because we could have multiple reducers that are handling different actions and if this reducer doesn't happen to handle the action, then we should just return the state.
* we use the ES6 spread operator on our existing state, and then use `Object.assign`, and pass it our target object, and then the `course` that's passed on our action.

### Root Reducer

* Redux supports multiple reducers, and most apps will end up using more than one reducer.
* traditionally the rootReducer is called `index.js` in `reducers` folder.
* we're going to reference a function that comes from Redux called `combineReducers` and inside we define all of the reducers that we're wanting to combine for our application:

  ```js
  // index.js

  import {combineReducers} from 'redux';
  import courses from './courseReducer';

  const rootReducer = combineReducers({
    courses     // shorthand property name (instead of 'courses: courses')
  });

  export default rootReducer;
  ```

* it would be better if we use the word *Reducer* in the filename of the reducers (e.g. `courseReducer`) so that it would be clear by the name that we are looking at a reducer file. But since the exported reducer is a `default`, we can alias it however we want. This is important because on the `rootReducer`, the name that we supply for reducer (the property) will impact the way that we access this state throughout the application. So in the container components, we'll be saying `state.courses` instead of `state.courseReducer`, which doesn't read as well. So I recommend thinking carefully about the property name that you choose.

### Store

* In Redux, there's a single store. When creating a store, it's useful to define a function that configures the store. We'll call this function at our application's entry point. This way, the store is configured when the app starts up.
* so we'll create a folder called `store` in our source directory and inside, we'll create a file called `configureStore.js` to configure our Redux store:

  ```js
  // configureStore.js

  import {createStore, applyMiddleware} from 'redux';
  import rootReducer from '../reducers';
  import reduxImmutableStateInvariant from 'redux-immutable-state-invariant';

  export default function configureStore(initialState) {
    return createStore(
      rootReducer,
      initialState,
      applyMiddleware(reduxImmutableStateInvariant())
    );
  }
  ```

* The `configureStore` function should accept one parameter, which is the initial state for your app. Inside this function, we're going to return a call to `createStore` that will take two parameters: `rootReducer` and the `initialState`.
* We can add an optional piece of middleware as the third parameter to enhance our store. To do this, we're going to need a function that comes with Redux called `applyMiddleware`.
* we can pass all of the middleware that we'd like to utilize in our application to the `applyMiddleware` function (the middleware that we'd like to apply is `reduxImmutableStateInvariant`).
* make sure that we have parentheses to `reduxImmutableStateInvariant` so that we actually invoke it within `applyMiddleware` function.
* Check out React Slingshot on GitHub for an example of how to configure the other pieces of middleware.

### Instantiate Store and Provider

* The last detail is to update our app's entry point to work with Redux.
* we can create an instance of our store by calling `configureStore` function:

  ```js
  const store = configureStore();
  ```

* you can pass initial state to `configureStore` (i.e. `createStore`) if you're wanting to rehydrate your store using some separate state that's passed down from the server or stored in local storage.
* Our reducers already set their initial state using an ES6 default parameter (e.g. `export default function courseReducer(state = [], action)`). So, if we do not pass the initial state to the store, each one of the reducers will handle their initial state themselves.
* React-Redux provides this special component called `Provider`. What `Provider` is a higher-order component that attaches our store to our React container components.
* the `Provider` component accepts a store as a prop and just wraps our Router component. So, effectively the Provider component is wrapping our entire application so that it can be connected to our Redux store:

  ```js
  import 'babel-polyfill';
  import React from 'react';
  import { render } from 'react-dom';
  import configureStore from './store/configureStore';
  import {Provider} from 'react-redux';
  import { Router, browserHistory } from 'react-router';
  import routes from './routes';
  import './styles/styles.css';
  import '../node_modules/bootstrap/dist/css/bootstrap.min.css';

  const store = configureStore();

  render(
    <Provider store={store}>
      <Router history={browserHistory} routes={routes} />
    </Provider>,
    document.getElementById('app')
  );
  ```

### Connect Container

* We now have all the Redux infrastructure set up except for this remaining piece, which is to update our component to work with Redux. To do that, we need to reference the connection function which comes with React-Redux.
* in our component we import `connect` from React-Redux and at the bottom of our component, instead of exporting a plain component, we're going to export a component that's decorated by the React-Redux Connect function.

  ```js
  // CoursesPage.js

  function mapStateToProps(state, ownProps) {
    return {
      courses: state.courses
    };
  }

  function mapDispatchToProps(dispatch) {
    return {
      actions: bindActionCreators(courseActions, dispatch)
    };
  }

  export default connect(mapStateToProps, mapDispatchToProps)(CoursesPage);
  ```

* The `connect` function is what we use to create components that can interact with Redux.
* we refer to these components as container components.
* `connect` is a higher-order component that's going to wrap our CoursesPage. It takes two parameters, the first being `mapStateToProps` and the second being `mapDispatchToProps`. Each of these parameters is a function.
* two parentheses side by side is just two function calls. The Connect function right here ends up returning a function, and that function immediately calls our container component with the result of the first function.
* `mapStateToProps` takes two parameters, the first being `state` and the second being `ownProps`. Inside this function, we're going to define an object that returns the properties that we'd like to see exposed on our component. So, by defining `courses: state.courses` now we can have `this.props.courses` in the component.
* `state.courses` is the course data that's within our Redux store.
* in `rootReducer` if we had instead called it the reducer, `courseReducer`, then this here we had to use `state.courseReducer` instead of state.courses
* `mapStateToProps`' second parameter, `ownProps`, lets us access props that are being attached to the component. It's called ownProps because it's a reference to the component's own props.
* `mapDispatchToProps` is for deciding what actions you want to expose on your component. This is an optional parameter. When we omit this parameter, component automatically gets a `dispatch` property attached to it which is injected by `connect`. So if you don't put `mapStateToProps` as the second parameter, then you can use `this.props.dispatch` inside the component to manually do the dispatch ourselves.
* `dispatch` is a function that allows you to fire off your actions. So we will be able to dispatch different actions that we've defined in our actions file.
* in the above example we need to add some props validation for `dispatch` and `courses`:

  ```js
  // CoursesPage.js

  CoursesPage.PropTypes = {
    courses: PropTypes.array.isRequired,
    actions: PropTypes.object.isRequired
  };
  ```

### Step Through Redux Flow

* Here is the Redux flow when we dispatch an action:
  1. first the action creator
  1. then the reducer
  1. then `mapStateToProps` function
  1. finally, the `render` function

### mapDispatchToProps Manual Mapping

* The `mapDispatchToProps` function determines what actions are available in the component. It takes one parameter, which is `dispatch`. This will get injected in by the Connect function. In this function, we will also wrap our action creators in a call to dispatch:

  ```js
  // CoursesPage.js

  function mapDispatchToProps(dispatch) {
    return {
      createCourse: course => dispatch(courseActions.createCourse(course))
    };
  }
  ```

* **Note:** If we didn't wrap our action in a call to dispatch (e.g. `createCourse: course => courseActions.createCourse(course)`), then nothing would happen. Because, if we called `this.props.createCourse`, what we would end up calling would be `createCourse` function in `courseActions`, which returns an object. We would just end up holding onto a reference to an object and this object by itself would do nothing. So we wrap our actions in a call to `dispatch`, and that triggers our flow through Redux.

* now that we have this mapping setup, we've declared that our component above will receive `createCourse` as a prop, and it will be wrapped in a call to dispatch for us.

* since we have added an new prop we need to add the validation too:

  ```js
  CoursePage.propTypes = {
    createCourse: PropTypes.func.isRequired
  };
  ```

* We should note that when we use `mapDispatchToProps`, `connect` will no longer inject `dispatch` to props. So we don't need to validate `dispatch` in `propTypes`.

### bindActionCreators

* in previous section we saw how using `mapDispatchToProps` we can manually dispatch our action within our component, but Redux comes with a helper function, `bindActionCreators`, to help save us from having to manually wrap our action creators in a `dispatch` call.

  ```js
  // CoursesPage.js

  function mapDispatchToProps(dispatch) {
    return {
      actions: bindActionCreators(courseActions, dispatch)
    };
  }
  ```

* what `bindActionCreators` will do is it will go through `courseActions` and find all the actions in that file and then wrap them in a call to dispatch.
* now we need to say `this.props.actions.createCourse` because all actions will sit under `actions`.
* `actions` is an object so this is how we validate it:

  ```js
  CoursesPage.PropTypes = {
    courses: PropTypes.array.isRequired,
    actions: PropTypes.object.isRequired
  };
  ```

* we can `dispatch` a single action with `bindActionCreator` if we needed:

  ```js
  createCourse: bindActionCreators(courseActions.createCourse, dispatch)
  ```

* the function names `mapStateToProps` and `mapDispatchToProps` are optional, so you can use whatever name you like. You could also define these function inline in `connect`.
* So we've now seen three different ways that you can handle dispatching actions in your container components.

### Container Structure Review

* here is a review of the five major pieces of a container component:
  1. **The constructor:** In the constructor, we initialize state and also this is the best place to call our `bind` functions which are any functions that need to be bound to the `this` context
  1. **Child functions:** which are called by `render`
  1. **`render` function:** where we would typically just be calling a child component (in our example component for simplicity, we've just put the markup inline) but I recommend keeping the markup separate. Container components ideally just call a child component that contains that markup.
  1. **`propTypes`:** that provide our prop type validation.
  1. **Redux `Connect` and related functions:** So we have our call to `Connect`. We have our `mapStateToProps` function and our `mapDispatchToProps` function.

* here is our example component:

  ```js
  // CoursesPage.js

  import React from 'react';
  import {connect} from 'react-redux';
  import * as courseActions from '../../actions/courseActions';
  import {bindActionCreators} from 'redux';
  import PropTypes from 'prop-types';

  class CoursesPage extends React.Component {
    constructor(props, context) {
      super(props, context);

      this.state = {
        course: { title: "" }
      };

      this.onTitleChange = this.onTitleChange.bind(this);
      this.onClickSave = this.onClickSave.bind(this);
    }

    onTitleChange (event) {
      const course = this.state.course;
      course.title = event.target.value;
      this.setState({course: course});
    }

    onClickSave () {
      this.props.actions.createCourse(this.state.course);
    }

    courseRow(course, index) {
      return <div key={index}>{course.title}</div>;
    }

    render() {
      return (
        <div>
          <h1>Courses</h1>
          {this.props.courses.map(this.courseRow)}
          <h2>Add Course</h2>
          <input
            type="text"
            onChange={this.onTitleChange}
            value={this.state.course.title} />

          <input
            type="submit"
            value="Save"
            onClick={this.onClickSave} />
          </div>
      );
    }
  }

  CoursesPage.PropTypes = {
    courses: PropTypes.array.isRequired,
    actions: PropTypes.object.isRequired
  };

  function mapStateToProps(state, ownProps) {
    return {
      courses: state.courses
    };
  }

  function mapDispatchToProps(dispatch) {
    return {
      actions: bindActionCreators(courseActions, dispatch)
    };
  }

  export default connect(mapStateToProps, mapDispatchToProps)(CoursesPage);
  ```

### Action Type Constants

* Check out the Redux docs, the section that discusses alternative approaches for [reducing boilerplate](https://redux.js.org/docs/recipes/ReducingBoilerplate.html).

* We should use constants instead hard-coded strings for actionTypes. This helps us avoid typos. There're a couple of different ways of handling this:
  1. We could create a constants folder inside `reducers` folder with a dedicated constants file inside where all your actionType constants are stored in a single spot, but the downside is it's yet another file that you have to open and edit every time you create a new action.
  1. Placing your constants within your actions file is more convenient, but there are a couple of downsides: First, it would add noise to my actions file and, second, when I want to use the constant, I would have to reference my action from my reducer.
  1. Define our actionType constants in the `actions` in a file called `actionTypes.js`. I find it more logical to place actionType constants in the actions folder rather than out here in a separate constants folder:

      ```js
      //actionTypes.js

      export const CREATE_COURSE = 'CREATE_COURSE';
      ```

* here is the updated `courseActions` to use the constant:

  ```js
  // courseActions.js

  import * as types from './actionTypes';

  export function createCourse(course) {
    return { type: types.CREATE_COURSE, course};
  }
  ```

* and here is the updated reducer:

  ```js
  // courseReducer.js

  import * as types from '../actions/actionTypes';

  export default function courseReducer(state = [], action) {
    switch(action.type) {
      case types.CREATE_COURSE:
        return [...state,
          Object.assign({}, action.course)
        ];

      default:
        return state;
    }
  }
  ```

### Why a Mock API?

* I recommend the mock API pattern for building a client-side app. Here's why:
  1. this pattern allows you to start development immediately, even if the APIs that you need to consume haven't been created yet. As long as you can agree with the API team on the shape of the data that the final APIs will return, then you can create a mock API and begin development.
  1. A mock API helps me move independently when a separate team is handling the web APIs.
  1. A mock API gives me an easy backup plan if the API is down or broken at any given time, so I don't have to stop development.
  1. Hitting mock data is also the fastest way to handle rapid development because you can count on all responses being instantaneous if you like. This means that you're not hampered by slow or unreliable API calls in the early stages of development. And the good news is you don't have to wait until the real APIs are complete before testing how the app feels with slow APIs because unlike a real API call, a mock API allows you to control the speed of responses.
  1. A mock API also gives me a handy tool for automated testing. Since the data is local, it's both fast and reliable. You don't have to mock calls since your mock API is already a mock. And since the data is deterministic, you can even write tests that utilize the data, and they won't be slow since the tests are local. All the data is just sitting in memory.
  1. Finally, you can easily point to the real API later by simply changing the import at the top of your file. Or you could even check a centralized config that allows you to toggle between the mock and real APIs via a single setting.

### Async Library Options

* in Redux, actions are synchronous and must return an object.
* There are multiple libraries for handling async in Redux. The three most popular libraries for handling async calls in Redux are:
  * **redux-thunk:** Redux-thunk is quite popular and was written by Dan Abramov, who is also the creator of Redux. Redux-thunk allows you to return functions from your action creators instead of objects.
  * **redux-promise:** Redux-promise is a new alternative middleware library that uses Flux standard actions to bring some clear conventions to async calls. However, this project is still quite new and is currently the least popular of the three.
  * **redux-saga:** Redux-saga takes a very different approach from these first two libraries. It uses ES6 generators and offers an impressive amount of power with what's basically a rich domain-specific language for dealing with asynchrony.
* Let's contrast the two most popular options, thunks and sagas, in more detail:
  * With redux-thunk, your actions can return functions instead of objects, so a thunk wraps an asynchronous operation in a function. With sagas, you handle async operations via generators instead. Generators are functions that can be paused and resumed later. A generator can contain multiple `yield` statements. At each `yield`, the generator will pause.
  * Thunks are a bit clunky to test because you have to mock API calls, and you have no easy hooks for observing and testing individual steps in the asynchronous flow. Sagas are easier to test because you can assert on their effects because they simply return data. You don't have to mock anything, and your tests are generally more readable and clear.
  * The benefit of thunks is that they're conceptually simple and much like Redux, the API service area is very small. This makes learning thunks quite easy. Sagas, on the other hand, have a high learning curve because you need to understand generators and a rather large API.
* The decision between these two libraries isn't easy. I suggest using thunks initially since thunks cover many use cases well and are much easier to learn. Then consider moving to sagas as you feel pain points and get more comfortable with the power of generators.

### Thunk Overview

* Here's an example of a thunk for deleting an author:

  ```js
  export function deleteAuthor(authorId) {
    return dispatch => {
      return AuthorApi.deleteAuthor(authorId).then(() => {
        dispatch(deleteAuthor(authorId));
      }).catch(handleError);
    };
  }
  ```

* As you can see, a thunk is a function that returns a function.
* A **thunk** is a function that wraps an expression in order to delay its evaluation. So in this case, the deleteAuthor function is wrapping our dispatch function so that dispatch can run later.

### Mock API Setup

* Our mock api folder is `src/api` and has three files to it: `mockAuthorApi.js`, `mockCourseApi.js`, and `delay.js`
* The third file is called delay because it manages the simulated delay of each mock API call.
* at the top of both the `mockAuthorApi` and the `mockCourseApi` there is some hard-coded data that's really just simulating a database that sits on the server
* these files are mocking an API. So we're going to pretend that we're making AJAX calls to a server and that it's sending back data that it's retrieving from the database. But what these files actually do is use setTimeout to simulate the delay of making a call to the server, and then they return the data for me.

### Add Thunk to Store

The first step to using thunks is to enhance our store configuration. In the `configureStore.js` file add `thunk` to our middleware. To do so, we can just add it to the list of arguments that we're passing to `applyMiddleware`:

```js
// configureStore.js

import {createStore, applyMiddleware} from 'redux';
import rootReducer from '../reducers';
import reduxImmutableStateInvariant from 'redux-immutable-state-invariant';
import thunk from 'redux-thunk';

export default function configureStore(initialState) {
  return createStore(
    rootReducer,
    initialState,
    applyMiddleware(thunk, reduxImmutableStateInvariant())
  );
}
```

### Create Load Courses Thunk

* For our first thunk, let's create a way to load courses when the app initially loads. We can add a `thunk` to the `courseActions` file. We'll create a new function called loadCourses.
* a thunk always returns a function that accepts a dispatch. This wrapper function will exist in every one of our thunks. Here is the modified version of our action:

  ```js
  // courseActions.js

  import * as types from './actionTypes';
  import courseApi from '../mockCourseApi';

  export function loadCoursesSuccess(course) {
    return { type: types.LOAD_COURSE_SUCCESS, course};
  }

  export function loadCourses() {
    return function(dispatch) {
      return courseApi.getAllCourses().then(courses => {
        dispatch(loadCoursesSuccess(courses));
      }).catch(error => {
        throw(error);
      });
    }
  }
  ```

### Action Naming Conventions

In the above example I am using the success suffix for three reasons:

* First, we already have a function called `loadCourses`
* Second, this action doesn't fire until all authors have been successfully returned by our API call. So the suffix helps clarify that our async request was successful.
* Third, people often create a corresponding failure action type called `loadCoursesFailure` or `loadCoursesError`

### Load Courses in Reducer

Now that we have a `loadCoursesSuccess` action, we need to create a corresponding handler over in our reducers file:

```js
// courseReducer.js

import * as types from './actionTypes';
import courseApi from '../mockCourseApi';

export function loadCoursesSuccess(course) {
  return { type: types.LOAD_COURSE_SUCCESS, course};
}

export function loadCourses() {
  return function(dispatch) {
    return courseApi.getAllCourses().then(courses => {
      dispatch(loadCoursesSuccess(courses));
    }).catch(error => {
      throw(error);
    });
  }
}
```

### Dispatch Action on Page Load

* To fetch the course data when our app loads, we can open our application's entry point, which is `index.js`.
* After the store is configured by the call to `configureStore` we can go ahead and dispatch actions against the store:

  ```js
  // index.js

  import 'babel-polyfill';
  import React from 'react';
  import { render } from 'react-dom';
  import configureStore from './store/configureStore';
  import {Provider} from 'react-redux';
  import { Router, browserHistory } from 'react-router';
  import routes from './routes';
  import {loadCourses} from './actions/courseActions';
  import './styles/styles.css';
  import '../node_modules/bootstrap/dist/css/bootstrap.min.css';

  const store = configureStore();
  store.dispatch(loadCourses());

  render(
    <Provider store={store}>
      <Router history={browserHistory} routes={routes} />
    </Provider>,
    document.getElementById('app')
  );
  ```

* I used the dispatch function (`store.dispatch(loadCourses())`), which is part of my store, and I passed it the action that wanted to dispatch, `loadCourses`.

### Create Course List Component

Our `CoursesPage` component is a container component, so ideally the markup in the `render` function should sit in a separate component, a presentation style component rather than a container component. Here is the modified `CoursesPage.js` and the added `CourseList.js` and `CourseListRow.js`:

```js
// CoursesPage.js

import React from 'react';
import {connect} from 'react-redux';
import * as courseActions from '../../actions/courseActions';
import {bindActionCreators} from 'redux';
import PropTypes from 'prop-types';
import CourseList from './CourseList';

class CoursesPage extends React.Component {
  constructor(props, context) {
    super(props, context);
  }

  courseRow(course, index) {
    return <div key={index}>{course.title}</div>;
  }

  render() {
    const {courses} = this.props;

    return (
      <div>
        <h1>Courses</h1>
        <CourseList courses={courses}/>
      </div>
    );
  }
}

CoursesPage.PropTypes = {
  courses: PropTypes.array.isRequired,
  actions: PropTypes.object.isRequired
};

function mapStateToProps(state, ownProps) {
  return {
    courses: state.courses
  };
}

function mapDispatchToProps(dispatch) {
  return {
    actions: bindActionCreators(courseActions, dispatch)
  };
}

export default connect(mapStateToProps, mapDispatchToProps)(CoursesPage);
```

```js
//CourseList.js

import React, {PropTypes} from 'react';
import CourseListRow from './CourseListRow';

const CourseList = ({courses}) => {
  return (
    <table className="table">
      <thead>
      <tr>
        <th>&nbsp;</th>
        <th>Title</th>
        <th>Author</th>
        <th>Category</th>
        <th>Length</th>
      </tr>
      </thead>
      <tbody>
      {courses.map(course =>
        <CourseListRow key={course.id} course={course}/>
      )}
      </tbody>
    </table>
  );
};

CourseList.PropTypes = {
  courses: PropTypes.array.isRequired
};

export default CourseList;
```

```js
//CourseListRow.js

import React, {PropTypes} from 'react';
import {Link} from 'react-router';

const CourseListRow = ({course}) => {
  return (
    <tr>
      <td><a href={course.watchHref} target="_blank">Watch</a></td>
      <td><Link to={'/course/' + course.id}>{course.title}</Link></td>
      <td>{course.authorId}</td>
      <td>{course.category}</td>
      <td>{course.length}</td>
    </tr>
  );
};

CourseListRow.PropTypes = {
  course: PropTypes.object.isRequired
};

export default CourseListRow;
```

## Async Writes in Redux

* When I create stateless functional components, I prefer to destructure all the `props` in the function's argument list:
  * This keeps the calls nice and short.
  * It also has the added benefit of making the component's dependencies clear. You can glance at the function signature and clearly see what's required, even if `PropTypes` are defined.

  ```js
  // CourseForm.js

  const CourseForm = ({course, allAuthors, onSave, onChange, saving, errors}) => {
  return (
    <form>
      <h1>Manage Course</h1>
      <TextInput
        name="title"
        label="Title"
        value={course.title}
        onChange={onChange}
        error={errors.title}/>
  ```

* The other pattern that I often follow here is I should have the same number of destructured `props` that I have as the arguments, under my `PropTypes`.

* We need to create some reusable components for handling `TextInputs` and `SelectInputs` on forms:
  * Since they're common components, I'd like to place them here in the common folder.
  * The big benefit is there're a lot of Bootstrap-specific classes that are now being handled automatically anytime that I use a `TextInput` (e.g. things like form-group, form-control, the wrapper div, etc.) these are all useful conventions that I get right out of the box by using this TextInput control that I've created.
  * I'm also consistently showing errors inside the `TextInput` if any exist and using some Bootstrap classes to style those errors.

  ```js
  // TextInput.js

  import React, {PropTypes} from 'react';

  const TextInput = ({name, label, onChange, placeholder, value, error}) => {
    let wrapperClass = 'form-group';
    if (error && error.length > 0) {
      wrapperClass += " " + 'has-error';
    }

    return (
      <div className={wrapperClass}>
        <label htmlFor={name}>{label}</label>
        <div className="field">
          <input
            type="text"
            name={name}
            className="form-control"
            placeholder={placeholder}
            value={value}
            onChange={onChange}/>
          {error && <div className="alert alert-danger">{error}</div>}
        </div>
      </div>
    );
  };

  TextInput.propTypes = {
    name: PropTypes.string.isRequired,
    label: PropTypes.string.isRequired,
    onChange: PropTypes.func.isRequired,
    placeholder: PropTypes.string,
    value: PropTypes.string,
    error: PropTypes.string
  };

  export default TextInput;
  ```

  ```js
  // SelectInput.js

  import React, {PropTypes} from 'react';

  const SelectInput = ({name, label, onChange, defaultOption, value, error, options}) => {
    return (
      <div className="form-group">
        <label htmlFor={name}>{label}</label>
        <div className="field">
          {/* Note, value is set here rather than on the option - docs: https://facebook.github.io/react/docs/forms.html */}
          <select
            name={name}
            value={value}
            onChange={onChange}
            className="form-control">
            <option value="">{defaultOption}</option>
            {options.map((option) => {
              return <option key={option.value} value={option.value}>{option.text}</option>;
            })
            }
          </select>
          {error && <div className="alert alert-danger">{error}</div>}
        </div>
      </div>
    );
  };

  SelectInput.propTypes = {
    name: PropTypes.string.isRequired,
    label: PropTypes.string.isRequired,
    onChange: PropTypes.func.isRequired,
    defaultOption: PropTypes.string,
    value: PropTypes.string,
    error: PropTypes.string,
    options: PropTypes.arrayOf(PropTypes.object)
  };

  export default SelectInput;
  ```

### Redirect via React Router Context

* we can use React Router's `context` to redirect. To set up React Router's context, you have to declare that you want it. And we can do that by declaring the `contextTypes` that we'd like to import on our component. Since `contextTypes` is a static property, it has to be done after the class definition. We're effectively saying that we want `router` to be one of the `contextTypes` that are required. And by doing this, this makes React Router's context available to us throughout this component:

  ```js
  // ManageCoursePage.js

  //Pull in the React Router `context` so router is available on `this.context.router`
  ManageCoursePage.contextTypes = {
    router: PropTypes.object
  };
  ```

* If you're not already familiar with context, it's basically a global variable that library authors use but that we as library consumers should avoid. Yes, global state is generally evil, but context is used by both React Router and Redux in some places to provide easy access to the data that we need without having to write boilerplate plumbing code.

* And now that we have access to the router's context, we can go back up to our save function and put it to use. After we call `saveCourse`, I'll just call `this.context.router`, and we will push a new item to the `router` on our `context` object. So this will change our URL to `/courses`:

  ```js
  // ManageCoursePage.js inside ManageCoursePage class

  saveCourse(event) {
    event.preventDefault();

    if (!this.courseFormIsValid()) {
      return;
    }

    this.setState({saving: true});

    this.props.actions.saveCourse(this.state.course)
      .then(() => this.redirect())
      .catch(error => {
        toastr.error(error);
        this.setState({saving: false});
      });
  }

  redirect() {
    this.setState({saving: false});
    toastr.success('Course saved');
    this.context.router.push('/courses');
  }

  render() {
    return (
      <CourseForm
        allAuthors={this.props.authors}
        onChange={this.updateCourseState}
        onSave={this.saveCourse}
        course={this.state.course}
        errors={this.state.errors}
        saving={this.state.saving}
      />
    );
  }
  ```

### Populate Form via mapStateToProps

* we can populate the form using `mapStateToProps`, if the id of the course is specified as parameter in the URL
* we can use `ownProps` to access the URL parameters. See how `courseId` is used in the following code:

  ```js
  // ManageCoursePage.js

  function getCourseById(courses, id) {
    const course = courses.filter(course => course.id == id);
    if (course) return course[0]; //since filter returns an array, have to grab the first.
    return null;
  }

  function mapStateToProps(state, ownProps) {
    const courseId = ownProps.params.id; // from the path `/course/:id`

    let course = {id: '', watchHref: '', title: '', authorId: '', length: '', category: ''};

    if (courseId && state.courses.length > 0) {
      course = getCourseById(state.courses, courseId);
    }

    return {
      course: course,
      authors: authorsFormattedForDropdown(state.authors)
    };
  }
  ```

* `params` property is an object with every param specified in the URL with the `ownProps` object. For example: `ownProps.params` will be equal to `{ filter: 'SHOW_COMPLETED' }` if we are navigating to `localhost:3000/SHOW_COMPLETED`.

### Update State via componentWillReceiveProps

* when the `props` change, we need to update our container component's state. To do that, we'll add some code in `componentWillReceiveProps`. Place this function right below the constructor. This React lifecycle function is called anytime that props have changed, as well as anytime that React thinks that props might have changed:

  ```js
  // ManageCoursePage.js

  componentWillReceiveProps(nextProps) {
    if (this.props.course.id != nextProps.course.id) {
      // Necessary to populate form when existing course is loaded directly.
      this.setState({course: Object.assign({}, nextProps.course)});
    }
  }
  ```

* So, we populated the form on page load using a combination of `mapStateToProps` to initially populate the form when mounted and `componentWillReceiveProps` to update local state when the `props` change



