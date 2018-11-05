JSX Pragmatic
-------------

Build JSX structures, then decide at runtime which pragma you want to use to render them.

#### Build an abstract jsx component

```javascript
/* @jsx node */

import { node } from 'jsx-pragmatic';

function Login({ prefilledEmail }) {
  return (
    <section>
      <input type="text" placeholder="email" value={prefilledEmail} />
      <input type="password" placeholder="password" />
      <button>Log In</button>
    </section>
  );
}
```

#### Render on the server

```javascript
/* @jsx node */

import { node, html } from 'jsx-pragmatic';
import { Login } from './components'

function render() {
  return (
    <Login prefilledEmail='foo@bar.com' />
  ).render(html());
}
```

#### Render on the client

```javascript
/* @jsx node */

import { node, dom } from 'jsx-pragmatic';
import { Login } from './components'

function render() {
  return (
    <Login prefilledEmail='foo@bar.com' />
  ).render(dom());
}
```

#### Render in a React app

```javascript
/* @jsx node */

import { node, react } from 'jsx-pragmatic';
import { Login } from './components'

function render() {
  return (
    <Login prefilledEmail='foo@bar.com' />
  ).render(react({ React }));
}
```

### Write your own renderer

```javascript
/* @jsx node */

import { node } from 'jsx-pragmatic';
import { Login } from './components'

function customDom({ removeScriptTags = false } = {}) {

  let domRenderer = (name, props, children) => {
    if (removeScriptTags && name === 'script') {
      return;
    }

    let el = document.createElement(name);

    for (let [ key, val ] of Object.entries(props)) {
      el.setAttribute(key, val);
    }

    for (let child of children) {
      if (child.isTextNode()) {
        el.appendChild(document.createTextNode(child.text()));
      } else {
        el.appendChild(child.render(domRenderer));
      }
    }

    return el;
  }

  return domRenderer;
}


function render() {
  return (
    <Login prefilledEmail='foo@bar.com' />
  ).render(customDom({ stripScriptTags: true }));
}
```

### Why?

[JSX](https://reactjs.org/docs/introducing-jsx.html) is a neat way of parsing and compiling templates to vanilla javascript. Right now most people use JSX with [React](https://reactjs.org/). But in reality, the technology is decoupled enough from React that it can be used to render anything:

- HTML
- XML
- DOM Nodes

This library helps you do that.

### Can't you do that with Babel?

Yep, Babel provides a neat `pragma` [option](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx#pragma) which lets you choose what your jsx is compiled to; if you don't want to use `React.createElement`, you can write your own pragma to convert the jsx to anything else.

The only problem with that is, the decision of which pragma to use is made entirely at build-time. Let's say you have a template which needs to be:

- Rendered as an html string on the server side.
- Rendered directly as a DOM element in some client environments.
- Rendered as a React component in other client environments.

`jsx-pragmatic` helps you achieve that by allowing you decide when you render what your jsx should be transformed into.


Quick Start
-----------

#### Install

```bash
npm install --save jsx-pragmatic
```

#### Getting Started

- Fork the module
- Run setup: `npm run setup`
- Start editing code in `./src` and writing tests in `./tests`
- `npm run build`

#### Building

```bash
npm run build
```

#### Tests

- Edit tests in `./test/tests`
- Run the tests:

  ```bash
  npm run test
  ```

#### Testing with different/multiple browsers

```bash
npm run karma -- --browser=PhantomJS
npm run karma -- --browser=Chrome
npm run karma -- --browser=Safari
npm run karma -- --browser=Firefox
npm run karma -- --browser=PhantomJS,Chrome,Safari,Firefox
```

#### Keeping the browser open after tests

```bash
npm run karma -- --browser=Chrome --keep-open
```

#### Publishing

##### Before you publish for the first time:

- Delete the example code in `./src`, `./test/tests` and `./demo`
- Edit the module name in `package.json`
- Edit `README.md` and `CONTRIBUTING.md`

##### Then:

- Publish your code: `npm run release` to add a patch
  - Or `npm run release:path`, `npm run release:minor`, `npm run release:major`