- [Introduction](#introduction)
  * [SPA vs MPA](#spa-vs-mpa)
  * [SPAs + REST APIs](#spas---rest-apis)
    + [Lifecycle](#lifecycle)
  * [Automation and bundling tools](#automation-and-bundling-tools)
    + [Load order](#load-order)
    + [Web component based architecture](#web-component-based-architecture)
  * [Ecmascript](#ecmascript)
    + [ECMAScript 6 and ECMAScript Next](#ecmascript-6-and-ecmascript-next)
    + [Browser support](#browser-support)
- [Frontend frameworks/libraries](#frontend-frameworkslibraries)
- [Beyond the Browser](#beyond-the-browser)
  * [Server side](#server-side)
  * [Desktop](#desktop)
  * [Mobile](#mobile)

# Introduction

Before we start diving into [ReactJS] let's take a view over the past years and understand why did we ended up with libraries and frameworks like React, Angular, Vue and others.  

I think that, more important than learning how to work with any of these, is why you would need to. There's never a *"one fits all solution"* and when it comes to software development that's specially true.  
With this in mind, **always consider reading before building!**

## SPA vs MPA
Multi-page vs Single-page applications is an important matter when it comes to libraries like the ones we're talking here.

Multi-page applications are those more "traditional" websites where every link or form submit triggers a browser refresh.  
Single-page applications, in the other hand, don't require page reloads during its use. There's tons of SPAs today, for instance: GMail, Facebook, Google Maps...  

They are known for being fast and for giving the user a smoother experience but they're generally more complex to develop.  
[SEO] is harder to achieve and your JavaScript code has to be much better maintained. Avoid globals, side effects, memory leaks and a bunch of other problems that I myselft never worried about when I was still a rookie.

They don't require page reloads when clicking on a link or submiting a form, but you still need the page to exchange data with the server in response to these actions. It is all handled by JavaScript and AJAX requests. So, for SPAs you'll end up with much more JavaScript code than in a traditional MPA. And this brings a whole new set of problems that you wouldn't face before when using MPA. 

<p align="center">
  <img alt="Amount of JS code" src="https://i.imgur.com/X2T6i9s.png" width="600" />
  <br>
  <span>Amount of JS code with MPAs and SPAs</span>
</p>

Now you had to handle form submits, validations, Ajax requests, DOM manipulation in response to user's actions, handle dynamic content... And then you'll find your self with files like:

<p align="center">
  <img alt="Huge files" src="https://i.imgur.com/Tz4hPId.png" width="600" />
  <br>
  <span>Large JS files</span>
</p>

This huge amount of JavaScript lead us to several issues that we didn't face when using MPAs, like:

* **Page loading time**: There's much more code to download, much more JavaScript.  
* **Memory leaks, side effects**: Your code has to be scoped, namespaced. You can't give you the luxury of working with globals and expect the [Garbage Collector]() to do the dirty job all on its own.  
* **Service Engine Optimization**: Content is loaded via Ajax. No more static meta tags for indexing.
* **You still need those links**: Routing. We still need them to be present in the browser's URL bar, otherwise how would you know which content to present when the page first loads?

But hey! Nothing lost here. That's where frontend libraries/frameworks and automation and bundling tools come into play. Yes, we're talking about React, Angular... and [Webpack](https://webpack.js.org/).  
[Webpack] is not our focus on this course but we'll take a brief overview about it. As it has a very important role when it comes to the implementation of several solutions for the above mentioned issues.

For now, I can tell you that we'll be talking about:
* JS modules
* Client side routing
* JS bundles and lazy loading
* SSR - Server Side rendering

In each one of these topics we'll see how we can overcome these new challenges when implementing SPAs.

## SPAs + REST APIs
With SPAs, decoupling your frontend application from the backend has never been so easy.  
The server can provide data through an **[RESTful API](http://searchmicroservices.techtarget.com/definition/RESTful-API)** and all the templating and UI rendering is no longer its responsibility, it only has to provide data and the frontend app will take care of the rest.

This way you can treat your backend and frontend as completly separated and independent projects. Each one with its own versioning, architecture, stack, development team...  
Which is really cool, as you can scale your apps in a very modular and easy way. Frontend apps can consume data from different APIs and the same API can serve different frontend apps.  
Plus, REST APIs are stateless. Which means that you can replicate your server as much as you need since it doesn't matter which server is the app talking to.

**Example:** You have a e-commerce website and a mobile application that sell the same products/services. A common approach would be having two different frontend apps (web and mobile) consuming the same API.

### Lifecycle
So, at the end of the day that's what we get:

* MPA will always response to your requests with new pages, **new HTML**, new content.
* SPA will response with **HTML** when you **first access** to the page and from there fore the SPA will communicate with the server's **API** using **`JSON`** format.

<table>
  <tr>
    <td align="center">
      <img alt="MPA Lifecycle" src="https://i.imgur.com/JT5cytZ.png" />
      <br>
      <span>Multi Page Application request lifecycle</span>
    </td>
    <td align="center">
      <img alt="SPA Lifecycle" src="https://i.imgur.com/chP0yYa.png" />
      <br>
      <span>Single Page Application request lifecycle</span>
    </td>
  </tr>
</table>

## Automation and bundling tools
Nowadays when talking about SPAs, talk about automation and bundling tools like [Grunt], [Gulp], [Browserify] and [Webpack] is as much indispensable as talking about frontend libraries and frameworks.

All these have given an important contribuition for the frontend development world as we know it, each one with its approach and purpose but [webpack] has definetely marked its position in the community.  
Is the most used and known tool for not only bundling JavaScript, but also giving you the hability to transform, bundle or even package any other resource or asset.

These bundling tools gave us the opportunity to use [JS modules] on our browsers as we knew them in [Node.js]. That was mindblowing! You could actually `export` and `import` and the browser would just understand it!  
(Ok... Actually the browser does not understand any [Module System] (like [CommonJS, AMD or ES2015]) but you can use them and the bundling tool will take care of it.)

This way you can avoid those huge JS files like the above mentioned `main.js` file (which are a pain in the neck to maintain) and split them in several modules, maintaining all its code scoped.  

Then, bundling tools like webpack will take all these tiny little files in the collection and bundle them into a single file (or several, depending on what you whish). During this proccess several transformations can be applied to your code, like transpiling it with [Babel] (We'll talk a little more about this when talking about ES6).

<p align="center">
  <img alt="Bundling" src="https://i.imgur.com/FGemk5V.png" width="600" />
  <br>
  <span>Webpack bundling</span>
</p>

### Load order
Ok, we have this collection of JS files that are then bundled but there's something you must question yourself about, the load order.

Lets supose we have a `profile.js` file with the following code:  
~~~javascript
function getUserAvatar(userID) {
  return UsersService.getUser(userID).then(function result(user) {
    return user.avatar
  })
}
~~~

As you can see, this code depends on `UsersService` and this code is placed at `users.js` file, so we would need to import it into `profile.js` like:

~~~javascript
import UsersService from './users'

function getUserAvatar(userID) {
  return UsersService.getUser(userID) ...
}
~~~

And this will produce a loading tree like the following.
<p align="center">
  <img alt="Load order" src="https://i.imgur.com/3fPcvHF.png" width="400" />
  <br>
  <span>Load order</span>
</p>

> A bundle is nothing more than a group of modules (and their dependencies) into a single file (or group of files) in the correct order.


### Web component based architecture
This opportunity of building your collections of JS files along with ReactJS brought us a new way of thinking and architecture our frontend appplications. The [Web component based architecture] is this idea of encapsulating individual pieces of a larger user interface into self-sustaining, independent micro-systems known as **components**. 

With this in mind, your applications would become something like this:

<p align="center">
  <img alt="JS Modules" src="https://i.imgur.com/oQXoNME.png" width="500" />
  <br>
  <span>Component based architecture</span>
</p>

Where components are small pieces of UI that compose larger screen sections and pages, and services are JS files with some business logic and AJAX requests.

## Ecmascript

So, Ecmascript.... Hmmm... This will be a little confusing to be honest :sweat_smile:

Starting with **Ecma International** and **ECMA-262**.  
**Ecma International** is an organization that creates standards for technologies and that have published **ECMA-262**, a standard containing the specification for a general purpose scripting language.  
Then we have **ECMAScript**, which is the specification defined in ECMA-262 for creating a general purpose scripting language.  
And finally **JavaScript**! A general purpose scripting language that conforms to the ECMAScript specification. (It is basically ECMAScript at its core but builds upon it.) :scream:

<p align="center">
  <img alt="Mind blowing" src="https://i.imgur.com/WrKPhfd.gif" width="200" />
  <br>
  <span>"My understanding about ECMAScript"</span>
</p>

> By reading the ECMAScript specification, you learn how to create a scripting language. By reading the JavaScript documentation, you learn how to use a scripting language.

### ECMAScript 6 and ECMAScript Next

**ECMAScript 6** (also known as ES2015 or ECMAScript 2015) is the sixth edition of the ECMA-262 standard, and features major changes and improvements to the ECMAScript specification. 

And then we have **ECMAScript Next** (aka **ES.Next**), which is a dynamic name that refers to whatever the next version is at time of writing. Its features are more correctly called proposals, because, by definition, the specification has not been finalized yet.

Still confused? You can read more about it here: [What’s the difference between JavaScript and ECMAScript?]

### Browser support

You can use the shiny new features that come with **ES.Next**, but careful about cross-browser compatibility. Browser may not fully support features from its specification.

<p align="center">
  <img alt="Browser support" src="https://i.imgur.com/JsY9X0E.png" width="500" />
  <br>
  <span>Browser support table in the <a href="https://en.wikipedia.org/wiki/ECMAScript">ECMAScript Wikipedia page</a></span>
</p>

For better browser compatibility you can use [Babel] to convert ES.Next code to a functionally equivalent version that only use ES5 features. All of the major browsers fully support ES5, so they can run the code without any issues.

In fact, **Babel** transpiling is one of those "webpack's transformations" that we were talking about.  
You can write your code using ES.Next and then, at bundling time your ES.Next code will be transformed into ES5.


# Frontend frameworks/libraries

> First, software ate the world, the web ate software, and JavaScript ate the web. In 2018, React is eating JavaScript. - *Eric Elliot, [Top JavaScript Libraries & Tech to Learn in 2018]*

There are tons of solutions about building frontend apps nowadays. But the ones you'll probably heard about are [Angular], [VueJS] and (obviously) [ReactJS].

Like I've said before, there's no "one fits all solution". Chose wisely based on your project.  

We keep saying "[Angular] framework" and "[ReactJS] library", but why this difference? Why is Angular considered a framework and React a library?  
Well, React focuses only on building User Interfaces while Angular focuses on building Single Page Applications.  
What this means is that Angular gives you much more functionality like routing and form validation. It is built to control the whole user experience.

But isn't this possible to achieve with React too? It is, but not only with react itself. You'll need some other libraries from the React ecosystem like [react-router] or [redux-form].

React has been the one gaining more traction these past years but hey, don't chose it blindly.  
I could give you a much more deeper overview about each one of these, but I wouldn't dare trying to replace other such good resources about this topic.

Here they are:
* [Top JavaScript Libraries & Tech to Learn in 2018]
* [Choosing the Right JavaScript Framework
for Your Next Web Application](https://d585tldpucybw.cloudfront.net/docs/default-source/whitepapers/telerik-com/choose-the-right-javascript-framework-for-your-next-web-application_whitepaper.pdf?sfvrsn=faae52de_4&download=true)
* [The state of JavaScript 2017](https://stateofjs.com/2017/front-end/results)

# Beyond the Browser
JavaScript began as this little and simple scripting language for building some minimal dynamic behaviours in our web pages. But it has grown in such a manner that nowadays it's available not only in our browsers but also in our servers, mobile Operating Systems and even on our desktops. And you can thank this to [Node.js] and [V8's engine](https://developers.google.com/v8/) :smiley:

## Server side

> Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js' package ecosystem, npm, is the largest ecosystem of open source libraries in the world.

How cool is this? You can now access the file system in you machine, connect to a database, listen to HTTP requests... And thanks to this you can:
* Build a web server ([express], [koa]...)
* Build utility packages like [Grunt], [Gulp], [Yoman]...

## Desktop

> Electron is a framework for creating native applications with web technologies like JavaScript, HTML, and CSS. It takes care of the hard parts so you can focus on the core of your application.

[Electron] uses Chromium and Node.js so you can build your app with HTML, CSS, and JavaScript for both for Mac, Windows and Linux. 

There are several desktop apps built with Electron that you may already know:  
* [Atom](https://electronjs.org/apps/atom)
* [Visual Studio Code](https://electronjs.org/apps/visual-studio-code)
* [Slack](https://electronjs.org/apps/slack)

## Mobile
JavaScript started being used for mobile development using options like [PhoneGap] and [Cordova], where the approach was to wrap web code in a WebView, and use that WebView to drive a native mobile application. This allows web developers to build mobile apps with skills that they already have - namely JavaScript - and because of that Cordova has remained a compelling option for building mobile apps for many years.

But that's starting to change. Today, Cordova is being challenged by alternative development approaches, like [Progressive Web Apps] ([PWAs]) and JavaScript-driven native apps.

[PWAs] bring many native-like features to the web world, such as push notifications, offline access, and home screen icons but is still based on the web.  

On the other hand, JavaScript-driven native apps like Facebook's [React Native] and Progress's [NativeScript] do not use a WebView, therefore, they don't suffer the same web-based performance problems that can plague Cordova-based applications.
Well known mobile apps [Facebook] and [Instagram] are examples of mobile apps built using React Native.

[SEO]: https://developer.mozilla.org/en-US/docs/Glossary/SEO
[ReactJS]: https://reactjs.org
[React Native]: https://facebook.github.io/react-native/
[Nativescript]: https://www.nativescript.org/
[Webpack]: https://webpack.js.org
[Grunt]: https://gruntjs.com/
[Gulp]: https://gulpjs.com/
[Browserify]: http://browserify.org/
[JS modules]: https://medium.freecodecamp.org/javascript-modules-a-beginner-s-guide-783f7d7a5fcc
[Node.js]: https://nodejs.org/en/
[Module System]: https://auth0.com/blog/javascript-module-systems-showdown/
[CommonJS, AMD or ES2015]: https://auth0.com/blog/javascript-module-systems-showdown/
[Babel]: https://babeljs.io/
[Web component based architecture]: https://medium.com/@dan.shapiro1210/understanding-component-based-architecture-3ff48ec0c238
[What’s the difference between JavaScript and ECMAScript?]: https://medium.freecodecamp.org/whats-the-difference-between-javascript-and-ecmascript-cba48c73a2b5
[Angular]: https://angular.io
[VueJS]: https://vuejs.org/
[Top JavaScript Libraries & Tech to Learn in 2018]: https://medium.com/javascript-scene/top-javascript-libraries-tech-to-learn-in-2018-c38028e028e6
[express]: https://expressjs.com/
[koa]: http://koajs.com/
[Yoman]: http://yeoman.io/
[Electron]: https://electronjs.org/
[PhoneGap]: https://phonegap.com/
[Cordova]: https://cordova.apache.org/
[Progressive Web Apps]: https://developers.google.com/web/progressive-web-apps/
[PWAs]: https://developers.google.com/web/progressive-web-apps/
[Facebook]: https://www.facebook.com/
[Instagram]: https://www.instagram.com/
[react-router]: https://github.com/ReactTraining/react-router
[redux-form]: https://redux-form.com/7.3.0/