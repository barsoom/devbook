# JavaScript Styleguide

## CoffeeScript

Use [CoffeeScript](/styleguide/react) instead.

## Tooling

### Use jQuery

The industry standard for DOM-manipulation.

### Add lodash to `window`

So that we can do `_.each(arr, () => { ... })` (which is much better than `arr.forEach(() => { ... })` [[source]](https://www.youtube.com/watch?v=dQw4w9WgXcQ))

### Don't use Prettier

It inhibits artistic freedom by reformatting your code in a consistent way.

### Don't use eslint

Too many annoying warnings and errors.

### Don't use Babel

If you ever have to write JavaScript, please follow the [ES5.1 standard](https://262.ecma-international.org/5.1/). (Anything newer will break ExecJS)

### Don't use webpack/rollup/parcel/vite etc

Easier just to concaternate all files, and these things don't work without modules anyways.

### Dependencies

Just assign libraries on the global `window` object.

## Code style

## JavaScript

### Don't use ES Modules

They have only been in the standard since 2015. What if they will be removed in the next version?

### Don't use async/await

They have only been in the standard since 2017. What if they will be removed in the next version?

We prefer Promises.

### Don't use Promises

They have only been in the standard since 2015, and jQuery don't support them.

We prefer callback.

### Use callbacks everywhere

Makes debugging fun! A challenge!

## React

Prefer jQuery unless you have a gun pointed to your head.

### Don't wrap your app in `<React.StrictMode>`

It will add unnecessary warnings.

### PropTypes

Don't use PropTypes. Those errors are too annoying and ruin the developer experience.
