---
title: Building a Roguelike in Javascript 2019 - part 0
layout: post
date: 2019-06-15 13:37
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
description: Building a Roguelike in JavaScript 2019 - part 0, what you need to make your first Roguelike game.
---

## What is a Roguelike?


Before we start this article I would like to clarify what a Roguelike is for those who do not know.

> Roguelike is a subgenre of role-playing video game characterized by a dungeon crawl through procedurally generated levels, turn-based gameplay, tile-based graphics, and permanent death of the player character. Most roguelikes are based on a high fantasy narrative, reflecting their influence from tabletop role playing games such as Dungeons & Dragons.
>
> > <a href="https://en.wikipedia.org/wiki/Roguelike" target="_blank">Roguelike - Wikipedia </a>


<center>
<img src="https://upload.wikimedia.org/wikipedia/commons/1/17/Rogue_Screen_Shot_CAR.PNG" alt="Rogue - The original Roguelike"/>
</center>

This definition is often described as the "Berlin Interpretation." Some notable classic Roguelikes include <a href='https://www.nethack.org/' target='_blank'>NetHack</a>, <a href='https://www.adom.de/home/index.html' target='_blank'>ADOM</a>, <a href='https://rephial.org/' target='_blank'>Angband</a>
, <a href='https://te4.org' target='_blank'>Tales of Maj'Eyal</a>
, and <a href='https://crawl.develz.org/' target='_blank'>DCSS</a>.

## Is making a Roguelike difficult in 2019?

Designing a Roguelike now has never been easier thanks to the use of widely available libraries. If you have ever taken a look at the <a href="https://github.com/NetHack/NetHack" target="_blank">NetHack source code </a> you would understand what a daunting task it use to be to write your own Roguelike. Fortunately enough, you can write your own Roguelike in nearly any language, including JavaScript.

JavaScript is actually quite a flexible language, relatively easy to pick up (although it does have some <a href='https://whydoesitsuck.com/why-does-javascript-suck/' target='_blank'>pitfalls</a>
). JavaScript more importantly is cross-platform, meaning you do not have to trouble yourself with compiling code across different operating systems (unlike NetHack, which manually has to be compiled on each operating system and ported over).

For this series, we will be using <a href='https://ondras.github.io/rot.js/hp/' target='_blank'>rot.js</a>. Unfortunately the tutorial available at this time for rot.js is somewhat outdated, considering the <a href='http://www.codingcookies.com/2013/11/25/building-a-roguelike-in-javascript-part-16/' target='_blank'>last blog post</a> was in 2013...

Therefore, I've decided to write my own Roguelike tutorial utilizing more modern features JavaScript has to offer. For this series we will be heavily utilizing the new features <a href="http://es6-features.org" target='_blank'>ES6</a> has to offer. Such features include: <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes" target='_blank'>Classes</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions" target='_blank'>Arrow Functions</a>, <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import" target='_blank'>imports and modules </a>, and using <a href="https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let" target='_blank'>scoped-based variables</a>, and much more. Don't worry about reading through all that dense material, for now, let's worry about making your life easier by setting up your development environment.

## Development Tools to make your life easier

![It's dangerous to only use notepad](/assets/images/blog/roguelikedev/its-dangerous-to-go-alone-take-this.jpg)

Programming can be difficult sometimes without the right tools. While you can write all your code in vim (what I initially did), using other development tools will help us enhance our workflow.

**NOTE: Just because I've used these tools, doesn't necessarily mean you can't use some other set of tools. For example, if you decide to use notepad++, atom, sublime, etc you can still follow along. Use what development environment your most comfortable with.**

### Tools

- <a href="https://code.visualstudio.com/download" target='_blank'>Visual Studio Code</a>
  - extensions: <a href="https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode" target='_blank'>Prettier</a>, <a href="https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer" target='_blank'>Live Server</a>
- <a href="https://git-scm.com/" target='_blank'>git</a> (optional - if you want to follow a long with the code)

If you already have VSCode and git installed on your system you can skip the following below to the last section, <a href="#how-to-follow-this-series" > How to follow this tutorial series</a>, if you are however new to progrmaming or do not have neither VSCode nor git than you may proceed reading. 

***



### Setting up your development environment and getting started

#### Step 1. Installing VSCode

Installing <a href="https://code.visualstudio.com/docs/setup/setup-overview" target="_blank">Visual Studio Code</a> is fairly straight forward. 

The reason we are using Visual Studio Code (VSCode for short...) over other editors such as Atom or Sublime is because of its simplicity, its power, and most importantly... its efficiency. In my experience Atom has been a very intensive resource hog on lower end machines, usually resulting in substantial slow down. Sublime on the other hand, lacks the flexibility Atom and VSCode has due to its limited options when it comes to extensions. 

**Note: You can follow this series using Atom or any other editor you desire (<a href="https://www.vim.org/" target="_blank">vim</a> ftw) however you would need to find a way to serve the index.html page we'll be making, similar to how we do it in Live Server (VSCode Extension). This could be achieved by setting up an Apache server and serving up the index.html as the main page or using a web hosting solution. The reason we need Live Server extension (or regular server) is because we'll get a CORS error when trying to import the library locally.**

##### Windows 
1. Download VSCode Installer from <a href="https://code.visualstudio.com/docs?dv=win" target="_blank">here</a>.
2. Run installer (default installation is %AppData%\Local\Programs\Microsoft VS Code) 


##### MacOS
1. Download VSCode Installer from <a href="https://code.visualstudio.com/docs?dv=osx" target="_blank">here</a>.
2. Double click on archive to extract. 
3. Drag "Visual Studio Code.app" into your Applications folder.


##### Linux (Ubuntu, Linux Mint, Debian, etc)
1. Install snap by running the following command in your terminal: `sudo apt update && sudo apt install snapd`
2. Install VSCode by running `sudo snap install --classic code` in your terminal. 


##### Arch Linux
1. Install yay (or any other AUR helper). By running the following in your terminal:
```
sudo pacman -S git
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
2. Install visual-studio-code-bin from the AUR by running the following command in your terminal: `yay -S visual-studio-code-bin`


#### Setting Up Visual Studio Code
1. Navigate to the Extensions menu by either going to View->Extensions or by pressing **Ctrl+Shift+X**
2. Search for Live Server and install.
3. Profit.

#### Installing git 

Git will be how we manage our code. Git is a distributed version control system. This tutorial series is broken down into parts, each part has its own separate 'branch.' We can use git to track what branch of code we are working on. 

By default most Linux and MacOS systems should have git installed by default. If however you don't you can do the following...

##### Windows 
1. Download <a href="https://gitforwindows.org/" target="_blank">Git for Windows</a>.
2. Install (I recommend using Git Bash)


##### MacOS
1. Install XCode/XCode Command Line Tools or download <a href="https://sourceforge.net/projects/git-osx-installer/files/">Git for Mac</a>
2. Follow installer prompts.


##### Linux (Ubuntu, Linux Mint, Debian, etc)
1. Run the following command in your terminal: `sudo apt update && sudo apt install git`.


##### Arch Linux 
1. Run the following command in your terminal: `sudo pacman -S git`.


##### Checking if git is installed

After you install git, make sure its running by running the following command either in your terminal or command prompt: `git --version`.

If git is properly installed you can proceed.

<h3 id="how-to-follow-this-series">How to follow this tutorial series</h3>

The easiest way to follow this series is by cloning the github repository for this series, and following along by checking out each branch. Note: part 1 will start from scratch, however you still need the lib/ folder which is part of rot.js. So we'll need to remove every other folder in the repo other than lib. 

*Alternatively you can download the repo as a zip file, and only extract the lib folder to where you want to work from.*

We can do this by running the following commands in our terminal:

```
git clone https://github.com/edgar-montano/jsroguelike
cd jsroguelike
rm -rf assets classes screenshots
rm LICENSE README.md index.html
```

The only files that should be remaining are the `lib` folder.

After you finish this you can now proceed to actually [**_coding your first Roguelike in JavaScript!_**](/building-a-roguelike-in-js-part1/){:target="_blank"}

