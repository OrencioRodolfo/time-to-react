# Table of contents

- [What is redux?](#what-is-redux-)
  * [Why and when should you use it?](#why-and-when-should-you-use-it-)
  * [Three principles](#three-principles)
    + [Single source of truth](#single-source-of-truth)
    + [State is read-only](#state-is-read-only)
    + [Changes are made with pure functions](#changes-are-made-with-pure-functions)
- [Basics](#basics)
  * [Actions](#actions)
  * [Action creators](#action-creators)
  * [Reducers](#reducers)
  * [Data flow](#data-flow)
- [Advanced](#advanced)
  * [Async actions](#async-actions)
  * [Middleware](#middleware)
- [Connected components - [react-redux]](#connected-components---react-redux)
- [Demo app](#demo-app)

# What is redux?
> [Redux] is a predictable state container for JavaScript apps.

You can use [Redux] with React or any other UI library. It's purpose is to simplify your app's state management and make it a single source of truth.

## Why and when should you use it?

SPAs have become increasingly complicated, our code must manage more state than ever before. This state can include server responses and cached data, as well as locally created data that has not yet been persisted to the server. 

React let's you manage your state, using smart components but it doesn't scale that good... To share state between multiple components in your application is not that easy to achieve, since you would need to pass data through props and callbacks. What can be not so clear for the developer trying to understand where the data comes from, how was it manipulated, by whom and when.  
You would find yourself having a deep nesting of components passing props from component to component. Imagine this scenario where you have component `A` and you want to pass data from its state to component `F`. We would have:

~~~js
class A extends React.Component {
  constructor(props) {
    super(props)
    this.state = { ... }
  }

  render() {
    return (
      <B someProp={this.state.someData}/>
    )
  }
}
~~~
~~~js
const B = (props) => (
  <C someProp={props.someData}/>
)
~~~
~~~js
const C = (props) => (
  <D someProp={props.someData}/>
)
~~~
~~~js
const D = (props) => (
  <E someProp={props.someData}/>
)
~~~
~~~js
const F = (props) => (
  <p>`Here's our data: ${props.someData}`</p>
)
~~~

Could you imagine having to inspect each one of these components and check where the data comes from?  
At some point, you no longer understand what happens in your app as you have lost control over the when, why, and how of its state.

I would say that you should use [Redux] whenever your app becomes larger and its state is harder and harder to maintain.  
For smaller apps with simple state, React's `setState` is enough, otherwise consider using a state management framework like [Redux].

## Three principles

The three principles we'll be discussing here is what makes Redux a good fit for state management. It tries to make state mutations predictable by imposing certain restrictions.

### Single source of truth
> The state of your whole application is stored in an object tree within a single store.

That's right, all your application's data will be stored into a single JavaScript object. It can seem messy but don't panic, it won't be when you understand how the state is managed and the data flow.  
Plus, a single state tree also makes it easier to debug or inspect your application.

### State is read-only
> The only way to change the state is to emit an action, an object describing what happened.

This ensures that there's no multiple, concurrent points on your application trying to change your application's state at the same time. Instead, they express an intent to transform the state using **actions**.

### Changes are made with pure functions
> To specify how the state tree is transformed by actions, you write pure reducers.

**Actions** communicate the appp's intention to change the state and **reducers** are the ones deciding how that change happens. Which data is stored in the **store** and how.

> Reducers are just pure functions that take the previous state and an action, and return the next state. 

Immutability here is as much important as when managing React's state using the `setState` method.  
Reducers must always return new state objects intead of mutating the previous state.

# Basics
## Actions
> Actions are payloads of information that send data from your application to your store. They are the only source of information for the store.

~~~js
{
  type: 'ADD_TO_CART',
  payload: item,
}
~~~

Actions are plain JavaScript objects always with a `type` string property and some sort of data from your application that is aimed to be stored.  
Types must be unique and, when your app is large enough, probably it would be a good idea to declare them as constants somewhere in your application. Ex. Have a file `types` with the following:
~~~js
export const ADD_TO_CART = 'ADD_TO_CART'
export const GET_CART = 'GET_CART'
...
~~~

Besides the `type` property, the structure of the `action` object is up to you. But by convention it has a `payload` property with the data that can potentially become part of the application's new state.

## Action creators
As the name might suggest, action creators are functions that simply create actions.  
So, for dispatching the `USER_FETCHED` we would define an action creator like:

~~~js
const addToCart = item => ({
  type: 'ADD_TO_CART',
  payload: item
})
~~~

## Reducers
> Reducers specify how the application's state changes in response to actions sent to the store. Remember that actions only describe the fact that something happened, but don't describe how the application's state changes.

The app communicates its intention dispatching actions with a `type` and `payload` properties and reducers receive these intentions and decides how the application's state changes in response to these.

~~~js
const cart = {
  items: {},
  total: 0,
}
function cart(state = initialState, action) {
  switch (action.type) {
    case 'ADD_TO_CART':
      return { // return a shiny new object (immutability)
        ...state, // previous state
        items: {
          ...state.items,
          ...action.payload, // the cart's new item
        }
      }
    default:
      return state
  }
}
~~~

In this example we have this "Cart" reducer that reacts to the `ADD_TO_CART` action, adding a new "item" to the `cart` stored in the application's state.

You can have as much reducer as you want to. This will give you the opportunity to split your state management into as many pieces you may want to and Redux will be incharge of combining all of them into a single state object through `combineReducers` utility function.

~~~js
import { combineReducers } from 'redux'
import cart from './reducers/cart'
import user from './reducers/user'

const shoppingApp = combineReducers({
  user,
  cart,
})
 
export default shoppingApp
~~~

## Data flow
> Redux architecture revolves around a strict unidirectional data flow.

This means that all data in an application follows the same lifecycle pattern, making the logic of your app more predictable and easier to understand.

The data lifecycle in any Redux app follows these 4 steps:

1. You dispatch an `action`
2. The Redux store calls the `reducer` function you gave it
3. The root reducer may combine the output of multiple reducers into a single state tree
4. The Redux store saves the complete state tree returned by the root reducer

<p align="center">
  <img alt="Redux data flow" src="https://i.imgur.com/2c3hlA2.gif" width="600" />
  <br>
  <span>Redux data flow</span>
</p>

# Advanced

## Async actions
Ok, for synchronous actions the action creators that we've just seen are enough. But what happens when we want to perforn an async action? An AJAX request fetching data about the user for instance?

There's where we have some other packages like [redux-thunk] and [redux-promise-middleware]. [redux-thunk] is the most known and used by far and you can read about how to use it at [Redux docs](https://redux.js.org/advanced/async-actions#async-action-creators). 

But for a matter of simplicity let's see [redux-promise-middleware].

Redux promise middleware is a Redux middleware (how suprising) that lets you return promises from the action's payload instead of only plain objects. This means we can now have the following action:

~~~js
{
  type: 'FETCH_USER',
  payload: fetch(`/user/${userId}`)
}
~~~

And from an async action like this one, [redux-promise-middleware] will produce three new actions:
* `FETCH_USER_PENDING`
* `FETCH_USER_REJECTED`
* `FETCH_USER_FULFILLED`

Where `_PENDING` is dispatched when the request begins, `_REJECTED` if the promise is rejected (ex. the fetch fails because there's no internet connection) and `_FULFILLED` if and when the request ends successfully. Each one of these dispatched actions hold a `payload` property with the resulting data.

## Middleware

We've seen middlewares when talking about [redux-promise-middleware]. Have you ever worked with [Express] middlewares in any Node.js server? Well, middlewares in Redux are the exact same thing. They intercept actions before they reach the reducer.  
There are enumerous use cases for middlewares, like logging, crash reporting, talking to an asynchronous API, routing, and more.

Here's how you can implement your own middleware:  

~~~js
/**
 * Logs all actions and states after they are dispatched.
 */
const logger = store => next => action => {
  console.group(action.type)
  console.info('dispatching', action)
  let result = next(action)
  console.log('next state', store.getState())
  console.groupEnd(action.type)
  return result
}
~~~

~~~js
import promiseMiddleware from 'redux-promise-middleware'
import logger from './middlewares'
import cart from './reducers/cart'
import user from './reducers/user'

const shoppingApp = combineReducers({
  user,
  cart,
})

const store = createStore(
  shoppingApp, // reducers
  applyMiddleware( // middlewares
    reduxPromiseMiddleware
    logger,
  )
)
~~~

# Connected components - [react-redux]

Until now we've talked only about [Redux], but what about React?

[react-redux] is the glue between these two. It gives your the `connect` function where you'll pass two arguments: `mapStateToProps` and `mapDispatchToProps`.

**`mapDispatchToProps`** is the one mapping the state stored at Redux to the component's properties. This is a function that receives the `state` and `props` arguments and returns a plain JavaScript object where each property becomes a property of the **connected component**.

On the other hand, **`mapDispatchToProps`** is the one mapping action creators to the component's properties. This is a plain object where each property is an action creator that can be invoked whenever the component wants to communicate its intent to change something in the store.  

Example: When the user clicks on the "Add to cart" button, the `addToCart` action creator would be invoked with the item being selected.

Example of a connected component:

~~~js
import { connect } from 'react-redux'
import actions from '../store/actions'

const Product = (props) => (
  <h1>{props.product.name}</h1>
  <div className="product-details">
    <p>{props.product.description}</p>
    <p>{props.product.price}</p>
  </div>
  <button onClick={() => props.addtoCart(product)}>Add to cart</button>
)

const mapStateToPros = (state) => {
  product: state.selectedProduct
}

const mapDispatchToProps = {
  addToCart: actions.addToCart,
}

export default connect(mapStateToPros, mapDispatchToProps)
~~~

Here we have the "product" details being provided from the store and the `addToCart` action creator available for being invoked when the user clicks the "Add to cart" button.

# Demo app

So we can put all this in practice, let's take on the project from the [challenge from chapter 3](/ch3-intro-to-react-js/README.md#demo-app) and this time pass the state management to redux.

[Redux]: https://redux.js.org/
[redux-promise-middleware]: https://github.com/pburtchaell/redux-promise-middleware
[redux-thunk]: https://github.com/gaearon/redux-thunk
[react-redux]: https://github.com/reactjs/react-redux
[Express]: https://expressjs.com/