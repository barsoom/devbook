# JavaScript Styleguide

## CoffeeScript

Avoid it.

Try not to add more to projects that already use it. (Though it's fine if that's easier.)

## Tooling

### Avoid jQuery

Don't add jQuery to new projects. You probably don't need it.

Try not to add more to projects that already use it. (Though it's fine if that's easier.)

Prefer vanilla JS or let something like React handle the DOM for you.

In projects where we already use jQuery, it's fine to keep using it, but consider it legacy and something we want to get away from.

Read more:

- [Should you use or learn jQuery in 2020?](https://flaviocopes.com/jquery/)

### Avoid lodash

You probably don't need it – see ["Avoid jQuery"](#avoid-jquery).

If you do use it, import only the functions you need, like this:

``` javascript
import foo from 'lodash/foo'
```

Don't do this, because [it will make tree-shaking impossible](https://www.azavea.com/blog/2019/03/07/lessons-on-tree-shaking-lodash/):

``` javascript
import { foo } from 'lodash'
import _ from 'lodash'
```

### Do not use moment.js

[You don't need Moment.js](https://github.com/you-dont-need/You-Dont-Need-Momentjs).

Use [date-fns](https://www.npmjs.com/package/date-fns) instead. It's small, modular and supports tree-shaking.

### Use Prettier

Use our [prettier-config](https://github.com/barsoom/prettier-config)

### Use eslint

Use [eslint-config-barsoom](https://github.com/barsoom/eslint-config-barsoom)

### When using Babel

Try to avoid using too many experimental features.

Stage 3 is fine and stage 2 can be OK.

Just don't go crazy with everything shiny and new that is likely to change, or maybe not even get into the standard.

### Dependencies

Avoid adding npm dependencies for simple things.

It might be tempting to add a dependency for something, but if it's simple enough it's probably better to implement it yourself. (Looking at you, [left-pad](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code/)).

## Code style

### Regular functions vs. arrow functions

Use `function` for named functions and arrow functions for unnamed closures.
The reason for this is that `function foo` easier to read (less syntax).

``` javascript
const foo = () => {
  // Avoid this
}

function foo() {
  // When you can do this
}
```

Use arrows for functions that are passed as callbacks, since arrow functions bind `this` automatically.

If you're using function-style components in React, you (usually) don't need `this`, so it's better to use named functions because they are easier to read. But always prefer arrows instead of `.bind(this)`, and for unnamed closures.

### camelCase in object properties

Do:

``` javascript
const obj = {
  fooBar: {
    barBaz: 123
  },
}
```

Don't:

``` javascript
const obj = {
  foo_bar: {
    bar_baz: 123
  },
}
```

If you're working with an API that uses snake\_case, there are libraries that can transform keys between camelCase/snake\_case in nested objects. Just plug them into your API client.

## React

*TODO: Merge this with [our React styleguide](/styleguide/react). That guide may be outdated and uses CoffeeScript.*

Read and understand [the React documentation](https://reactjs.org/docs/hello-world.html). It's pretty short.

### Wrap your app in `<React.StrictMode>`

This will enable some extra warnings in development mode.

[React.js docs about Strict Mode](https://reactjs.org/docs/strict-mode.html)

### Prefer function components over class components

Do this:

``` jsx
function Counter() {
  const [count, setCount] = React.useState(0)

  function handleDecrement() {
    setCount(count - 1)
  }

  function handleIncrement() {
    setCount(count + 1)
  }

  return (
    <div>
      <p>Count: {count}</p>
      <div>
        <button onClick={handleDecrement}>-</button>
        <button onClick={handleIncrement}>+</button>
      </div>
    </div>
  )
}
```

Instead of this:

``` jsx
class Counter extends React.Component {
  state = {
    count: 0,
  }

  handleDecrement = () => {
    this.setState({ count: this.state.count - 1 })
  }

  handleIncrement = () => {
    this.setState({ count: this.state.count + 1 })
  }

  render() {
    <div>
      <p>Count: {this.state.count}</p>
      <div>
        <button onClick={this.handleDecrement}>-</button>
        <button onClick={this.handleIncrement}>+</button>
      </div>
    </div>
  }
}
```

Most of the time function components are easier to write and maintain, but sometimes class components are better suited.

Some of the benefits with function components:

* Less syntax.
* You don't need to think about binding `this`.
* You will use identifiers that exist in the current scope, so the linter will complain if you try to use something that doesn't exist, which it can't do if you do `this.somethingThatDoesNotExist`.
* Function components use hooks for state and lifecycle methods, which can easily be reusable.

Try to keep your components small and compose them instead of having large components that have too much state and logic.

### PropTypes

Read [Typechecking With PropTypes](https://reactjs.org/docs/typechecking-with-proptypes.html).

Our eslint rules will complain if you don't use them.

### Use CSS Modules

CSS Modules solve the same problem as BEM, but automatically.

Basically what it does is that it turns CSS-files into JavaScript modules, so that `.title { color: #f0f; }` will turn into `export default { title: '_components__MyComponent__title_abc123' }` or any other unique name, so that when we do `<h1 className={styles.title}>hello</h1>` we get `<h1 className="_components__MyComponent__title_abc123">hello<h1>`, which guarantees there will be no collisions in class names.

[What are CSS Modules and why do we need them?](https://css-tricks.com/css-modules-part-1-need/)

### Callback names

Prop names for callbacks and events should be called `onSomething`, like `onClick` or `onMouseOver` etc.

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

### File structure

Name the file after the default export, so that `MyAwesomeComponent.js` exports the component `MyAwesomeComponent`.

Try to use 1 component for each file, especially if they have a lot of logic that can be separated.
It's OK to have multiple components in one file if they are closely related.

Example file structure:

```
Counter/index.js
  export { default } from './Counter'
Counter/Counter.js
  export default Counter
Counter/Counter.css
  .counter { background: #f0f; }
```
