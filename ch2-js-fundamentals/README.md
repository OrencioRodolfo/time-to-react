- [Hold your horses!](#hold-your-horses-)
- [JS quiz](#js-quiz)
  * [Scopes](#scopes)
  * [Hoisting](#hoisting)
  * [Closures](#closures)
  * [Sync vs Async](#sync-vs-async)
  * [Promises](#promises)
- [Answers](/ch2-js-fundamentals/quiz-answers.md)
- [Resources](#resources)

# Hold your horses!

Before you start diving into any library/framework I highly recommend you to be comfortable with JavaScript on its own.  
Otherwise you'll be missing important aspects about JS that will definetely help you to better uderstand and use these.

There are tons and tons of resources about JavaScript in every corner, but from my readings my recommendation would be [You Don't Know JS (book series)] from [Kyle Simpson](https://github.com/getify).

In fact, me and some friends have wrote a sort of `".gzip"` version of this book series and is available at [Watch your language] repo.

Because of this, I decided to build a small quiz with different questions about different topics. All the answers can be found [here](/ch2-js-fundamentals/quiz-answers.md) but I would suggest you to try anwsering to all these before checking the answers.

# JS quiz

## Scopes
1. What are scopes?

2. Describe *function scope* vs *block scope*

3. Identify how many scopes we have here and what's the output.
~~~javascript
function foo(a) {
    var b = a * 2;

    function bar(c) {
        console.log( a, b, c );
    }

    bar(b * 3);
}

foo(2);
~~~

## Hoisting
1. What's the output of the following code snippets?
~~~javascript
a = 2;
var a;
console.log(a); 
~~~

~~~javascript
console.log(a);

var a = 2;
~~~

~~~javascript
foo();

var foo;

function foo() {
    console.log( 1 );
}

foo = function() {
    console.log( 2 );
};
~~~

~~~javascript
const foo = () => {
  const bar = 'hey';

  {
    const baz = 'you!';
  }

  console.log(`${bar} ${baz}`);
}
~~~


## Closures
1. What is a closure?

2. What's the output of the following code snippets?
~~~javascript
var x = 10;

function foo() {
  var y = 20; // free variable
  
  return function bar() {
    var z = 15; // free variable
    return x + y + z;
  }
}

var baz = foo();
baz();
~~~

~~~javascript
function hello() {
    var message = 'Hello, ';

    return function greet(name) {
      message += name + '!';
      console.log(message);
    }
}

var hey = hello();
hey('John');
~~~

## Sync vs Async
1. What's the output of the following code snippets?

~~~javascript
(function() {
    console.log(1); 
    setTimeout(function(){console.log(2)}, 1000); 
    setTimeout(function(){console.log(3)}, 0); 
    console.log(4);
})();
~~~

2. Is JS single-threaded?

3. What is the Event Loop?

## Promises

1. What's the order of execution of each one of the following functions?

~~~javascript
doSomething().then(function() {
  return doSomethingElse();
});

doSomething()
  .then(function() {
    doSomethingElse();
  })
  .then(finalHandler);

doSomething()
  .then(doSomethingElse)
  .then(finalHandler);
~~~


# Resources

* [You Don't Know JS (book series)]
* [Watch your language]


[You Don't Know JS (book series)]: https://github.com/getify/You-Dont-Know-JS
[Watch your language]: https://github.com/Exictos-DCS/watch-your-language