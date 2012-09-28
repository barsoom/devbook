![Barsoom](http://barsoom.se/barsoom.png)

# Barsoom developer's handbook

* [Styleguide](#styleguide)
  * [General](#general)
  * [HTML](#html)
  * [Ruby](#ruby)
* [How we work](#how-we-work)

## Styleguide

We aim towards a consistent style so we don't waste time repeatedly arguing over unimportant things.


---
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
### Ruby

#### Prefer 1.9-style hashes.

Do `{ json: :style }` and not `{ :hash => :rockets }` when possible.


## How we work

We are little-a agile. That means we are continually looking for ways to improve how we work but don't subscribe to any particular methodology (Scrum, XP, …).

We use the practices that work for us, namely:

#### Test-driven development.

We try to work test-first.

We aim for 100% code coverage. Not because that guarantees a perfect test suite, but because the coverage reports help us find dead code and other things we miss.

#### Collective code ownership.

While it's fine that different developers gravitate towards different things, no developer should be the only person that knows how to do any particular thing.

If we notice that the same person always does the same task because no one else knows how to, we "forbid" them from doing it the next time.

We also try to automate or at least document common tasks to help spread the knowledge.

#### Continuous deployment.

We deploy several times a day.

Any difference between development and production is a liability. Small and frequent changes means smaller failures when they happen.

#### Leaving some slack.

We don't plan all of our time, leaving some slack. It counters stress and leads to a higher level of polish.

We try to spend at least an hour or two every morning fixing things we want to fix: tools, visual polish, fun features, improve workflows, refactoring etc.

And when we do planned tasks, we try to have enough slack to do them well the first time.

#### Some code review.

We don't review all code all the time; we prefer to "move fast and break things".

We do review some changes, when breaking things would be unacceptable. At those times, we use GitHub pull requests.

All commits are shown and linked in our chatroom, so there is also some spontaneous post-merge review from that.

#### Some pair programming.

We pair program to spread knowledge or for problems that don't easily fit inside one head.

But we don't always pair program, as the flow and speed of the lone developer is hard to beat.

Sometimes we do "distributed pair programming" where two or more developers work on different parts of the same task, so they can consult each other without any major context switching.
