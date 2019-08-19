---
title: Building a Roguelike in JavaScript 2019 - part 2a
layout: post
date: 2019-06-29 13:37
image: assets/images/blog/roguelikedev/roguelikedev.png
headerImage: true
tags: [featured, javascript, roguelike, rot.js, programming]
tag:
  - javascript
  - roguelike
  - rot.js
  - programming
  - featured
star: true
categories: [javascript, programming]
author: edgarmontano
description: Building a Roguelike in JavaScript 2019 - part 2, Screen Management - how to setup input, output, and screen management.
---

# Screen Management - Winning, Losing, and Everything in Between (part 2a)

In this section we will guide you through screen management. Screen management is achieved by creating serparate screen states, these states are triggered by specific conditions. Some screen states include a victory screen also known as `winScreen` and a defeat screen also known as `loseScreen`.

We will do this by creating separate Classes which will abstract our functionality into a self-contained object, describing both specific behavior of the object, and what the object contains. In our case, we will be making a `Game` Class and a `GameScreen` Class.

This section covers creating the first part, the `Game` Class.

## Overview

In this part of the Roguelike in JavaScript series we will be reviewing the following topics:

- [Exporting Constant Options](#options) - Teaches about programming practices and how to export constants.
- [Game Class is in Session](#game_class) - Introduction into Object-Oriented Programming, and creating our main Game Class which our functionality will extend off of, as well as teaches a variety of new JavaScript concepts. - [What is this?](#this) - Learning this and context - [What is null?](#null) - Learning a new primitive. - [Listening to Events](#events) - Understanding event-driven programming. - [Getting Arrow Functions](#arrow) - Introducing modern ES6 JavaScript functionality. - [Exporting Default](#default) - Exporting a single item.
- [Full Source Code](#full_code)

# Exporting Constant Options

{:#options}

In our `assets` folder, we are going to make a new file name called `types.js`. We are going to refactor our code, and reinforced good programming practices. In this case, if you look at our `assets/main.js` file from the previous section, you'll notice that we defined a variable called `options`. The problem with this variable is quite subtle... what if we decide at a later point we wanted to change the _width_ and the _height_ of the screen, we would have to change this `options` variable. The values of _width_ and _height_ are currently fixed, meaning the only way to change them, is by navigating to this specific file, and editing them manually.

In programming these fixed numbers are sometimes referred to as _magic numbers_. _Magic Numbers_ usually possess and arbitrary value at first glance, and may seem random. _Magic Numbers_ are essentially numbers hardcoded into a program that appear quite random to another person reading the source code, for they may not understand the "source" or "use" of this specific number. _Magic Numbers_ or any hardcoded values in your program like this is usually a poor programming practice and should be avoided.

At this very moment, that may not seem like a big deal, however imagine if our code is a few thousands of lines, with hundreds of separate files for each object, monster, function, ...etc, that we create. Now do you see the problem? Finding that one line of code to edit will be a nightmare.

So we'll create a separate file to handle these options.

In `assets/types.js`

```javascript
export const HEIGHT = 24;
export const WIDTH = 80;
```

Our problem of the elusive variable is now gone. If you are wondering why the variable names are all uppercase, it's rather quite simple, its easier to identify a constant variable in a file, and therefore it's usually good programming practice.

This helps identify the difference between a local variable that may have a `width` parameter, opposed to our global config file's `WIDTH` parameter. If you haven't realized by now, [JavaScript is a case sensitive language](http://www.scriptingmaster.com/javascript/case-sensitivity-in-javascript.asp){:target="\_blank"}. Basically a case-sensitive language just simply means that the variable name `width` is different then `Width` or `WidTH` or in our case `WIDTH`. Essentially the case of each character matters in determing what variable it belongs to.

If you are wondering what `export const` prefix means... then well that's also rather simple.

The `export` is a [unique keyword](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export){:target="\_blank"} in JavaScript that you use when you want to include that functionality in another file in JavaScript. We use this because we plan on including this value throughout our program later on.

The `const` keyword is similar to `let` for creating variables, with one key distinction, the value is [immutable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const){:target="\_blank"}. Immutable simply just means the value cannot be re-assigned. So if we decide to declare the following line `HEIGHT = 25;` the code will spit back a `TypeError: Assignment to constant variable`. Meaning that you cannot reassign this variable once initialized. The benefit of a constant variable is that we know that it will always stay consistent and never change.

Unfortunately `const`has some pitfalls in JavaScript... primitive types work as intended, and are constant values, but reference type variables can be reassigned even if they are set as constants. This is because `const` isn't really constant in JavaScript. However for the purpose of this section, we do not have to worry about the details. If you want to understand the different types of variables in JavaScript you can [click here](https://javascript.info/types){:target="\_blank"} for more info.

To finish up our `assets/types.js` file you can add the following which we will use later on:

```javascript
export const KEYDOWN = "keydown";
export const KEYUP = "keyup";
export const KEYPRESS = "keypress";
```

This code won't necessarily be important right this moment, but later on we'll utilize it. Later on we'll have to take into account complex keyboard actions, and instead of managing a bunch of strings, we can easily add these as constants to our types file and therefore manage keyboard events much more easily.

# Game Class is now in session...

{:#game_class}
This section might be difficult to grasp at first, but I suggest you try to fully understand it because it'll be the key to becoming a successful programmer. We'll be delving into [Object Oriented Programming](https://en.wikipedia.org/wiki/Object-oriented_programming){:target="\_blank"} in this section, which is a key component in modern programming languages.

First we'll start off this section by creating a new folder called `classes` and in this folder we'll create a file called `Game.js`. This is where our main Game class will run.

Inside `classes/Game.js`

```javascript
import { Display } from "../lib/index.js";
import { KEYDOWN, KEYUP, KEYPRESS, HEIGHT, WIDTH } from "../assets/types.js";
```

This chunk of code should be fairly straight forward. The first line we are destructuring `Display` from `rot.js`. The next line we are doing the exact same thing, but with the types file we made earlier. We are essentially just including the functionality we are going to want in our program.

## Object Oriented Programming - Overview

> Object-oriented programming (**OOP**) refers to a type of computer programming (software design) in which [programmers](https://www.webopedia.com/TERM/P/programmer.html) define not only the [data type](https://www.webopedia.com/TERM/D/data_type.html) of a [data structure](https://www.webopedia.com/TERM/D/data_structure.html), but also the types of operations ([functions](https://www.webopedia.com/TERM/F/function.html)) that can be applied to the data structure.
>
> In this way, the data structure becomes an [object](https://www.webopedia.com/TERM/O/object.html) that includes both [data](https://www.webopedia.com/TERM/D/data.html) and functions. In addition, programmers can create relationships between one object and another. For example, objects can inherit characteristics from other objects.
>
> > [webopedia - oop](https://www.webopedia.com/TERM/O/object_oriented_programming_OOP.html)

We have already worked with an object before. Our `options` variable that contained the value `options = {width: WIDTH, height: height};` is an example of an object in JavaScript. Object's are simply containers for data. Object Oriented Programming therefore is just a programming paradigm that utilizes objects to describe specific behavior and patterns of data. We could have easily added a function to our `options` object called `getArea()` and in this function we can calculate the area of a rectangle given the `width` and `height` in our `options` object. This is the essence of Object Oriented Programming.

### Game Class

In `classes/Game.js`

```javascript
/*
 * Our main Game class, this is where GameScreen will extend from.
 */

import { Display } from "../lib/index.js";
import { KEYDOWN, KEYUP, KEYPRESS, HEIGHT, WIDTH } from "../assets/types.js";
class Game {
  /*
   * Create a game class.
   * @param options - an optional object with width and height values for display size.
   */
  constructor(options = { width: WIDTH, height: HEIGHT }) {
    this._display = new Display(options);
    this._currentScreen = null;

    //add an event listener to listen for keypresses.
    const bindEventToScreen = event => {
      window.addEventListener(event, e => {
        if (this._currentScreen !== null)
          this._currentScreen.handleInput(event, e);
      });
    };

    //this is what keys we desire to bind to the event listener
    bindEventToScreen(KEYDOWN);
    bindEventToScreen(KEYUP);
    bindEventToScreen(KEYPRESS);
  }
  /*
   * Helper function that simply returns the current display.
   */
  getDisplay = () => this._display;
}

// We need to export the Game class in order to extend it from GameScreen
export default Game;
```

You can think of a **Class** in programming as a collection of objects that have a specific set of actions and behaviors. Classes are the essence of Object-Oriented Programming. We can easily represent any data we want using this paradigm. Classes in JavaScript are actually [syntactic sugar over existing prototype-based inheritance first introduced in ES6](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes){:target="\_blank"}, however this is not really important for our case.

Let's dissect what is going on. All of our code is wrapped in our `Class Game {}` You can think of `Class`es in JavaScript as being _special functions_ or you can think of them as objects with functions. What makes a _Class_ a special function is that it has unique properties, one of them being it has a `constructor()` method.

The `constructor()` method is ran anytime a Class is initialized. So later on in our code you'll see this syntax `let game = new Game(option);` . In this instance we are initializing the `Game` class with our options variable by calling the `constructor()` method. It is important to note that the constructor will not run again after it is initialized. So the purpose of a `constructor` method in JavaScript is to initialize variables and "setup" the state of your `Class` .

In this case, we are just initializing it with our `options` variable we made earlier, this syntax is actually defining a default, so if you don't enter any parameters when initializing the `Class`, the default would be used, which in this game is the default values from our `types.js` file.

## What is this?

{:#this}

You may be puzzles why we are using `this` keyword. `this` has to do with maintaining the proper context. In the example below, `this` refers to the values `Game._display` and `Game._currentScreen` . `this` provides a way of distinguishing properties in a `Class` and values passed through a parameter.

```javascript
this._display = new Display(options);
this._currentScreen = null;
```

## What is null?

{:#null}
`null` is actually a [primitive data type](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null){:target="\_blank"} in JavaScript. `null` represents the absence of a reference for an object. It is essentially an empty value. We are initializing our `_currentScreen` value as `null` because we want to later on test to see if it has been reassigned to a new value, or if it still does not contain a valid value. `null` is a great way of setting up a placeholder value for future values, especially if you don't want it to retain a value until some sort of action occurs, in this case, we want our `_currentScreen` to only change when we call a switch screen function we'll define later.

## Listening to Events

{:#events}
We need a way for actions dispatched by the user to be captured and processed by our game, in this case we use **events**. An **event** is an important concept in JavaScript. Events are generated by user interaction with the web page, in order to capture and interact with events we need to add an event listener, which will listen for very specific events, and allow us to interact with those events.

```javascript
//add an event listener to listen for keypresses.
const bindEventToScreen = event => {
  window.addEventListener(event, e => {
    if (this._currentScreen !== null) this._currentScreen.handleInput(event, e);
  });
};

//this is what keys we desire to bind to the event listener
bindEventToScreen(KEYDOWN);
bindEventToScreen(KEYUP);
bindEventToScreen(KEYPRESS);
```

In this case we are listening for keyboard events. If the `_currentScreen` value is not set... that is the value is set to `null`, we handle the event being given.

## Getting Arrow Functions

{:#arrow}
In Object-Oriented Programming its common practice to create what we call _Getters_ and _Setters_. These are simply methods that either return a value, or set a value part of an Object. It is often preferred to call these methods rather than to access the values directly cause we may unintentionally set a value that may break functionality, by limiting access to a value through a _Getter_ or _Setter_ we can ensure that our data is accessed the way we intend.

```javascript
/*
 * Helper function that simply returns the current display.
 */
getDisplay = () => this._display;
```

This is a simple helper function, more specifically it's a _Getter_ method designed to just return the value of the display object in our class. However the notation it is written in is rather peculiar. This is known as arrow function.

Traditional functions require the `function` keyword in front of it, however with ES6 we now have [Arrow Functions](https://www.w3schools.com/js/js_arrow_function.asp){:target="\_blank"} which are a short hand for making functions. This same function can be written as

```javascript
function getDisplay() {
  return this._display;
}
```

In addition to the short hand, we do not have to specify the `return` keyword in Arrow Functions when we are only returning one value. Another example of Arrow Functions to illustrate this abstraction is as follows:

```javascript
//old way of writing functions
function add(x, y) {
  return x + y;
}
//es6 arrow functions
add = (x, y) => x + y;
```

## Exporting default

{:#default}
The last line of our `Game.js` function is rather simple, we are telling JavaScript that only one value will be exported in this Class, so there is no need to use our destructuring syntax when importing our class into another file.

```javascript
export default Game;
```

This is similar to what we did earlier on with our `types.js` file, however we are only exporting one value, so we set it as the default value exported.

# Full Code

{:#full_code}

In `assets/types.js`

```javascript
export const KEYDOWN = "keydown";
export const KEYUP = "keyup";
export const KEYPRESS = "keypress";

export const HEIGHT = 24;
export const WIDTH = 80;
```

In `classes/Game.js`

```javascript
import { Display } from "../lib/index.js";
import { KEYDOWN, KEYUP, KEYPRESS, HEIGHT, WIDTH } from "../assets/types.js";
class Game {
  constructor(options = { width: WIDTH, height: HEIGHT }) {
    this._display = new Display(options);
    this._currentScreen = null;

    const bindEventToScreen = event => {
      window.addEventListener(event, e => {
        if (this._currentScreen !== null)
          this._currentScreen.handleInput(event, e);
      });
    };

    bindEventToScreen(KEYDOWN);
    bindEventToScreen(KEYUP);
    bindEventToScreen(KEYPRESS);
  }
  getDisplay = () => this._display;
}

export default Game;
```
