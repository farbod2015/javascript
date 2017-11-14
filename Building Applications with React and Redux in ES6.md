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



