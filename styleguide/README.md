![Barsoom](http://barsoom.se/barsoom.png)

# Styleguide

We aim toward a consistent and document style so we don't waste time arguing over unimportant things with one another, or with ourselves.

We may have old code that conflicts with these rules, but we try to follow them for new code, and to update old code as we encounter it.

We try not to waste space in this guide repeating widely accepted idioms (e.g. 2 space indent in Ruby), but only things we've had trouble with or been undecided on.


---
### General

#### Avoid trailing whitespace.

It's good practice as a developer to show invisible characters, since they can be significant. But when you do, trailing whitespace left by others can be annoying.

So show it, and strip it.

Avoid significant trailing whitespace. Use `<br>` over Markdown's double-space, use quoted strings for the `"--  "` e-mail signature convention and so on.

---
### HTML

#### Use hyphens in multiple-word variable names (CSS classes, ids, data attributes).

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
### SASS

#### Use hyphens in multiple-word variable names.

So `$foo-bar: 123px;`

This is the style used in the Sass documentation.


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

### Don't skip indent levels for alignment.

When a statement is split over several lines, we think it's generally more readable if each new line changes by at most one level of indentation.

Do any of these:

```ruby
my_method(foo, one: 1, two: 2)

my_method(
  foo,
  one: 1,
  two: 2
)

my_method(
  foo,
  {
    one: 1,
    two: 2
  }
)

my_method(foo,
  one: 1,
  two: 2)
```

But not any of these:

```ruby
my_method(foo, one: 1,
          two: 2)

my_method(foo, one: 1,
               two: 2)
```

If you do skip levels of indentation to align arguments, and you later rename something, the indentation is also liable to become confusing.

It should be noted that Vim's Ruby indentation rules disagree with us here; they do align on arguments.

### Don't assign in a method argument.

Don't do:

```ruby
Foo.should_receive(:bar).with(:baz).and_return(waz = mock)
```

Prefer:

```ruby
waz = mock
Foo.should_receive(:bar).with(:baz).and_return(waz)
```

Otherwise it's hard to see where the variable is defined.

### Don't define bang methods without good reason.

When in doubt between `#foo` and `#foo!`, go with `#foo`.

Only use the bang when there is a pair of methods, with and without bang, where one is more destructive (e.g. raises, modifies in place). Cf. `String#gsub!`, `ActiveRecord::Base#save!`.

Further reading: ["Bang methods; or, Danger, Will Rubyist!" by David Black](http://dablog.rubypal.com/2007/8/15/bang-methods-or-danger-will-rubyist)

### Double-quote strings unless the string contains double quotes.

Prefer `"this"` to `'this'`.

Single-quoted strings [aren't faster](http://stackoverflow.com/questions/1836467/is-there-a-performance-gain-in-using-single-quotes-vs-double-quotes-in-ruby). Double quotes means you don't need to change them if you add interpolation.

But don't escape quotes inside a string if you don't need to. Change the quote style instead: `'like "this"'` or `%{'like' "this"}`.

In contexts (e.g. `Gemfile`) where single quotes are conventional, respect that convention.

### Use strings for complex i18n keys.

Do `t("this.here")` and not `t(:"this.here")`.

Where a simple unquoted symbol will do, they're fine: `t(:this)`

### Use the short `class X::Y` form for nested classes/modules.

We prefer

`class X::Y`

to

``` ruby
class X
  class Y
  end
end
```

when possible. It's shorter and avoids superclass mismatch issues.

Beware of [constant lookup issues](http://cirw.in/blog/constant-lookup.html), though. Namely: if you type `Z` inside `X::Y`, you may find `X::Y::Z` or `::Z` but not `X::Z`.

---
### Active Record

#### Avoid `default_scope`.

It tends to cause confusing behavior.

#### Always use lambdas with `scope`.

Do `scope :cool, -> { where(cool: true) }` and not `scope :cool, where(cool: true)`.

The old style will be deprecated in Rails 4. The new style helps avoid issues where you need a lambda but forget.

There are some tricky bugs waiting to happen if you don't use lambdas everywhere, like:

``` ruby
scope :later, -> { where("happens_at > ?", Time.now) }  # now = runtime
scope :later_alligator, -> { later.alligator }          # now = runtime
scope :later_alligator, later.alligator                 # now = server launch time
```

#### Avoid SQL outside models.

Ideally, each model encapsulates its underlying table. Changes to the table shouldn't need to break the model's API. Specifically:

* Prefer scopes or custom methods to calling Active Record methods like `where` from outside the model.
* Use [`merge`](http://apidock.com/rails/ActiveRecord/SpawnMethods/merge) to avoid using raw SQL from another model's scopes.

There's a trade-off between keeping the model API small and keeping its internals encapsulated. In some cases, as with statistics, it may make more sense overall to use SQL methods from outside the class.

#### Explicitly declare `null:` for every column in migrations.

If a column shouldn't be empty, make it "NOT NULL" in the DB in the interests of data integrity.

To make sure we remember, we always declare `null:` for every column, even if it's `null: true`.


---
### Ruby on Rails

#### Don't use `delegate` with `prefix: true`.

If you do `delegate :bar, to: :foo, prefix: true`, it defines a `foo_bar` method that is hard to search for. Define it explicitly instead.

#### Don't use instance variables in partials.

Pass in locals from the view instead of using instance variables directly. This makes dependencies more obvious and makes renaming instance variables more predictable.

#### Use the short style for partials when possible.

Prefer `render "foo", bar: "baz"` to `render partial: "foo", locals: { bar: "baz" }`.

#### Avoid using instance variables beyond a controller + views.

Instance variables are (probably) fine to share data between a controller and its views. But avoid them to communicate between two helpers, controller and layout and so on.

Instead, define setters/getters/boolean methods for that communication: one helper sets with `foo!` and another checks with `foo?`

This way, renaming the instance variable or reimplementing that logic is less work and less risk.


---
### RSpec/testing

#### Use the new `expect()` style.

Prefer `expect(foo).to be_bar` to `foo.should be_bar`.

#### Balance "should not" tests with a DRY opposite.

A test like `foo.should_not include("bar")` can become irrelevant, but keep passing, when someone changes copy.

Only have tests like that if you also have a test stating the opposite, and with both sharing a constant or variable:

``` ruby
describe "something" do
  let(:failed_message) { "it failed" }

  specify { bar.should include(failed_message) }
  specify { foo.should_not include(failed_message) }
end
```

Further reading: ["How to make negative assertions in tests"](http://thepugautomatic.com/2013/04/negative-assertions/)
