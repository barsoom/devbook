![Barsoom](http://barsoom.se/barsoom.png)

# CSS and Sass styleguide

Also see [our top-level styleguide](/styleguide): it explains how and when to follow our styleguides, and how to change them.

This is an aspirational styleguide: Use this style for new projects. Move old projects in this direction, even if they have "broken windows", so we don't add to the burden of harder-to-maintain legacy styles.

This styleguide intentionally addresses both higher-level structure and lower-level details, based on the current needs of our team.

---
### Frameworks and libraries

#### Avoid Twitter Bootstrap for non-trivial sites.

It is a UI toolkit, not (just) a framework. It can be hard to override sensibly.

#### Use Autoprefixer.

Whenever possible, use [Autoprefixer](https://github.com/postcss/autoprefixer), typically via [autoprefixer-rails](https://github.com/ai/autoprefixer-rails).

It saves us from manually adding prefixes, from making mixins just for that, and from occasionally forgetting to do so.

#### Use normalize.css.

Use [normalize.css](https://github.com/necolas/normalize.css/) to make browsers render more consistently.


---
### File structure

The root stylesheet is named `application.scss` (possibly namespaced, e.g. `admin/application.scss`).

If there's a print stylesheet, it's named `print.scss`.

`application.scss` has no inline CSS, only import statements.


#### Importing

We use `@import "foo"` or `@import "dir/*"` ([globbing](https://github.com/rails/sass-rails#glob-imports) provided by sass-rails) rather than [Sprockets `require` statements](http://guides.rubyonrails.org/asset_pipeline.html#manifest-files-and-directives), since this works better with Sass variables etc.

For simplicity, our filenames don't start with an underscore (we use `foo.scss`,  not `_foo.scss`). So far we've never compiled Sass in a way that underscores would make a difference. We do use underscores as word separators: `cookie_notice.scss` defines `.cookie-notice`.

Note that all `@import`ed files must end in `.scss`, otherwise Sass will generate a plain-CSS `@import url(foo.css)` which may work in dev but fail in production.


#### `application.scss`

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

// Tiny, often single-declaration utility classes.
@import "utilities";

// Anything else, including major layout components.
@import "components/**/*";  // E.g. components/{sidebar,cart,cookie_notice}.scss

// Per-page CSS, when unavoidable.
@import "pages/**/*";  // E.g. pages/{about_page,item_page}.scss
```

We try to keep our components small (a screenful or less) and reusable (no more specific than necessary).

Influences:

* [SMACSS](https://smacss.com/)
* [ITCSS](https://www.youtube.com/watch?v=1OKZOV-iLj4)


---
### Principles


#### Keep specificity low.

Strive for as low CSS specificity as possible by avoiding nesting selectors (`.foo .bar { … }`), chaining selectors (`.foo.bar { … }`) or qualifying classes (`div.foo { … }`).

This makes maintenance easier by avoiding specificity wars and bugs.

[BEM-like naming](#use-bem-like-naming) helps achieve this.

Further reading:

 * ["Specificity" in Harry Roberts' CSS Guidelines](http://cssguidelin.es/#specificity)


#### Consider selector intent.

If you mean "within message boxes, style the icon" then write `.message-box .icon { }`, not `.message-box i { }`.

Even if both selectors work, the former expresses your actual intent and the latter does not. So it communicates better. As a side-effect of that, it's less fragile: the style rule will only affect future icons, not all `<i>` elements. And it won't break if we change the icon markup.

Further reading:

* [Shoot to kill; CSS selector intent](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)


#### Avoid undefining styles.

If you find yourself needing to undefine a previously defined style, stop and consider if this is a sign that the previously defined CSS should be refactored.


---
### Selectors and declarations


#### Use hyphens in class names.

Do `.foo-bar`, not `.foo_bar` or `.fooBar`.


#### Use `0` without a unit for zero values.

Rather than `0px`, `0em` etc. Not a big deal, but it's the common convention, and less to type.


#### Prefer `px` as the unit for `font-size`.

Our preference is `px` (as opposed to `pt`, `em`, `rem` or `%`) since it's easy to reason about.

The use of `px` was previously discouraged since older browsers didn't scale the text on zoom, but modern browsers get this right.

We *do* like `em` for margins or paddings that should be relative to the current font size.


#### Use a `test-` prefix for test-only classes.

If a CSS class is *only* used to find elements in automated tests, prefix it. E.g. `test-destroy-link`.

These classes should not be used for styling or to find elements in JavaScript. And styling or JS-hook classes should not be used to find elements in tests.

This means that we can confidently change our tests without breaking styling or JavaScript, and vice versa.

We don't use BEM-like naming for these: that's for styling.

Further reading:

* [QA namespaces](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/#qa-namespaces-qa-)


#### Use a `js-` prefix for classes used by JS.

When you find elements in JavaScript, use dedicated JS-only selectors, e.g. `.js-destroy-link`.

These classes should not be used for styling or to find elements in tests. And styling or test classes should not be used for JS.

This means that we can confidently change our JS without breaking styling or tests, and vice versa.

We don't use BEM-like naming for these: that's for styling.

Further reading:

* [JavaScript namespaces](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/#javascript-namespaces-js-)


#### In the markup, put more generic selectors first.

We usually write markup in something like [Slim](http://slim-lang.com/).

Then we prefer `table.table-bordered.items-table` to `table.items-table.table-bordered`.

Since the element name comes first and is the most generic, it reads well to also have generic CSS classes sooner.


#### Don't use IDs for styling or JS.

Feel free to use them for anchor links (and then write a comment like "This ID is used for anchor links" so it's clear).

Use them for third-party JS or CSS if we absolutely must (e.g. some third-party JS library has that API, or we style some embedded third-party widget).

But never *style* by ID.

They are ["specificity anomalies"](http://cssguidelin.es/#ids-in-css), making it hard to override styles.

They also discourage modular CSS, as they're only intended to be used once per page.

And for JS, we prefer to [use CSS classes with a `js-` prefix](#use-a-js--prefix-for-classes-used-by-js).


#### Use BEM-like naming.

We aim for [BEM-like naming](http://cssguidelin.es/#bem-like-naming).

This type of naming has less risk of collisions than nested selectors, more clearly shows which classes are part of the same whole, and makes it easier to override specificity.

Note that this section is a very brief summary for reference. You *must* [read up on BEM elsewhere](http://cssguidelin.es/#bem-like-naming) and grasp the idea behind it before you contribute to our CSS.

Use `__` (two underscores) for component parts (e.g. `.item-listing__title`).

Use `--` (two hyphens) for modifiers (e.g. `.item-listing--inactive`).

Example:

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

With Sass 3.4 or later, you can use `&` like this:

``` scss
.item-listing {
  &--inactive { … }
  &__title { … }
}
```

Use `&` or not as you please, on a case-by-case basis.

The block (the first part of the name) may be implied/implicit: we can use `.input--small` for `input` variants even if we don't want to add an `.input` class to all of them. Or we may do `.home-page__thingie` when a style needs to be page-specific and we don't need to actually add a `.home-page` class.

You must never use a component part without either an explicit or an implied block: don't use `.item-listing__title` for something that isn't an item listing just because it happens to look good. Either add a new component like `.other-thing__title` (if they incidentally look the same) or extract a more general concept like `.list-title` that can be used in both places.

BEM is an ideal. Pragmatically, we will sometimes "cheat" and write a selector like `.item-info__list dt` rather than `.item-info__list__title`, if we're confident this doesn't introduce significant risk of collisions or specificity wars. And if those things *do* become a problem later, we can stop cheating then – we have the ideal to fall back on.

Further reading:

* ["BEM-like Naming" in Harry Roberts' CSS Guidelines](http://cssguidelin.es/#bem-like-naming)
* [MindBEMding – getting your head ’round BEM syntax](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)
* [More Transparent UI Code with Namespaces](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/)
* [Side Effects in CSS](http://philipwalton.com/articles/side-effects-in-css/)
* [Shoot to kill; CSS selector intent](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)
* [Utility classes](http://csswizardry.com/2015/03/more-transparent-ui-code-with-namespaces/#utility-namespaces-u-)


#### Keep BEM component nesting shallow.

Only use `__` when something is a subcomponent, not when it can be seen as a separate component that happens to be nested.

If you have a "hamburger" menu in the site header and you know you are highly unlikely to have other types of hamburgers elsewhere, do `.hamburger` – no need for `.site-header__hamburger`.

Aim for no more than one level of subcomponents (`.a__b` is OK; avoid `.a__b__c`).

Further reading:

* ["Starting Context" in Harry Roberts' CSS Guidelines](http://cssguidelin.es/#starting-context)
* See the <http://csswizardry.com/> source


#### `.is-modified` is allowed for polymorphic modifiers only.

In addition to [BEM-like modifier naming](#use-bem-like-naming), we also accept naming like `.is-inactive` for modifiers *when they are polymorphic*, i.e. when JavaScript applies the same modifier to multiple types of components. Then BEM naming would be very inconvenient.

*Never* style these generic modifier classes standalone (don't do `.is-inactive { … }`); *always* style them together with the modified thing (do `.item-listing.is-inactive { … }`). Generic modifier classes should have no styling on their own since we never know what they may be combined with in the future.


#### Avoid pre-defined breakpoints.

Our philosophy on breakpoints (with @media queries) is to just resize the window (or view the page on a device) and set the breakpoint where it makes sense on a case-by-case basis.

We don't want to use predefined breakpoints like "phone", "tablet", "screen" because they're unnecessarily limiting, and less future-proof.

We only use predefined breakpoints when multiple things truly have the same reason to change. If the menu becomes a hamburger at a certain breakpoint, and that change should coincide with other layout changes, then a predefined breakpoint is fine. But name it something like "small-enough-for-hamburger", not "phablet".


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


#### Put nested selectors last.

Do

```
.my-thing {
  margin: 0;

  &__subthing {
    padding: 0;
  }
}
```

Don't do

```
.my-thing {
  &__subthing {
    padding: 0;
  }

  margin: 0;
}
```

No practical reason, just taste.


#### Put group selectors on separate lines with trailing commas.

When the list of selectors may grow, do

```
.foo,
.bar,
{
  color: #f00;
}
```

The separate lines makes it easier to read.

The trailing commas make changes slightly easier.

If the list of selectors is unlikely to grow (e.g. `a:hover, a:active {}`) you don't need multiple lines.


#### Only `@extend` silent classes.

Do

```
.foo,
%foo,
{
  color: #f00;
}

.bar {
  @extend %foo;
}
```

Don't do

```
.foo {
  color: #f00;
}

.bar {
  @extend .foo;
}
```

This avoids unintended side-effects if `.foo` appears in multiple contexts.

Further reading:

* [Extending silent classes in Sass](http://csswizardry.com/2014/01/extending-silent-classes-in-sass/)
