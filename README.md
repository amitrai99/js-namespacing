# JavaScript Namespacing

_Namespacing_ is a common software requirement because it allows us to create 
logically grouped components.

Different languages have different ways of namespacing components.
For example, Java uses the concept of `packages` for namespacing.
The packages are then imported using the `import` statement.

JavaScript offers various ways to namespace your components.
Some of the common ones as explained by [Addy Osmani](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#detailnamespacing) are:

* Single Global Object
* Prefix namespacing 
* Object literal notation
* Nested namespacing
* Immediately-invoked Function Expressions
* Namespace injection

## Single Global Object

Simplest namespacing mechanism involving just a single global object. 
Implemented using a simple IIFE and module pattern.

```js
var myApp = (function () {
  var bar = 'x';
  function foo() {
    console.log(bar);
  }
  //return an object that will form the global object
  return {
    callMe: foo
  };
})();
```
*Cons:*

The risk with this approach is that the global object `myApp` can get 
clobbered by any other script using the same variable name.

## Prefix namespacing 

Another very simple approach where we simply prefix the name of our module to every
object that we wish to use in our code.

```js
//Assuming we want to create a 'myApp' namespace
var myApp_foo = 'something';
var myApp_bar = {abc: 'xyz'};
var myApp_rab = function() {
  console.log('myApp_rab');
};
```
*Cons:*

* Clumsy
* Does not alleviate the problem of duplicate variable names clobbering each other
* Too many global objects are being created

## Object literal notation

JavaScript has a very powerful yet elegant way of creating objects dynamically
using object literals. We can use the JS object literals to create namespaces 
for our application.

There are a couple of ways of doing this:

* Creating Object Literal

```js
var myApp = {
  config: {
    color: 'red',
    weight: '80'
  },
  getColor: function() {return this.config.color;},
  getWeight: function() {return this.config.weight;}
};

//adding more properties to namespace
myApp.foo = function() {};
```

* Using revealing module pattern

```js
var myApp = (function(){
  var x = 'abcd';
  function privMethod() {}
  function pubMethod() {return x;};
  //return the public properties
  return {
    "foo": pubMethod
  };
})();
```

To avoid collisions we can check for the existence of the object or it's child.
For example,

```js
//check for existence 
var myApp = window.myApp || {};
myApp.foo = myApp.foo || {};
myApp.foo.bar = myApp.foo.bar || {};
```

*Cons:*

* The object structure can get deep and complex

## Nested namespacing

This is an extension of the Object literal notation used before.
This is useful if we have namespaces that can exist inside another namespace.

The main idea is that the chances of collision are less in a nested namespace. 
Also, we can detect and avoid collisions by checking the existence of namespace.

```js
//check for collision and existence
var myApp = window.myApp || {};
myApp.foo = myApp.foo || {};
myApp.foo.bar = myApp.foo.bar || {};
//add new namespaces/modules
myApp.foo.myMod1 = function(){};
myApp.foo.bar.myMod2 = {"func1": function() {}, "data": 'data'};
```

*Cons:*

* Deep nested objects can be time consuming to do lookup.

## Immediately-invoked Function Expressions (IIFE)s

IIFEs are very useful while creating modules because of their inherent execution context.
By passing the reference of an object to the IIFE we can create a namespace for it.

_Note_ that we are _not_ returning any object from inside IIFE but rather passing an
object into the IIFE which is then extended.

* Creating a namespace

```js
//Creating a namespace
(function(ns) {
  //private properties
  var px = '123';
  function func() {console.log('loggin log');}

  //add public properties to the namespace
  //you can access the private members from these
  ns.foo = function() {console.log(px)};
  ns.bar = function() {func();};
})(window.namespace = window.namespace || {}); //note the way we pass namespace object
```

* Extending a namespace

Extending namespace follows the same pattern as given above.
Pass the namespace object to the IIFE and then extend it.

```js
//Extending a namespace
(function(ns) {
  ns.moreFoo = function() {console.log('moreFoo');};
  ns.moreBar = function() {console.log('moreBar');};
})(window.namespace = window.namespace || {}); //note the way we pass namespace object
```
Advantage of this method is the access to the closure of IIFE which allows adding
functionality while preserving scope.

*Cons:*

* Closure can be a source of memory leaks

## Namespace injection

This is a variation of IIFE where we use `this` keyword to inject properties in a 
namespace object.

Advantage of this method is that we can add the same functionality to multiple namespaces.

*Cons:*

* `this` keyword is inherently confusing in JS
* Can be implemented by passing the namespace to an IIFE

```js
var myApp = myApp || {};
myApp.some = {};

//extend namespace
(function () {
  //"this" now references whatever we passed in to "apply" below
  //extend the object
 this.foo = "something";
 this.bar = function () {};
 this.mar = {};
}).apply(myApp.some); //we've passed the myApp.foo to "apply" method

//extend again a specific subnamespace
(function (){
  this.anotherFoo = function () {};
}).apply(myApp.mar);

myApp;
```

## Refererences
[Essential JS design patterns](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#detailnamespacing)
