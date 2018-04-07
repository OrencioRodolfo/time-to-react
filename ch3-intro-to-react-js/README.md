- [What is React?](#what-is-react-)
  * [Virtual DOM](#virtual-dom)
  * [Introducing JSX](#introducing-jsx)
  * [Components](#components)
    + [Functional components](#functional-components)
    + [Class components](#class-components)
    + [Lifecycle methods](#lifecycle-methods)
    + [Managing state](#managing-state)
  * [Lists and keys](#lists-and-keys)
- [Demo app](#demo-app)
  * [Setup](#setup)
  * [Challenge](#challenge)

# What is React?
> React is a declarative, efficient, and flexible JavaScript library for building user interfaces.

But what do we mean with "declarative"?

> Declarative programming is a programming paradigm … that expresses the logic of a computation without describing its control flow.

> Imperative programming is a programming paradigm that uses statements that change a program’s state.

I.e. with declarative you say what's the result you want to achieve but not how to achieve it.

In practive with react you don't manipulate the DOM, instead you manipulate the application's state and the app will react to this change and re-render the UI, achieving the final result that you're were looking for in the first place.

Let's seet it action:

~~~js
const container = document.getElementById(‘container’);
const btn = document.createElement(‘button’);
btn.className = ‘btn red’;
btn.onclick = function(event) {
 if (this.classList.contains(‘red’)) {
   this.classList.remove(‘red’);
   this.classList.add(‘blue’);
 } else {
   this.classList.remove(‘blue’);
   this.classList.add(‘red’);
 }
};
container.appendChild(btn);
~~~

~~~js
class Button extends React.Component{
  this.state = { color: 'red' }
  handleChange = () => {
    const color = this.state.color === 'red' ? 'blue' : 'red';
    this.setState({ color });
  }
  render() {
    return (<div>
      <button 
         className=`btn ${this.state.color}`
         onClick={this.handleChange}>
      </button>
    </div>);
  }
}
~~~

The React example never actually touches an element. it simply declares an element should be rendered given our current state. It does not actually manipulate the DOM itself.  
When writing React, it’s often good not to think of how you want to accomplish a result, but instead what the component should look like in it’s new state.

Does this code looks weird for you? Don't panic, we'll get into component right away.

## Virtual DOM

**DOM**

**DOM** stands for **Document Object Model** and is an abstraction of a structured text. For web developers, this text is an **HTML** code, and the DOM is simply called **HTML DOM**. Elements of HTML become nodes in the DOM.

So, **while HTML is a text, the DOM is an in-memory representation of this text**.

The HTML DOM provides an interface (API) to traverse and modify the nodes. It contains methods like `getElementById` or `removeChild`.  
So, whenever we want to dynamicly change the content of the web page, we modify the DOM:

~~~js
var item = document.getElementById("myLI");
item.parentNode.removeChild(item);
~~~

The DOM trees are huge nowadays. Since we are more and more pushed towards dynamic web apps (SPAs), we need to modify the DOM tree incessantly and a lot. And this is a real performance and development pain. That's where Virtual DOM came to rescue us.

**Virtual DOM**

Virtual DOM was not invented by React, but React uses it and provides it for free.  
The **Virtual DOM is an abstraction of the HTML DOM**. Since the DOM itself was already an abstraction, the virtual DOM is, in fact, an abstraction of an abstraction.

<p align="center">
  <img alt="Inception" src="https://i.imgur.com/oLU1Wye.jpg" width="400" />
  <br>
  <span>Abstraction inception</span>
</p>

When you render a JSX element, every single virtual DOM object gets updated.
This sounds incredibly inefficient, but the cost is insignificant because the virtual DOM can update so quickly.

Once the virtual DOM has updated, then **React compares the virtual DOM with a virtual DOM snapshot** that was taken right before the update.  
By comparing the new virtual DOM with a pre-update version, React figures out exactly which virtual DOM objects have changed. Then those, and only those objects, are updated on the real DOM.  
This "comparing" process is called "**diffing**." 

This makes a big difference! React can **update only the necessary parts** of the DOM and its reputation for **performance** comes largely from this innovation.

In summary, here's what happens when you try to update the DOM in React:

1. The entire virtual DOM gets updated.
2. The virtual DOM gets compared to what it looked like before you updated it. React figures out which objects have changed.
3. The changed objects, and the changed objects only, get updated on the real DOM.
4. Changes on the real DOM cause the screen to change.

## Introducing JSX

**JSX is a syntax extension to JavaScript**. When using React, you use JSX to describe what the UI should look like. It might remind you of a template language, but it comes with the full power of JavaScript.

JSX looks almost like pure HTML:

~~~js
var CommentBox = React.createClass({
  render: function() {
    return (
      <div className="commentBox">
        Hello, world! I am a CommentBox.
      </div>
    );
  }
});
~~~

With the slightly different of using `className` instead of `class`, since `class` is a reserved word in JavaScript.

React embraces the fact that rendering logic is inherently coupled with other UI logic: how events are handled, how the state changes over time, and how the data is prepared for display.

Instead of artificially separating technologies by putting markup and logic in separate files, React separates concerns with loosely coupled units called **"components"** that contain both. 

## Components
> Components let you split the UI into independent, reusable pieces, and think about each piece in isolation.

Conceptually, 

There are two types of components, **Functional and Class components**.

### Functional components

Functional components (aka dumb or presentational or stateless components) are JavaScript functions. They accept arbitrary inputs (called “props”) and return [React elements](https://reactjs.org/docs/rendering-elements.html) describing what should appear on the screen.

~~~js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
~~~

**Properties**

Properties is the way you can pass data into components, like the following:  
~~~js
function greet(props) {
  return <Hello name="John!" />;
}
~~~

This will return `<h1>Hello, John!</h1>` React element, which will go into the virtual DOM and finally to the DOM and get rendered on the screen.

Properties are read-only, which means, React gives you **one way data binding**. Using props you can only pass data from the top to the bottom. The only way to pass data back is through callback functions. Example:

~~~js
function Avatar() {
  return <Gallery onImageSelection={(image) => { selectAvatar(image) }} />;
}
~~~

`onImageSelection` is a property that has a function as its value that is invoked when an image from the gallery is selected. I.e. the `Gallery` component is actually sending data back to the `Avatar` component.

### Class components

Class (aka Smart) components have something that functional component don't, **state**.

State is a plain object that stores data about the application, about the component itself and this dictates how the app should work.

~~~js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
~~~

By convention, functional components are trated as "**presentational components**" and class components as "**containers**".  
What this basically means is that presentational components simply receive data through props and return some amount of JSX, while container are responsible for managing the state of the application.

**Containers:**  
* Describe how things work
* Provide **NO** DOM markup or styles
* Provide application data, do data fetching
* Call Flux actions (more on this later)

Here's a really good article from [Dan Abramov](https://twitter.com/dan_abramov) about this: [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)

### Lifecycle methods

The lifecycle methods are hooks that are invoked at different phases of the lifecycle of a component.  
The existing phases and corresponding methods are:

<p align="center">
  <img alt="Component lifecycle" src="https://i.imgur.com/ps6lHP9.png" width="800" />
  <br>
  <span>Component lifecycle</span>
</p>

I was wondering about giving you a description about each one of these methods and then I've found this article: [Understanding React — Component life-cycle](https://medium.com/@baphemot/understanding-reactjs-component-life-cycle-823a640b3e8d).  
And the thruth is, I wouldn't giving you a better explanation, so I would rather recommend you reading this article to get to know React's lifecycle methods.

### Managing state

React stores the component state in `this.state` and you can set its initial value as following:

~~~js
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = { counter: 0 };
    }
    ...
}
~~~

To chage the state you must use the `setState` method:  
~~~js
this.setState(data, callback);
~~~

Do never do this:  
~~~js
this.state = data
~~~

The only time you should being doing this is on the state initialization only.

> Never mutate this.state directly, as calling setState() afterwards may replace the mutation you made. Treat this.state as if it were immutable.

Why?  
* `setState` batches work behind the scenes. This means a manual state mutation may be overridden when setState is processed.
* If you declare a `shouldComponentUpdate` method, you can’t use a === equality check inside because the object reference will not change. So the approach above has a potential performance impact as well.

More about it here: [React: A (very brief) talk about immutability.](https://medium.com/pro-react/a-brief-talk-about-immutability-and-react-s-helpers-70919ab8ae7c)

## Lists and keys
I would say that the most common use case of `map` in React is when you're building lists.  
Let's see the following example:

~~~js
const listItems = (items) => items.map((number) =>
  <li>{number}</li>
);

const list = () => {
  const numbers = [1, 2, 3, 4, 5];

  return (
    <ul>
      {listItems(numbers)}
    </ul>
  )
}
~~~

This will produce:
~~~html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
</ul>
~~~

But when building lists like the previous one, you must provide a `key` property to each one of the list items with unique value. Keys help React identify which items have changed, are added, or are removed. So rather than having the code presented above we would have:  

~~~js
const listItems = (items) => items.map((number) =>
  <li key={number.toString()}>{number}</li>
);

const list = () => {
  const numbers = [1, 2, 3, 4, 5];

  return (
    <ul>
      {listItems(numbers)}
    </ul>
  )
}
~~~

The best way to pick a key is to use a string that uniquely identifies a list item among its siblings. 

# Demo app
Having all this base concepts in our minds now, it's time to build our first react app.

## Setup

For this I've created a bootstrap project with a simple "Hello world!" rendered in the screen. This project gives you:
* webpack + webpack-dev-server + HMR  
* linter  
* proptypes  
* styled components  
* jest + enzyme

Feel free to clone it a start building your first react app.  
**Repo:** [react-bootstrap](https://github.com/OrencioRodolfo/react-bootstrap)

Take a look at the project's `README.md` file to get started.

## Challenge

Using the sportmonks's soccer API, let's build the following page. The goal is to load the standings for a certain league and season and, when clicking on a team, load the team squad.

The API endpoints that we'll need are:
* Get All Leagues: https://soccer.sportmonks.com/api/v2.0/leagues?include=seasons
* Standings: https://soccer.sportmonks.com/api/v2.0/standings/seasons/{id}
* Team: https://soccer.sportmonks.com/api/v2.0/teams/{id}?include=squad

![](https://i.imgur.com/oKV8MAW.png)

![](https://i.imgur.com/XieJ7iZ.png)