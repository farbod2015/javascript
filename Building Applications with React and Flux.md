# Building Applications with React and Flux

This note is from the following modules:

* React Core Concepts
* React: Creating Components Introduction
* React Lifecycle
* React Composition
* Flux

## React Core Concepts

Why react?

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





