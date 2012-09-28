![Barsoom](http://barsoom.se/barsoom.png)

# Barsoom developer's handbook


## Styleguide

We aim towards a consistent style so we don't waste time repeatedly arguing over unimportant things.

### HTML

#### Prefer `-` to `_` in CSS classes, ids, data attributes.

``` css
<div class="foo-bar" id="baz-boink" data-foo-bar="wat">
```

Rails `dom_id` uses `_`. Twitter Bootstrap uses `-`. We can't be consistent with both.
