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

* React offers an optional XML-like syntax, called JSX, for markup. It looks almost identical to HTML.
* There are some minor differences, such as using className instead of class, and htmlFor instead of for.
* JSX compiles down to JavaScript
  ```jsx
  // jSX
  <h1 color="red">Headinghere </h1>

  // Compiled version
  React.createElement("h1", {color: "red"}, "Heading here")
  ```
* It is totally optional, but most people prefer JSX because it looks like the final HTML, which makes it easier to write, easier to read






, and more approachable for designers. Now, before I show you JSX, I want to warn you that it's a bit like the first time you see an ugly baby. At first, you're going to have to hold back a cringe, but after you give it some time, you're likely going to fall in love, trust me. Now, also, clearly, this baby's adorable. Try to imagine an ugly baby here. This is my son so I'm a little biased. Anyway, with that, let's take a look at JSX. And here we have JSX. This is a simple React component that displays an AboutPage. Now most of this is plain old JavaScript, but what's this here in the middle? That is JSX. As you can see, JSX looks like HTML but it's sitting in the middle of your JavaScript. Now you're probably wondering how in the world this works. It's not valid JavaScript as is, and that's why JSX has to be compiled into JavaScript, so that your browser can understand it. Compiling JSX to JavaScript is pretty simple. 
Here's a snippet of JSX. As you can see, the JSX on top looks like HTML. As I mentioned in the previous slide, there's only a few minor differences, and this JSX compiles down to plain old JavaScript. As you can see, it becomes a call to React.createElement. The function is passed the name of the tag you created, an object that specifies the attributes that you'd like to set, and finally, the markup that should sit inside. This final parameter will contain calls to other elements if you have nested markup. Let's take a look at a more complex example. Here, a table structure is compiled down into multiple nested calls to React.createElement. The XML-style syntax of JSX has the benefit of balanced opening and closing tags. This helps make large trees easier to read than function calls or object literals, and you can really see this clearly by comparing the left side to the right hand side. Notice that the brace matching nature of JSX makes it easier to read and understand than the raw JavaScript on the right. 
It's harder to spot a missing closing tag with the raw JavaScript because it's simply a bunch of parentheses. So yes, you can avoid JSX if you want to do it the hard way and write this JavaScript yourself, but it's harder to read, it takes more time to type, and it's much less friendly to designers, so I recommend using JSX. 


