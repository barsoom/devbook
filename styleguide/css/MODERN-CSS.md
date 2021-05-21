![Barsoom](http://barsoom.se/barsoom.png)

# Modern CSS styleguide

Also see [our top-level styleguide](/styleguide): it explains how and when to follow our styleguides, and how to change them.

This guide is specifically aimed at new projects using modern web tools.

---

### Frameworks and libraries

Avoid frameworks and libraries as much as you can.

Use SVG icons instead of font icons. They both have their pros and cons, but SVG is a little bit more flexible (allows for colors and animations). [Icon Fonts vs SVG – Clash of the Icons](https://www.lambdatest.com/blog/its-2019-lets-end-the-debate-on-icon-fonts-vs-svg-icons/)


### Use [prettier](https://prettier.io/)

This will force our CSS to be consistently formatted.


### Use [stylelint](https://stylelint.io/)

TODO: We still need to write a stylelint config, and make sure it's compatible with prettier so that it doesn't complain over rules that would be fixed by prettier.


### Use [postcss](https://postcss.org/) with [postcss-preset-env](https://preset-env.cssdb.org/)

[PostCSS Preset Env](https://preset-env.cssdb.org/) lets you convert modern CSS into something most browsers can understand, determining the polyfills you need based on your targeted browsers or runtime environments, using [cssdb](https://cssdb.org/).


### Use CSS Modules

CSS Modules solve the same problem as BEM, but allows you to name your classes as you want without following any specific rules.

BEM:

``` scss
/* assets/css/items.scss */

.item-listing {
  &--inactive { … }
  &__title { … }
}
```
``` jsx
function ItemListing({ title, inactive }) {
  const className = classnames('item-listing', {
    'item-listing--inactive': inactive,
  })

  return (
    <div className={className}>
      <h3 className="item-listing__title">{title}</h3>
    </div>
  )
}
```

CSS Modules:

``` css
/* ItemListing.module.css */

.itemListing { … }
.inactive { … }
.title { … }
```
``` jsx
import styles from './ItemListing.module.css'

function ItemListing({ title, inactive }) {
  const className = classnames(styles.itemListing, {
    [styles.inactive]: inactive,
  })

  return (
    <div className={className}>
      <h3 className={styles.title}>{title}</h3>
    </div>
  )
}
```

---

### Selectors and declarations

#### Avoid global styles.

There will probably be 1 file in the project that configures fonts and body background.
Generally only that file should be allowed to configure global styles.


### Always use `box-sizing: border-box`

This makes sizing easier.  Set to `content-box` on specific elements if you need it.

When you start a new project, always add these lines to the main CSS file:

``` css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```


#### Use CSS Variables.

The same file that configures fonts and body should configure variables as well.
Font names, colors, margins, paddings etc. All that goes there.
Could be put in separate files too if that makes it easier.

For general palette colors, use the same naming of shades as [Material UI](https://material-ui.com/customization/color/), i.e. `gray500`, `blue50` etc, so in CSS it would be `var(--gray500)` or `var(--blue50)`.


#### Use camelCase in class names.

Do `.fooBar`, not `.foo-bar` or `.foo_bar`.

This is consistent with JavaScript naming, so that we can do `styles.fooBar` instead of `styles['foo-bar']`.


#### Use `0` without a unit for zero values.

Rather than `0px`, `0em` etc. Not a big deal, but it's the common convention, and less to type.


#### Prefer `px` as the unit for `font-size`.

Our preference is `px` (as opposed to `pt`, `em`, `rem` or `%`) since it's easy to reason about.

The use of `px` was previously discouraged since older browsers didn't scale the text on zoom, but modern browsers get this right.

We *do* like `em` for margins or paddings that should be relative to the current font size.
