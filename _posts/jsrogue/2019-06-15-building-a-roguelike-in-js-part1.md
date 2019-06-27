---
title: Building a Roguelike in JavaScript 2019 - part 1 
layout: post
date: 2019-06-25 13:37
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
description: Building a Roguelike in JavaScript 2019 - part 1, Hello, Rogue! Setting up rot.js, the first step in programming your own Roguelike!
---

# Hello, Rogue


Nearly every introduction to a programming language or framework has a 'Hello World' application as its first step. This is to ensure that you have properly configured your development environment. Therefore this section will aim to guide you to print out your own 'Hello, World' message on screen. 


## Overview

In this part of the Roguelike in JavaScript series we will be reviewing the following topics:
- [Deployment with Live Server](#live_server)
	- Shows how to serve index.html so we can make live edits to our application without the need of renting a server. 
- [Hello, Rogue - Printing to Display](#hello_rogue)
	- The goal of this part is to simply display 'Hello, World' in a rot.js container on our screen. 
	- [Imports and Destructuring](#importing_destructuring)
		- Talks about ES6 importing modules and destructuring them.
	- [Let their be options](#options)
		- Discusses scopes of variables
	- [Constructing a new Display](#classes)
		- Refers to ES6 Classes, and the DOM
	- [Looping and Printing in Color](#loops)
		- Teaches how to loop and print values in rot.js
- [Results](#results)
- [Full Source Code](#full_code)

If you have yet to setup your development environment, I suggest reading the [previous part of this series](http://localhost:4000/building-a-roguelike-in-js-part0/#how-to-follow-this-series){:target="_blank"} where I discuss how to setup VSCode and how to get the rot.js library. 

# Getting Started - Serving up some HTML 
{:#live_server}

The first step will involve us with setting up Live Server, and properly serving our `index.html` file, after this we will start working on our `assets/main.js` file which will be where we execute all our main JavaScript functions.

To get started open up VSCode and create a new file called `index.html` in the root folder of your project.  Inside our index.html will be the following code:

```
<!DOCTYPE html>
<html>
<title>jsroguelike - 2019</title>
<body>
<h1>JavaScript Roguelike</h1>
	<script type="module" src="assets/main.js"></script>
</body>
</html>
```

Here we are simply just setting up our template HTML. If you don't know HTML, then I highly recommend you [learn it](https://www.w3schools.com/html/default.asp){:target="_blank"}, however knowing HTML is absolutely unnecessary for this series. The most important part of this, in fact the only part that matters is the `<script></script>` tag. This is where we are loading our `main.js` file.

In order for us to see what's going on, we need to load up a local server and serve our `index.html`. In this case we'll be using the [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer){:target="_blank"} extension provided by [VSCode](https://code.visualstudio.com/){:target="_blank"}. 

To start Live Server simply right click on our `index.html` in VSCode, and click **Open with Live Server**. After that navigate to your web browser and enter the URL `localhost:5500`. This is the default location Live Server starts up. You should see a JavaScript Roguelike header appear. However we have yet to make the `assets/main.js` file so if we look at our console in our browser we will see an error `failed to load resource assets/main.js`. For Chrome users you can use the shortcut **Control+Shift+I** to pull up the console to see this message. 

![Serving our index.html with live server](/assets/images/blog/roguelikedev/running-live-server.gif)

# Hello, Rogue - Printing to the display
{: #hello_rogue}


In this section we will finally start writing in JavaScript. To start make a folder called `assets` and inside that folder a file called `main.js`. The point of creating an assets folder is to structure our code and organize it better, you can name the folder anything you want, however for the purpose of this tutorial we will be naming the folder assets. 

In `assets/main.js` we will start off by importing rot.js library. *_For this to work you must have followed the previous guide's section about [obtaining the lib folder in our project](/building-a-roguelike-in-js-part0/#how-to-follow-this-series){:target="_blank"}._*

We will start off by simply importing the rot.js library to our project. This gives us all the functionality rot.js has to offer. Furthermore we destructure Color, and Display from the rot.js namespace so we can simply type less, and for our code to be more readable. 

```
import {Color, Display} from '../lib/index.js'
```

## Importing and Destructuring (Optional Read)
{:#importing_destructuring}

[Import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import){:target="_blank"} is the way of JavaScript to include other modules exported from libraries into our file. In this case we are importing the rot.js library into our file. 

[Destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment){:target="_blank"} is an easy way of unpacking values from an object. In this case we destructure `Color` and `Display` from the rot.js library. Without this, everytime we need to call upon either the Color or Display object, we would need to write: `ROT.Color()` or `ROT.Display()`.  

These are two ES6 features that make our lives slightly easier, and we'll occasionally use them throughout the series, so understanding what it means will be helpful. 

## Let their be options
{:#options}

The next line of code in `assets/main.js` will be an optional object we will create to pass into our Display [constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor){:target="_blank"}. 

```
let options = { 
	width: 80,
	height: 24
};
```

The keyword `let` is a huge improvement from the previous [tutorial](http://www.codingcookies.com/2013/04/01/building-a-roguelike-in-javascript-part-1/){:target="_blank"}.  [Let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let){:target="_blank"} declares a block scope local variable, opposed to the previous way of creating variables in JavaScript: [var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var){:target="_blank"} which declares variables in the global scope. 

Previously if we defined our **options** variable with `var` instead of `let`, and we decided to make another **options** variable in another file, their would be a conflict between those variables, and it's possible that an entirely different file can affect our current options object. In general, using global scope variables is a bad idea and should be avoided as much as possible. With the introduction of `let` we can limit the scope of our variable to whatever block of code it is in, in this case, it is only limited to the `assets/main.js` file and no other file in our project, which is what we want. 


## Constructing a new Display
{:#classes}

We will finally initialize our `Display` class. This is where the meat of our application will reside in. The `Display` class will be used to print to the screen. 

In `assets/main.js` our next line of code is simple:

```
let display = new Display(options);
document.body.append(display.getContainer());
```

rot.js provides us with a canvas-based output that is suppose to resemble traditional TTY terminal. This output is our `display` class. The first line `let display = new Display(...);` initializes a class called `display`, by passing in the `options` object we created to the constructor of rot.js's Display class... wow that was a mouthful. Essentially what we did was initialize a [JavaScript class](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes){:target="_blank"}, a class is simply a paradigm of [Object-Oriented Programming](https://www.webopedia.com/TERM/O/object_oriented_programming_OOP.html){:target="_blank"} that allows us to structure our data into a more coherent object that can perform actions and retain values. In essence, it's a collection of objects, functions, and variables.

rot.js provides a [Display class](https://ondras.github.io/rot.js/manual/#display){:target="_blank"} that can take in an object that specifies options. These options are passed in through a constructor. A [constructor](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/constructor){:target="_blank"} basically is called when you first create a class with the `new` keyword. 

The next line has to do with the Document Object Model, or DOM for short. [The DOM is the programming interface for HTML documents](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction){:target="_blank"}. The DOM has to do with how your HTML document is formatted and displayed. `document.body.append(display.getContainer())` is simply calling the class method `display.getContainer()` which returns a DOM node of the display element. This element is then appended to the body of our `index.html`. This is how we can display our application. If we did not append the container to the body of our `index.html` then our Roguelike game will **never** display. 

*_Note since we destructure we do not have to write `let display = new ROT.Display(...);` instead we simply write `let display = new Display(...);`_*

## Looping and Printing in Color
{:#loops}

Below appending our display value into the DOM, we will now finally print our message to the screen. How exciting, 6 minutes into this article and we finally get to the part that matters. 😳

```
let foreground, background, colors, x;
for (let i = 0; i < 15; i++) {
  x = i * 20;
  foreground = Color.toRGB([255 - x, 255 - x, 255 - x]);
  background = Color.toRGB([x, x, x]);
  colors = "%c{" + foreground + "}%b{" + background + "}";
  display.drawText(2, i, colors + "Hello, Rogue!");
}
```

First we start off by initializing the `foreground`, `background`, `colors`, and `x` value. Currently if you tried accessing these values immediately after the first line, you'll get a not defined value, this is because we haven't assigned the value to anything yet, we simply initialized it. The reason behind this is because we do not want to continuously initialize the values within the loop, each time the loop is called if we had an initialization statement within the loop, we'll constantly be re-initializing the code, which is a waste of memory. 

The [for loop](https://www.w3schools.com/js/js_loop_for.asp){:target="_blank"} simply just loops everything inside the curly braces 15 times.  

The following lines where we set `foreground` and `background` involves RGB calculations which revolve around a Red Green Blue color system. The idea is the foreground will start light, with RGB values of 255,255,255, and the background starts off dark, with a RGB value of 0,0,0. As the program loops over, the values slowly decrease for the foreground, meaning it gets darker over each iteration, while the inverse is true for the background, it gets lighter over each iteration.

Next we set these values to a `colors` string, which colors our text according to the specified foreground and background.

Finally, and the most important aspect of our code, we finally use `display.drawText()` to write to the screen. This function should be fairly self-explanatory, it draws text to the display, in this case, we are drawing the string 'Hello, Rogue' with varying colors to the display. 


# Results 
{:#results}

And finally, visiting the browser we can see our 'Hello, World' being displayed. (Note yours should say 'Hello, Rogue')

![Making your own Roguelike in JavaScript - Part 1](/assets/images/blog/roguelikedev/part-1-hello-rogue.png)


# Full Code
{:#full_code}

#### index.html
```
<!DOCTYPE html>
<html>
<title>jsroguelike - 2019</title>
<body>
<h1>JavaScript Roguelike</h1>
	<script type="module" src="assets/main.js"></script>
</body>
</html>
```

#### assets/main.js

```
import { Color, Display } from "../lib/index.js";

let options = {
  width: 80,
  height: 20
};

let display = new Display(options);
document.body.append(display.getContainer());

let foreground, background, colors, x;
for (let i = 0; i < 15; i++) {
  x = i * 20;
  foreground = Color.toRGB([255 - x, 255 - x, 255 - x]);

  background = Color.toRGB([x, x, x]);

  colors = "%c{" + foreground + "}%b{" + background + "}";

  display.drawText(2, i, colors + "Hello, Rogue!");
}

```

