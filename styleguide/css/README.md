![Barsoom](http://barsoom.se/barsoom.png)

# CSS and Sass styleguide

Also see [the general styleguide](/styleguide).

---
### CSS

#### Don't use ID selectors.

Feel free to use them for anchor links, but don't style by ID.

They are ["specificity anomalies"](http://cssguidelin.es/#ids-in-css), making it hard to override styles.

They also discourage modular CSS, as they're only intended to be used once per page.

#### Use a `test-` prefix for test-only classes.

If a CSS class is only used to find elements in automated tests, prefix it. E.g. `test-destroy-link`.

This makes its purpose clear to the reader.

#### Use a `js-` prefix for JS-hook classes.

If a CSS class is only used to find elements in JavaScript, prefix it. E.g. `js-destroy-link`.

This makes its purpose clear to the reader.

#### Use `0` without a unit for zero values.

Rather than `0px`, `0em` etc. Not a big deal, but it's the common convention, and less to type.


---
### Sass

#### Use `$` variables for colors etc.

Prefer `color: $error-color` or `color: $red` to `color: #f00`.

This helps us keep color schemes small and consistent. It also names the color.

Similarly, variables can help us keep things like margins, padding, line height, font family and font weight consistent.

#### Use hyphens in multiple-word variable names.

So `$foo-bar: 123px;`

This is the style used in the Sass documentation.
