![Barsoom](http://barsoom.se/barsoom.png)

# CSS and Sass styleguide

Also see [the general styleguide](/styleguide).

---
### CSS

#### Don't use ID selectors.

Feel free to use them for anchor links, but don't style by ID.

They are ["specificity anomalies"](http://cssguidelin.es/#ids-in-css), making it hard to override styles.

They also discourage modular CSS, as they're only intended to be used once per page.

#### Use `0` without a unit for zero values.

Rather than `0px`, `0em` etc. Not a big deal, but it's the common convention, and less to type.


---
### Sass

#### Use hyphens in multiple-word variable names.

So `$foo-bar: 123px;`

This is the style used in the Sass documentation.
