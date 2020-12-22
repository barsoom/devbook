# JavaScript Styleguide

## Tooling

#### Avoid jQuery

Just don't use it. It's better to use standard APIs.

[Should you use or learn jQuery in 2020?](https://flaviocopes.com/jquery/)

#### Avoid lodash

You probably don't need it. And if you do, import the functions you need like this:

``` javascript
import foo from 'lodash/foo'
```

Don't do this:

``` javascript
import { foo } from 'lodash'
import _ from 'lodash'
```

The latter will make tree-shaking impossible.
Read more about [lodash and tree-shaking](https://www.azavea.com/blog/2019/03/07/lessons-on-tree-shaking-lodash/)

#### Use Prettier

Use our [prettier-config](https://github.com/barsoom/prettier-config)

#### Use eslint

Use [eslint-config-barsoom](https://github.com/barsoom/eslint-config-barsoom)

#### When using Babel

Try to avoid using too many experimental features.

Stage 3 is fine and stage 2 can be ok.

Just don't go crazy with everything shiny and new that is likely to change, or maybe not even get into the standard.

#### Prefer npm over yarn

When Yarn was initially released, it solved a lot of problems that npm had.

Since then, npm has improved a lot and nowadays there isn't really any reason to use yarn over npm.

[Why Are You Still Using Yarn in 2018?](https://iamturns.com/yarn-vs-npm-2018/)

#### Dependencies

In the npm repository, you can find packages for almost anything.

It might be tempting to add a dependency for something, but if it's simple enough it's probably better to implement it yourself. (Looking at you, [left-pad](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code/))

## Code style

#### Regular functions vs. arrow functions

Use `function` for named functions and arrow functions for unnamed closures.

``` javascript
// Avoid this
const foo = () => { /*...*/ }

// When you can do this
function foo() { /*...*/ }
```

Use arrows for functions that are passed as callbacks, since arrow-functions bind `this` automatically.

If you're using function-style components in React, you (usually) don't need `this`, so it's better to use named functions because they are easier to read. But always prefer arrows instead of `.bind(this)`, and for unnamed closures.

## React

The other guide is written for CoffeeScript so here's one is for JavaScript.

#### Wrap your app in `<React.StrictMode>`

This will enable some extra warnings in development mode.

[Read more here](https://reactjs.org/docs/strict-mode.html)

#### PropTypes

Read [Typechecking With PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html).

Our eslint rules will complain if you don't use them.

#### Callback names

Prop names for callbacks and events should be called `onSomething`, like `onClick` or `handleMouseOver` etc.

Example:

``` jsx
function Question({ onAccept, onDismiss, message }) {
  return (
    <div className={styles.question}>
      <p>{message}</p>
      <button onClick={onAccept}>Accept</button>
      <button onClick={onDismiss}>Dismiss</button>
    </div>
  )
}
```

Event handlers should be called `handleSomething`, like `handleClick`, `handleMouseOver` etc.

Example:

``` jsx
function MyComponent() {
  function handleAccept() {
    alert("Accepted!")
  }

  function handleDecline() {
    alert("Declined!")
  }

  return (
    <Question
      message="Do you want to continue?"
      onAccept={handleAccept}
      onDecline={handleDecline}
    />
  )
}
```

#### File structure

Name the file after the default export, so that `Foobar.js` exports the component `Foobar`.

Try to use 1 component for each file, especially if they have a lot of logic that can be separated.
It's ok to have multiple components in one file if they are closely related.
