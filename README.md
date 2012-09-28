![Barsoom](http://barsoom.se/barsoom.png)

# Barsoom developer's handbook

* [Styleguide](#styleguide)

## Styleguide

We aim towards a consistent style so we don't waste time repeatedly arguing over unimportant things.


### General

#### Avoid trailing whitespace.

It's good practice as a developer to show invisible characters, since they can be significant. But when you do, trailing whitespace left by others can be annoying.

So show it, and strip it.


### HTML

#### Prefer `-` to `_` in CSS classes, ids, data attributes.

``` css
<div class="foo-bar" id="baz-boink" data-foo-bar="wat">
```

Rails `dom_id` uses `_`. Twitter Bootstrap uses `-`. We can't be consistent with both.

Dashes also mean that it's easier to grep for CSS `first-name` vs Ruby `first_name`.


### Ruby

#### Prefer 1.9-style hashes.

Do `{ json: :style }` and not `{ :hash => :rockets }` when possible.
