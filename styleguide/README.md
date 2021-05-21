![Barsoom](http://barsoom.se/barsoom.png)

# Styleguide

We aim toward a consistent style so we don't waste time arguing over unimportant things with one another, or with ourselves.

We try not to waste space in this guide repeating widely accepted idioms (e.g. 2 space indent in Ruby), but only things we've had trouble with or been undecided on.


## How and when to follow this styleguide

We may have old code that conflicts with these rules, but we try to follow them for new code, and to update old code as we encounter it (preferably in a separate commit).

We prefer to commit style changes before changes in functionality so that reviewers only have to comment on things we miss, not things we've fixed in a later commit.

If a styleguide violation is pointed out in review, follow it and don't debate it there – make a pull request with your proposed styleguide change.


## How to change this styleguide

If anyone in the team wants to add, change or remove something, they make a pull request against this repo and make their case. Feel free to make a rough initial PR to start the discussion; it can be fleshed out and polished before we merge.

We discuss in the comments and decide by consensus.

Add checkboxes to the pull request with all our names like this:

- [ ] Joe
- [ ] Jane

If everyone checks their box, we have consensus.

If you veto or need more discussion, say so in a comment.

If you haven't weighed in after two weeks, it counts as a non vote. If no one else is opposed, the pull request may be merged without your vote, though you should be notified in a comment.

If you missed the vote and want to veto it after the fact, we will revert it. But if months have passed we encourage you to instead make a new pull request.

Whenever possible, try to make it automatically enforced by something like [Rubocop](https://github.com/bbatsov/rubocop).

---
### General

#### Follow idioms.

As a general rule, we should follow the idioms of a language, even if they're different from another language we're more used to.

The idioms can often be derived from documentation (e.g. CoffeeScript, Slim) or some dominant library (e.g. jQuery for JS).

If we have good reason to deviate from the idioms, we should [put that down in the styleguide](#how-to-change-this-styleguide).

#### Avoid trailing whitespace.

It's good practice as a developer to show invisible characters, since they can be significant. But when you do, trailing whitespace left by others can be annoying.

So show it, and strip it.

Avoid significant trailing whitespace. Use `<br>` over Markdown's double-space, use quoted strings for the `"--  "` e-mail signature convention and so on.

#### Use "NOTE:" in crucial comments only

Our developers' editors may be set up to visually highlight "NOTE" in comments like `# NOTE: If you update this list, you must also update the list in foo_bar.rb.` This makes these comments stand out.

We want "NOTE" to be reserved for especially important comments, so we don't see it all the time and start ignoring it.

The rule of thumb is: If the comment points out something you must be aware of when you edit the associated code, use "NOTE". If the comment only provides background, context or pointers that may be helpful but can safely be ignored, don't use "NOTE".

#### Explicitly show both branches of a conditional used as a return value.

If a method will sometimes return nil, we want that to be explicit, not implicit.

So prefer this:

``` ruby
def my_method
  if something
    1
  else
    nil
  end
end

foo.map { |x|
  if x == something
    1
  else
    nil
  end
}
```

And avoid this:

``` ruby
def my_method
  if something
    1
  end
end

foo.map { |x|
  if x == something
    1
  end
}
```

---
### Shell scripts

#### Spell out command-line flags

Prefer e.g. `grep --invert-match foo` to `grep -v foo` because it's easier to understand and to look up.

If a flag is still unclear, consider documenting it:

``` ruby
# --insecure      Allow connections to SSL sites without certs (H)
`curl --insecure https://example.com`
```

---
### HTML

#### Use hyphens in multiple-word variable names (CSS classes, ids, data attributes).

``` css
<div class="foo-bar" id="baz-boink" data-foo-bar="wat">
```

Rails `dom_id` uses `_`. Twitter Bootstrap uses `-`. We can't be consistent with both.

Dashes also mean that it's easier to grep for CSS `first-name` vs Ruby `first_name`.

#### Document when IDs are for anchor links.

``` slim
/ This ID is an anchor.
#snake
  p Hello!
```

So it's not cleaned up by someone thinking it's used for styling.


---
### CSS and Sass

Please see [the CSS and Sass styleguide](/styleguide/css).


---
### React

Please see [the React styleguide](/styleguide/react).


---
### JavaScript

Please see [the JavaScript styleguide](/styleguide/javascript).


---
### CoffeeScript

Avoid CoffeeScript. But if you must:

Do [follow idioms](#follow-idioms). The style of [their documentation examples](http://coffeescript.org/) is canon.

#### Use `lowerCamelCase` for names of variables and functions.

It's idiomatic.

#### Prefer `if foo.length` to `if foo.length > 0` when checking for non-emptiness.

It's idiomatic.

#### Use a `_` prefix or non-property functions at end of class for "private methods".

If the `greeting` method below is meant to be internal, either use methods with a `_` prefix:

``` coffeescript
class Greeter
  constructor: (@name) ->

  greet: ->
    "#{@_greeting()}, #{@name}!"

  _greeting: ->
    "Hello"
```

or non-property functions like:

``` coffeescript
class Greeter
  constructor: (@name) ->

  greet: ->
    "#{greeting()}, #{@name}!"

  # private

  greeting = ->
    "Hello"
```


`_` methods functions suggest private intent but they *can* be called from outside, and `@_` looks a bit messy.

Non-property methods look a little cleaner and are "actually" private (cannot be called from outside), but they don't inherit and `this` isn't bound to the instance. The `# private` comment makes it more visually clear where the public API ends.

Use your own judgment to choose between the options.


---
### Ruby

#### Use a consistent class layout.

Loosely based on [bbatsov's Ruby Style Guide](https://github.com/bbatsov/ruby-style-guide#consistent-classes).

```ruby
class Person
  # extend and include go first
  extend SomeModule
  include AnotherModule

  # inner classes
  CustomErrorKlass = Class.new(StandardError)

  # constants are next
  SOME_CONSTANT = 20

  # afterwards we have attribute macros
  attr_reader :name

  # followed by other macros (if any)
  validates :name

  # public class methods are next in line
  def self.some_method
  end

  # followed by public instance methods
  def some_method
  end

  # protected and private methods are grouped near the end
  protected

  def some_protected_method
  end

  private
  
  # private class methods are grouped after the private declaration
  private_class_method \
  def self.some_private_class_method
  end

  def some_private_method
  end
end
```

#### Prefer 1.9-style hashes.

Do `{ json: :style }` and not `{ :hash => :rockets }` when possible.

#### Lambdas should stab their arguments.

Do `->(x) { x * 2 }` and not `-> x { x * 2 }`.

Mostly a stylistic choice. It's consistent with how we define regular methods. Also, stabby lambdas want something to stab!

#### Prefer `select` to `find_all`.

A stylistic choice.

#### Prefer `alias_method` to `alias`.

It's more consistent with normal Ruby syntax (`alias_method :a, :b` instead of `alias :a :b`).

It's also got a mnemonic for the argument order in its name: `alias_method :the_alias, :the_method`

#### Use square brackets for `%w[]` and friends.

Do `%w[]`, not `%w()`. It results in an array and the square brackets make that more obvious at a glance.

This rule applies to `%` syntax that results in an array, e.g. `%w`, `%W`, `%i` and `%I`.

This rule does *not* apply to `%r`, `%x` or any other such syntax that doesn't result in an array.


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


### Use "Weirich style" for blocks.

Use curly braces (`{/}`) when we use the block for its return value (and for one-liners).

Use `do/end` when we use the block for its side effects.

```
# Used for value.
names = people.map { |person|
  person.name
}.sort

# Used for side effect.
people.each do |person|
  puts person.name
end

# One-liner.
people.each { |person| puts person.name }
```

How do you know if we use the block for its return value? Picture adding `nil` as the last line inside the block. If that breaks your code, you're relying on the return value.

If we chain another method call onto the block (`foos.map { … }.sort`) or assign the result (`bars = foos.map { … }`) we most likely use the block's return value.

Rationale: It adds more information than the "always `do/end` for multiple lines" convention. It also looks better with chained calls.

We still use braces for one-liners, because we think `people.each do |person| puts person.name end` looks bad, and you also want to keep one-liners short.


### Don't assign in a method argument if you use that variable later.

Don't do:

``` ruby
expect(Foo).to receive(:bar).with(:baz).and_return(waz = double)
do_something_with(waz)
```

Prefer:

``` ruby
waz = double
expect(Foo).to receive(:bar).with(:baz).and_return(waz)
do_something_with(waz)
```

Otherwise it's hard to see where the variable is defined.

It *is* acceptable to define *unused* local variables as a refactoring step towards replacing positional arguments with keyword arguments:

``` ruby
method_with_too_many_arguments(name = "Foo", company = "Bar", age = 42, do_the_thing = false)
```

### Prefer `unless x` to `if !x` for simple expressions.

Do:

``` ruby
unless auction.catalog_number?
  puts "No number!"
  # …
end
```

Don't do:

``` ruby
if !auction.catalog_number?
  puts "No number!"
  # …
end
```

Rationale: it's the Ruby idiom.

However, never use `unless` together with an `elsif` or `else`, since that's hard to read.

Avoid using `unless` with complex conditions (e.g. `unless x.blank? || x.old?`) or negation (`unless !x.old?`), since those are usually hard to understand.


### Don't define bang methods without good reason.

When in doubt between `#foo` and `#foo!`, go with `#foo`.

If the method is used in a context where Active Record semantics would make sense, it's fine to use a bang to follow those semantics – e.g. `Item.create_with_some_custom_behavior!(attributes)` if the method raises on validation errors.

Otherwise, only use the bang when there is a pair of methods, with and without bang, where one is more destructive (e.g. raises, modifies in place). Cf. `String#gsub!`, `ActiveRecord::Base#save!`.

Further reading: ["Bang methods; or, Danger, Will Rubyist!" by David Black](http://dablog.rubypal.com/2007/8/15/bang-methods-or-danger-will-rubyist)

### Double-quote strings unless the string contains double quotes.

Prefer `"this"` to `'this'`.

Single-quoted strings [aren't faster](http://stackoverflow.com/questions/1836467/is-there-a-performance-gain-in-using-single-quotes-vs-double-quotes-in-ruby). Double quotes means you don't need to change them if you add interpolation or escape sequences.

But don't escape quotes inside a string if you don't need to. Change the quote style instead: `'like "this"'` or `%{'like' "this"}`.

### Use strings for complex i18n keys.

Do `t("this.here")` and not `t(:"this.here")`.

Where a simple unquoted symbol will do, they're fine: `t(:this)`

### Use `_html` and `_markdown` i18n key suffixes.

If a translation string contains HTML, use a `_html` suffix, e.g. `t("foo.bar_html")`

If a translation string contains Markdown, use a `_markdown` suffix, e.g. `t("foo.bar_markdown")`

Rails will automatically consider `_html` i18n values to be HTML safe, and this also communicates to the translator what they're allowed to do.

The `_markdown` suffix gets no automatic special treatment, but it also communicates to the translator what they can do.

### Use `%r{}` for regular expressions with slashes.

`%r{https?://}` reads better than `/https?:\/\//`.

### Prefer `class X; class Y` for nested classes/modules.

Prefer

``` ruby
class X
  class Y
  end
end
```

to

``` ruby
class X::Y
end
```

but use the latter if you must.

The first version avoids [constant lookup issues](http://cirw.in/blog/constant-lookup.html). Namely: if you type `Z` inside `X::Y`, you may find `X::Y::Z` or `::Z` but not `X::Z`.

Also, it may mean not having to fake out the wrapping module in unit tests.

But the latter version may sometimes be necessary to avoid superclass mismatch issues in some unit tests.


### Put a marker in placeholder translations.

*The below applies when we manually manage translations. It does not apply when we use a translation tool like [WebTranslateIt](http://webtranslateit.com) that requires us to leave untranslated strings empty in the target language.*

If you must store unfinished translations, add an "[untranslated]" so it's obvious, and easy to search for.

So do:

``` yml
de:
  foo: "[untranslated] some English copy for now"
```

Not:

``` yml
de:
  foo: "some English copy for now"
```

We chose "[untranslated]" because it's easy to search for and shouldn't be super confusing to end users if they see it.


### Add commas to the end of multiline lists and hashes

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

Don't do this within one-liner lists or hashes, though, as it doesn't have any of the above benefits. So, do:

```ruby
{
  list: [
    { one_liner: [ :hash, :and, :array ] },
  ],
}
```

### Whitespace in arrays and hashes

Do `[ 1, 2, 3 ]` and `{ foo: 1, bar: 2 }` instead of `[1, 2, 3]` and `{foo: 1, bar: 2}` for increased readability.


### Put a blank line below guard statements.

Do

``` ruby
def hello
  return nil unless greetable?

  "Hello!"
end
```

and not

``` ruby
def hello
  return nil unless greetable?
  "Hello!"
end
```

When a method has a happy path and guard statements, we like to have them visually distinct.


### Explain why you lock down a Gemfile version or fork.

If you lock a gem to a specific version, or specify a non-standard GitHub repository or branch, you should explain why in a comment.

This helps the person who wants to upgrade that dependency later. Did the standard version have a regression? Does the fork have some fix we need? Is the new version especially hard to upgrade to?

A comment is not necessary when you lock down the major version to avoid it being changed when updating another gem (e.g. `gem "redis", "~> 2.0"`), since this is so common. But if you've tried upgrading the major version and it was especially difficult or had specific gotchas, please do specify these, of course.

Example:

``` ruby
# 1.2.4 is not compatible with "apricots".
gem "bananas", "1.2.3"

# (no need for a comment)
gem "redis", "~> 2.0"
```


---
### Active Record

#### Prefer non-dynamic dirty tracking methods.

Do e.g.

``` ruby
attribute_before_last_save("title")
saved_change_to_attribute?("title")
```

rather than

``` ruby
title_before_last_save
saved_change_to_title?
```

Because it arguably reads a little clearer, being more clearly distinct from an attribute. It's incidentally also slightly more performant and easier to find in documentation.

#### Avoid `default_scope`.

It tends to cause confusing behavior.

#### Always use lambdas with `scope`.

Do `scope :cool, -> { where(cool: true) }` and not `scope :cool, where(cool: true)`.

The old style is deprecated in Rails 4 and removed in 4.1. The new style helps avoid issues where you need a lambda but forget.

There are some tricky bugs waiting to happen if you don't use lambdas everywhere, like:

``` ruby
scope :later, -> { where("happens_at > ?", Time.now) }  # now = runtime
scope :later_alligator, -> { later.alligator }          # now = runtime
scope :later_alligator, later.alligator                 # now = server launch time
```

#### UPPERCASE SQL keywords.

Write `foo.bar IS NOT NULL`, never `foo.bar is not null`.

#### Explicitly state SQL sort direction.

Write `order("name ASC")`, never `order("name")`.

#### Avoid SQL outside models.

Ideally, each model encapsulates its underlying table. Changes to the table shouldn't need to break the model's API. Specifically:

* Prefer scopes or custom methods to calling Active Record methods like `where` from outside the model.
* Use [`merge`](http://apidock.com/rails/ActiveRecord/SpawnMethods/merge) to avoid using raw SQL from another model's scopes.

There's a trade-off between keeping the model API small and keeping its internals encapsulated. In some cases, as with statistics, it may make more sense overall to use SQL methods from outside the class.

#### Explicitly declare `null:` for every column in migrations.

If a column shouldn't be empty, make it "NOT NULL" (`null: false` in migrations) in the DB in the interests of data integrity.

Even when the column may be empty, explicitly declare `null: true` in migrations to force yourself to make an explicit decision, and to show code reviewers that you did so.


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

#### Either write time zone safe code or document that it's unsafe.

These examples assume that:
  * the database stores UTC times
  * the server is in another zone, say EST
  * Rails is configured to use another zone, say CET

If you e.g. do `DATE(fooed_at)` in your SQL, that will be the date of the UTC timestamp, and not CET.

Or if you do `Time.mktime(1999, 12, 31, 12, 0)` that will be noon EST but not noon CET.

We should write "time zone safe" code when we can:
  * use `where("BETWEEN ? AND ?", fooed_at.beginning_of_day, fooed_at.end_of_day)` instead of `DATE(fooed_at)`
  * use `Time.zone.mktime` instead of `Time.mktime`
  * other similar cases (update this section if you encounter them)

Sometimes it doesn't really matter and isn't worth the effort. In those cases, say so in a comment so others know:

`# This might not be time zone safe but that's acceptable.`

The above applies equally to any scripts we keep around: if they stay in the repo to be run again, or modified, or copy-pasted from, they should be either safe or explicitly unsafe.

For code we don't keep around, anything goes, of course.

#### Use `require_dependency` to require app code.

If non-test code needs to require code that is in Rails' autoload path (e.g. code in `app` or `lib`; perhaps for Rails-less unit tests), use `require_dependency "foo"`, not `require "foo"`.

`require_dependency` plays nice with Rails development auto-reloading of classes.

`require` does not, so if you use it, changes to the required class may not be picked up in dev unless you reload the app server.

[Read more.](http://guides.rubyonrails.org/constant_autoloading_and_reloading.html#autoloading-and-require)

---
### RSpec/testing

#### Use the new `expect()` style.

Prefer `expect(foo).to be_bar` to `foo.should be_bar`.

#### Avoid `subject`.

It's easier to read `expect(account).to be_valid` than `expect(subject).to be_valid`.

You're free to use `subject(:account) { … }`, but don't refer to it by the name `subject` elsewhere.

If you do need it for technical reasons, perhaps with shared examples, go ahead.
[RSpec documentation](https://relishapp.com/rspec/rspec-core/v/3-9/docs/subject/explicit-subject) says:

> we recommend that you reserve `subject` for support of custom
> matchers and/or extension libraries that hide its use from examples.

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


#### Prefer `to have_received` over `to receive`.

Assuming a `Greeter.say_hello` that calls `say("hello")`, do:

``` ruby
allow(Greeter).to receive(:say)

Greeter.say_hello

expect(Greeter).to have_received(:say).with("hello")
```

Rather than:

``` ruby
expect(Greeter).to receive(:say).with("hello")

Foo.say_hello
```

The test reads clearer if we first exercise and *then* verify.

Note that you will need to replace the method or entire object with a test double as in the example above, for RSpec to be able to verify against it.


#### Use `private` for help methods.

Do:

``` ruby
describe "#something" do
  it "works" do
    expect(my_helper(foo)).to eq 123
  end

  private

  def my_helper(value)
    value + 1
  end
end
```

The `private` line creates a visually clear separation.


### Say why a test is pending.

Do:

``` ruby
it "foos" do
  pending "Waiting for the bar to baz"
  expect(a).to eq b
end
```

Don't do:

``` ruby
it "foos" do
  pending
  expect(a).to eq b
end
```

This communicates *why* it's pending so that others (or a later you) can tell, e.g. if it's abandoned.

### Don't use string interpolations with `Kernel#system` or `Kernel#\``.

This can be very dangerous because of shell escapes.

Use `system` with multiple arguments or [Shellwords](https://ruby-doc.org/stdlib-2.7.0/libdoc/shellwords/rdoc/Shellwords.html) if you need to construct more complex command lines.

Here are some examples:

``` ruby
system("rm -rf #{path}") # 🚫💀 If path contains e.g. spaces or asterisks, this can remove the wrong thing!

`rm -rf #{path}` # 🚫💀

cmd = "rm -rf #{path}"
system(cmd) # 🚫💀

system("rm", "-rf", path) # ✅

system("rm -rf #{Shellwords.escape path}") # ✅

`rm -rf #{Shellwords.escape path}` # ✅

cmd = Shellwords.shelljoin(["rm", "-rf", path])
system(cmd) # ✅

text = `figlet -f banner #{ARGV.join(" ")} | lolcat` # 🚫💀

figlet = Shellwords.shelljoin(["figlet", "-f", "banner", *ARGV])
system("#{figlet} | lolcat") # ✅
```
