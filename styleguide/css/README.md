![Barsoom](http://barsoom.se/barsoom.png)

# CSS and Sass styleguide

Also see [the general styleguide](/styleguide).

This is an aspirational styleguide. Use this style for new projects and move old projects in this direction.

This styleguide intentionally addresses both higher-level structure and lower-level details, based on the current needs of our team.

---
### File structure

We use a file structure influenced by [SMACSS](https://smacss.com/).

The root stylesheet is named `application.scss` (possibly namespaced, e.g. `admin/application.scss`).

If there's a print stylesheet, it's named `print.scss`.

`application.scss` has no inline CSS, only import statements. We use `@import "foo"` or `@import "dir/*"` in Sass rather than [Sprockets `require` statements](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives), since this works better with Sass variables etc.

For simplicity, our filenames don't start with an underscore (we use `foo.scss`,  not `_foo.scss`). So far we've never compiled Sass in a way that underscores would make a difference.

`application.scss` conventionally imports the following things:

``` scss
// Import general-purpose (typically third-party) libraries first, so we can override them.
@import "lib/*";  // E.g. normalize.scss, tipsy.scss

// Config files are for $vars only.
// Smaller projects may have a single config file:
@import "config";
// Bigger projects may have separate config files:
@import "config/*";  // E.g. colors.scss, fonts.scss, misc.scss

// Reusable SASS mixins, placeholder selectors and similar.
@import "mixins/*";  // E.g. clearfix.scss

// Base elements without classes like <a> and <p>.
@import "base";

// Anything else, including major layout components.
@import "components/*";  // E.g. sidebar.scss, cart.scss, cookie_notice.scss
```

We try to keep our components small (a screenful or less) and reusable (no more specific than necessary).

---
### Selectors and declarations

#### Don't use ID selectors.

Feel free to use them for anchor links, but don't style by ID.

They are ["specificity anomalies"](http://cssguidelin.es/#ids-in-css), making it hard to override styles.

They also discourage modular CSS, as they're only intended to be used once per page.

#### Use hyphens in class names.

So `foo-bar`, not `foo_bar` or `fooBar`.

#### Use BEM-like naming.

Within a component, we prefer [BEM-like naming](https://github.com/barsoom/devbook/pull/24) and we avoid nested selectors.

Use `__` (two underscores) for component parts (e.g. `.item-listing__title`).

Use `--` (two hyphens) for modifiers (e.g. `.item-listing--inactive`).

E.g.

``` slim
.item-listing.item-listing--inactive
  .item-listing__title The title
  .item-listing__body The body
```

This type of naming is less fragile than nested selectors, more clearly states intent, and makes it easier to override specificity. [Read more about the benefits.](http://www.smashingmagazine.com/2014/07/17/bem-methodology-for-small-projects/)

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
