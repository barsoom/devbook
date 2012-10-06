![Barsoom](http://barsoom.se/barsoom.png)

# Styleguide

We aim toward a consistent and document style so we don't waste time arguing over unimportant things with one another, or with ourselves.

We may have old code that conflicts with these rules, but we try to follow them for new code, and to update old code as we encounter it.


### General

#### Avoid trailing whitespace.

It's good practice as a developer to show invisible characters, since they can be significant. But when you do, trailing whitespace left by others can be annoying.

So show it, and strip it.

Avoid significant trailing whitespace. Use `<br>` over Markdown's double-space, use quoted strings for the `"--  "` e-mail signature convention and so on.

---
### HTML

#### Prefer `-` to `_` in CSS classes, ids, data attributes.

``` css
<div class="foo-bar" id="baz-boink" data-foo-bar="wat">
```

Rails `dom_id` uses `_`. Twitter Bootstrap uses `-`. We can't be consistent with both.

Dashes also mean that it's easier to grep for CSS `first-name` vs Ruby `first_name`.


---
### CSS

#### Use `0` without a unit for zero values.

Rather than `0px`, `0em` etc. Not a big deal, but it's the common convention, and less to type.


---
### Ruby

#### Prefer 1.9-style hashes.

Do `{ json: :style }` and not `{ :hash => :rockets }` when possible.


#### No empty lines when indentation level changes.

But always put empty lines around multiline blocks when the indent level doesn't change.
Avoid more than one empty line.

```ruby
class Foo
  def bar
  end

  def baz
  end
end
```

Don't do any of this:

```ruby
class Foo

  def bar
  end


  def baz
  end
  def waz
  end

end
```

---
### Active Record

#### Avoid `default_scope`.

It tends to cause confusing behavior.

#### Avoid SQL outside models.

Ideally, each model encapsulates its underlying table. Changes to the table shouldn't need to break the model's API. Specifically:

* Prefer scopes or custom methods to calling Active Record methods like `where` from outside the model.
* Use [`merge`](http://apidock.com/rails/ActiveRecord/SpawnMethods/merge) to avoid using raw SQL from another model's scopes.

There's a trade-off between keeping the model API small and keeping its internals encapsulated. In some cases, as with statistics, it may make more sense overall to use SQL methods from outside the class.

---
### Ruby on Rails

#### Don't use `delegate` with `prefix: true`.

If you do `delegate :bar, to: :foo, prefix: true`, it defines a `foo_bar` method that is hard to search for. Define it explicitly instead.

#### Don't use instance variables in partials.

Pass in locals from the view instead of using instance variables directly. This makes dependencies more obvious and makes renaming instance variables more predictable.
