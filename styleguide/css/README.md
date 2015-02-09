![Barsoom](http://barsoom.se/barsoom.png)

# CSS and Sass styleguide

Also see [our top-level styleguide](/styleguide): it explains how and when to follow our styleguides, and how to change them.

This is an aspirational styleguide. Use this style for new projects and move old projects in this direction.

This styleguide intentionally addresses both higher-level structure and lower-level details, based on the current needs of our team.

---
### Frameworks and libraries

Avoid Twitter Bootstrap for non-trivial sites. It is a UI toolkit, not (just) a framework. It can be hard to override sensibly.

---
### File structure

The root stylesheet is named `application.scss` (possibly namespaced, e.g. `admin/application.scss`).

If there's a print stylesheet, it's named `print.scss`.

`application.scss` has no inline CSS, only import statements. We use `@import "foo"` or `@import "dir/*"` in Sass rather than [Sprockets `require` statements](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives), since this works better with Sass variables etc.

For simplicity, our filenames don't start with an underscore (we use `foo.scss`,  not `_foo.scss`). So far we've never compiled Sass in a way that underscores would make a difference. We do use underscores as word separators: `cookie_notice.scss` defines `.cookie-notice`.

Note that all `@import`ed files must end in `.scss`, otherwise Sass will generate a plain-CSS `@import url(foo.css)` which may work in dev but fail in production.

`application.scss` links to this file at the top for reference and mentions any additional project-specific constraints or tools, e.g.:

``` scss
// Styleguide: https://github.com/barsoom/devbook/tree/master/styleguide/css
// We support IE8 or better.
// We use autoprefixer-rails, so there's no need to specify vendor prefixes.
```

`application.scss` conventionally imports the following things:

``` scss
// Import general-purpose (typically third-party) libraries first, so we can override them.
@import "lib/*";  // E.g. lib/normalize.scss, lib/tipsy.scss

// Config files are for $vars only.
// Depending on the project's needs, you may have a single file:
@import "config";
// Or separate files:
@import "config/*";  // E.g. config/{colors,fonts,misc}.scss

// Globally reusable SASS mixins, placeholder selectors and similar.
// Mixins etc that are only interesting within a single component should be defined there instead.
@import "mixins/*";  // E.g. clearfix.scss

// Base elements without classes: `a`, `a:hover`, `input[type=password]`, `li a`, `p > a`.
// Descendant/child/attribute selectors are allowed, but keep specificity as low as you can.
// Again, you may have a single file or separate files: base/{forms,tables,typography,misc}.scss
@import "base";

// Anything else, including major layout components.
@import "components/*";  // E.g. components/{sidebar,cart,cookie_notice}.scss
```

We try to keep our components small (a screenful or less) and reusable (no more specific than necessary).

Influences:

* [SMACSS](https://smacss.com/)
* [ITCSS](https://www.youtube.com/watch?v=1OKZOV-iLj4)

---
### Selectors and declarations

#### Use hyphens in class names.

So `foo-bar`, not `foo_bar` or `fooBar`.

#### Use BEM-like naming.

Within a component, we prefer [BEM-like naming](http://cssguidelin.es/#bem-like-naming) and we avoid nested selectors.

Use `__` (two underscores) for component parts (e.g. `.item-listing__title`).

Use `--` (two hyphens) for modifiers (e.g. `.item-listing--inactive`).

E.g.

``` slim
.item-listing.item-listing--inactive
  .item-listing__title The title
  .item-listing__body The body
```

Define these classes without nesting to [keep specificity low](#keep-specificity-low):

``` css
.item-listing { … }
.item-listing--inactive { … }
.item-listing__title { … }
```

This type of naming is less fragile than nested selectors, more clearly states intent, and makes it easier to override specificity.

With Sass 3.4 or later, you can use `&` like this:

``` scss
.item-listing {
  &--inactive { … }
  &__title { … }
}
```

Further reading:

* [Shoot to kill; CSS selector intent](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)
* [Scaling down the BEM methodology for small projects](http://www.smashingmagazine.com/2014/07/17/bem-methodology-for-small-projects/)


#### `.is-modified` is allowed.

In addition to [BEM-like modifier naming](#bem-like-naming), we also accept naming like `.is-inactive` for modifiers (a.k.a. "states") when that is more convenient, e.g. for "polymorphic" modifiers applied by JavaScript to multiple components.

In this case, *never* style the modifier class standalone (don't do `.is-inactive { … }`), *always* style it together with the modified thing (do `.item-listing.is-inactive { … }`). Generic modifier classes should have no styling on their own since we never know what they may be combined with in the future.


#### Keep specificity low.

Strive for as low CSS specificity as possible by avoiding nesting selectors (`.foo .bar { … }`), chaining selectors (`.foo.bar { … }`) or qualifying classes (`div.foo { … }`).

This makes maintenance easier by avoiding specificity wars and bugs.

[BEM-like naming](#bem-like-naming) helps achieve this.

[Read more in cssguidelin.es.](http://cssguidelin.es/#specificity)

#### Don't use ID selectors.

Feel free to use them for anchor links, but don't style by ID.

They are ["specificity anomalies"](http://cssguidelin.es/#ids-in-css), making it hard to override styles.

They also discourage modular CSS, as they're only intended to be used once per page.

#### Avoid undefining styles.

If you find yourself needing to undefine a previously defined style, stop and consider if this is a sign that the previously defined CSS should be refactored.

#### In the markup, put more generic selectors first.

We usually write markup in something like [Slim](http://slim-lang.com/).

Then we prefer `table.table-bordered.items-table` to `table.items-table.table-bordered`.

Since the element name comes first and is the most generic, it reads well to also have generic CSS classes sooner.

#### Use `0` without a unit for zero values.

Rather than `0px`, `0em` etc. Not a big deal, but it's the common convention, and less to type.

#### Use a `test-` prefix for test-only classes.

If a CSS class is only used to find elements in automated tests, prefix it. E.g. `test-destroy-link`.

This makes its purpose clear to the reader.

#### Use a `js-` prefix for JS-hook classes.

If a CSS class is only used to find elements in JavaScript, prefix it. E.g. `js-destroy-link`.

This makes its purpose clear to the reader.


---
### Sass

We use [Sass](http://sass-lang.com/). See [their docs](http://sass-lang.com/documentation/file.SASS_REFERENCE.html).

#### Use hyphens in multiple-word variable names.

So `$foo-bar: 123px;`

This is the style used in the Sass documentation.

#### Use `$` variables for colors etc.

Prefer `color: $error-color` or `color: $red` to `color: #f00`.

This helps us keep color schemes small and consistent. It also names the color.

Similarly, variables can help us keep things like margins, padding, line height, font family and font weight consistent.

#### Use `$_` for "private" variables.

Whenever possible, if you need a non-global variable, define it inside a selector so it's only visible in that scope.

Sometimes that's not possible. Then we name it with a leading underscore, e.g. `$_item-spacing`, to show intent: we see it as file-private, though it isn't technically.

Thanks to [Henric Trotzig](https://twitter.com/henrictrotzig/status/563362417439023104) for suggesting this.
