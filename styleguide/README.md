![Barsoom](http://barsoom.se/barsoom.png)

# Styleguide

We aim toward a consistent and document style so we don't waste time arguing over unimportant things with one another, or with ourselves.

We may have old code that conflicts with these rules, but we try to follow them for new code, and to update old code as we encounter it.

We try not to waste space in this guide repeating widely accepted idioms (e.g. 2 space indent in Ruby), but only things we've had trouble with or been undecided on.


## How to change this styleguide

If anyone in the team wants to add, change or remove something, they bring it up on our weekly Friday meeting.

Everyone makes their case if they have one, and we decide by consensus – but typically the minority will fold.

You can demand that someone should follow a rule in this styleguide until it has been changed this way, even if they disagree with it.


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
### JavaScript

Prefer CoffeeScript.

If you must use JavaScript, some best practices to keep in mind:

* Use `===` and `!==`, don't use `==` and `!=`, unless you have good reason


---
### CoffeeScript

Follow CS/JS conventions when they differ from Ruby conventions, unless you have very good practical reasons.

When in doubt about CS conventions, the style of [their examples](http://coffeescript.org/) is canon.

Some specific examples:

* Use lowerCamelCase for names of variables and functions
* Prefer `if foo.length` to `if foo.length > 0`


---
### Ruby

#### Prefer 1.9-style hashes.

Do `{ json: :style }` and not `{ :hash => :rockets }` when possible.

#### Lambdas should stab their arguments.

Do `->(x) { x * 2 }` and not `-> x { x * 2 }`.

Mostly a stylistic choice. It's consistent with how we define regular methods. Also, stabby lambdas want something to stab!


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

### Add commas to the end of lists

Do:

```ruby
list = [
  :foo,
  :bar,
]

hash = {
  foo: 1,
  bar: 2,
}
```

The trailing commas mean you can move lines around without accidentally introducing syntax errors. It also makes for cleaner diffs and won't unnecessarily shift the `git blame` of the previous line.

### Whitespace in arrays and hashes

Do `[ 1, 2, 3 ]` and `{ foo: 1, bar: 2 }` instead of `[1, 2, 3]` and `{foo: 1, bar: 2}` for increased readability.

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

#### UPPERCASE SQL keywords.

Write `foo.bar IS NOT NULL`, never `foo.bar is not null`.

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

#### Put the delegate `to:` on its own line

Do:

```
delegate :one, :two,
  to: :numbers
```

Even if you only delegate a single method.

Rationale: it's easier to spot delegations and determine their targets when you scan the code.

#### Don't use instance variables in views (including partials).

Pass in locals instead of using instance variables directly. This makes dependencies more obvious and makes renaming instance variables more predictable.

With a controller/view, this is as simple as:

``` ruby
def show
  render locals: { item: Item.find(params[:id]) }
end
```

Or, if you define a simple helper:

``` ruby
def show
  locals item: Item.find(params[:id])
end
```

#### Use the short style for partials when possible.

Prefer `render "foo", bar: "baz"` to `render partial: "foo", locals: { bar: "baz" }`.


---
### RSpec/testing

#### Use the new `expect()` style.

Prefer `expect(foo).to be_bar` to `foo.should be_bar`.

#### Avoid `subject`.

It's easier to read `expect(account).to be_valid` than `expect(subject).to be_valid`.

You're free to use `subject(:account) { … }`, but don't refer to it by the name `subject` elsewhere.

If you do need it for technical reasons, perhaps with shared examples, go ahead.

We haven't yet regulated using the subject implicitly like `it { should be_valid }`. Use it or not as you please.

#### Avoid `described_class`.

It's easier to read `expect(MyFooThingie.foo).to eq("foo")` than `expect(described_class.foo).to eq("foo")`.

That clarity is worth more than not having to type the name, or not having to search-and-replace if it changes.

If the class name is inconveniently long, maybe a `let` works: `let(:my_class) { Foo::Bar::Baz::MyClass }`

If you do need it for technical reasons, perhaps with shared examples, go ahead.

#### Don't use `be_foo` in the spec for `foo?`

In `describe Item, "#foo?"`, do `expect(item.foo?).to be_true` but not `expect(item).to be_foo`.

In another test that isn't for that method specifically, you are free to use it:

``` ruby
describe "#fooify" do
  it "makes the item foo" do
    item.fooify
    expect(item).to be_foo
  end
end
```

Rationale:

The method under test should be tested as-is without additional methods inbetween.

The actual output expectaction (`true` or truthy?) will be explicit.

It can be used consistently. `be_foo -> foo?` works, `have_foo -> has_foo` works but e.g. `need_foo -> needs_foo?` does not.

#### Balance "not" tests with a DRY opposite.

A test like `expect(foo).not_to include("bar")` can become irrelevant, but keep passing, when someone changes copy.

Only have tests like that if you also have a test stating the opposite, and with both sharing a constant or variable:

``` ruby
describe "something" do
  let(:failed_message) { "it failed" }

  specify { expect(bar).to include(failed_message) }
  specify { expect(foo).not_to include(failed_message) }
end
```

Further reading: ["How to make negative assertions in tests"](http://thepugautomatic.com/2013/04/negative-assertions/)
