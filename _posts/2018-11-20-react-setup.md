---
layout: post
title: Setup React development environment on Mac OSX
date:   2018-11-20 10:30:39
categories: development react javascript node.js
author: Xun Li
---

# Setup React development environment on Mac OSX

## Content
1. [Install NVM](#InstallNVM)
2. [Install Node.js](#InstallNode.js)
3. [Create React App](#CreateReactApp)

>It is not the same age that you just open your vim and start writing codes to draft a prototype of a web app (client side), then debug your code in chrome. You need a better MVC model for your javascript based (web) application, so your project is more manageable and more towards a team development setup. I am not sure how well React will help you in development for now, and I will try it out.


As suggested by [React](), Node.js >= 6 and npm >=5.2 should be installed on your machine. Since there are so many different versions of Node.js, it's better to use NVM to install/manage Node.js.


> *If you know python and virtualenv for python, NVM is a close idea of python virtualenv. You can use NVM to install different versions of Node.js and specify to use a specific version of Node.js in different projects.*

## Install NVM<a name="IntallNVM"></a>

(NVM official github site [here](https://github.com/creationix/nvm))

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

If you already installed NVM and doesn't work anymore (like what I have), here is what you can install manually

```shell
# delete previous nvm
rm -rf ~/.nvm
# download nvm
git clone https://github.com/creationix/nvm.git .nvm
# use latest version (please update the number from [here](https://github.com/creationix/nvm))
git checkout v0.33.11
# activate nvm by sourcing it from your shell
. nvm.sh
```

Then, add NVM environment to your bash profile (e.g. ~/.bash_profile or ~/.bashrc or ~/.profile) by copying the following lines and appending them to your bash profile:

```shell
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

## Install Node.js<a name="InstallNode.js"></a>

Using NVM, you can install a specific version of Node.js:
```shell
nvm install 10.10.0
```

or just install the latest version of Node.js
```shell
nvm install node
```

Then, you can run node via nvm
```shell
nvm use node
```

or use a specific version of node
```shell
nvm use 10.10.0
```

## Create React App<a name="CreateReactApp"></a>

>If you want to use React in your existing non-React project. You can check [Add React to a Website]().

The following commands will create a frontend build pipeline and a simple React template for you.

```shell
npx create-react-app my-app
cd my-app
npm start
```

You will get some directories and files under your directory my-app:
```
.
├── README.md
├── node_modules
├── package.json
├── public
├── src
└── yarn.lock
```

## React development<a name="ReactDevelopment"></a>

### JSX Tags<a name="jsx"></a>

It's an "extension" of javascript (* .js), and it looks ugly at first. See following:
```javascript
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>; // <- this is so ugly

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

It seems like React has a new "data type" `const element = <h1>Hello, {name}</h1>;`, which looks so ugly. They call it JSX tag. But I guess the compiler will translate it into final javascript codes, plus some HTML code (for `<h1></h1>` part).

The curly braces `{name}` in this line should be a way to put any "javascript defined" variables or results from executing a javascript function in this ugly line. Or you can just think that you can put regular javascript staff inside `{}`, so that the compiler knows that you are pointing to some variables or functions.

`ReacDOM.render` also comes from nowhere. But you can guess what the two parameters are for: `ReacDOM.render(what, to_which_DOM_element)`

>[Babel](https://babeljs.io/) compiles JSX tag down to React.createElement() calls.

For example, the ugly line above is equal to:

```javascript
const element = React.createElement(
  'h1',
  'Hello, Josh Perez'
);
```

`React.createElement` will check your code (e.g. prevent Injection Attack) and generates an javascript object like this:

```javascript
const element = {
  type: 'h1',
  props: {
    // className: '',
    children: 'Hello, Josh Perez'
  }
};
```

> This object is called "React Element", which is the basic element that you will see on the screen. React will use these "React Elements" to construct the DOM and keep it up to date.

OK! Here you go. JSX tags or React elements are just in memory, until you call `ReactDOM.render` to put it in a DOM. Specfically, the DOM is a DIV with `id="root"`:

```HTML
<div id="root"></div>
```   

## Function Components

> The main purpose of the idea "Components" of React is for *resuable* and *encapsulate*.

Why call is "function" component? I think that's because all "component" looking element defined in JSX tag has been "translated" into a javascript object.

### 1. Composing Components

### 2. Extracting Components

### 3. State and Lifecycle of Component
> 1.Do not modify state directly;
2.Instead, use setState();
3.Only assign thie.state in the constructor;

### 4. Events of Component
