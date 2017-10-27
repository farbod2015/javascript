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
  1. The first option is to ignore it since `mapDispatchToProps` is an optional parameter on the Connect function. When you omit it, then the `dispatch` function will be attached to your container component. This means you can call `dispatch` manually and pass it an action creator:
      ```js
      this.props.dispatch(loadCourses());
      ```
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
      ```
      * Compared to option 1, it keeps the calls in my actual component shorter at the cost of some extra coding here in `mapDispatchToProps`.

  1. Finally, you can use the `bindActionCreators` function, which is a convenience function that wraps your action creators in dispatch calls for you. `BindActionCreators` basically does what we're doing in option 2 automatically.
      ```js
      function mapDispatchToProps(dispatch) {
        return {
          actions: bindActionCreators(actions, dispatch)
        };
      }
      ```


  
  
   Let's look at examples of each of these approaches in more detail to help clear things up. As I just mentioned, one simple option is to ignore the mapDispatchToProps function altogether. Calling Connect on your component automatically adds a dispatch prop to your component. You can use this dispatch prop to call your action creators as I'm doing here. However, there're a couple of downsides with this approach. First, it requires more boilerplate each time you want to fire off an action because you have to explicitly call dispatch and pass it the action you'd like to fire. Second, this means your child components need to reference Redux-specific concepts like the dispatch function, as well as your action creators. If you want to keep your child components as simple as possible and avoid tying them to Redux, then this approach is not ideal. The second option is to manually wrap your action creators in dispatch calls. Here I'm specifying the actions I want to expose to my component explicitly. One by one, I wrap each action creator in a dispatch call, and then this is how my call would look within the component. When you're getting started, I recommend using this option because manually wrapping action creators makes it clear what you're doing. But as you can see, it's quite redundant. That's why you may prefer to use option 3, which is bindActionCreators. This function ships with Redux to handle this redundancy for you. With this approach, the bindActionCreators function will wrap all the actions pass to it in a dispatch call for you. Of course, the props created by these two examples is slightly different. Notice that the prop that will be exposed to the component here is called actions. But if we go back to the previous slide, we are exposing this.props.loadCourses, this.props.createCourse, and so on. So it's a minor difference in the way that I chose to wire this up. The bottom line is approaches 2 and 3 both produce the same result. They wrap your actions in a dispatch call so that they're easy to pass down to child components. But there's a notable advantage to options 2 and 3 over option 1, and that is that with options 2 and 3, your child components don't have to know anything about Redux. Child components can simply call the actions that are passed down to them via props. Remember, with option 1, we had to import action creators into our child components so that we could call Redux's dispatch directly. I know this is confusing without an example that's in proper context. So don't worry. In the next module, we'll use all three of these approaches. And before we close out this module, let's have a quick chat with Redux to wrap up everything that we've learned. 

