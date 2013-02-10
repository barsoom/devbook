![Barsoom](http://barsoom.se/barsoom.png)

# Rails conventions

This is where we document how our Rails conventions deviate (or not) from the [omakase stack](http://david.heinemeierhansson.com/2012/rails-is-omakase.html).

## `app/`

### `app/assets/`

Preferred libraries: SCSS (Sass) and CoffeeScript.

### `app/controllers/`

Take HTTP input (params, cookies etc) and pass data to views. Not much else.

Very simple CRUD can be inline as in omakase Rails.
Complexity should probably be moved into a model or use case.

Shouldn't know SQL or much of the Active Record API; models should encapsulate that.

Looking into: Two-step views (view models and templates); passing locals explicitly instead of ivars.

### `app/documents/`

PDF document generators. E.g. `ContractDocument`.

Have their own helpers in `app/documents/helpers`, e.g. `ContractDocumentHelper`.

Have their own views in `app/views/documents`, e.g. `contract_document.`.

Preferred library: our own unreleased abstraction on top of [Prawn](http://prawn.majesticseacreature.com/).

### `app/forms/`

Rails-compatible form objects not backed by a DB, when we need them.

Currently using [`Minimapper::Entity`](https://github.com/joakimk/minimapper).

Looking into: maybe `ActiveModel::Model` in Rails 4?

### `app/helpers/`

As in omakase Rails, but we prefer presenters (see below) if they're model-centric or complex enough that we want something more OO.

### `app/jobs/`

Preferred library: Resque. Looking into: Sidekiq.

Job classes should be minimal, much like controllers.

Their responsibility is to configure themselves for retry handling, queues and such; and to parse and pass on parameters; but not much else.

The actual task to perform belongs in a model or use case.

### `app/mailers/`

We like to use one mailer per action to facilitate refactoring. Action name: `build`.

### `app/models/`

Models don't have to inherit from `ActiveRecord::Base`. In fact, it's nice if they don't as they'll be easier to test.

Models never send mail (or similar) in callbacks. See use cases for that.

Looking into: separating persistence from domain logic with [Minimapper](https://github.com/joakimk/minimapper).

### `app/observers/`

Avoid them. They're implicit model callbacks.

If possible, create a *use case* instead.

### `app/presenters/`

For object-oriented helpers.

Uses:

1. Things that involve both models and helpers (e.g. a due date wrapped in a CSS class).
2. Localized strings based on non-localized state (e.g. turning "sold" state into "Sold item").

Do not use for:

1. Derived attributes that are a domain concept, e.g. age derived from a birthdate column.
   Add to an existing model or introduce a new one. General-purpose calculations like age should probably be delegated from the model to a gem or to code in `lib`.

Preferred library: none, just POROs.

We like the view context to be passed in explicitly and for presenters to be clearly named as such in view variables.

(FIXME: We're in disagreement about where derived attributes go.)

### `app/uploaders/`

For [CarrierWave](https://github.com/jnicklas/carrierwave).

### `app/use_cases/`

Also known as services. E.g. `SignUpUser.from_params(params)`.

Uses:

1. To wrap persistence (via a model) *and* sending mail.
2. More generally, to perform an action that doesn't go in the *controller*, because it's domain logic, not HTTP; nor in a *model*, because it is more of an interaction than an action on one model.

(FIXME: Henrik thinks the model/use case boundary is too undefined.)

[Read more.](/service_layer)

### `app/views/`

Preferred library: [Haml](http://haml.info/). Looking into: [Slim](http://slim-lang.com/).

Looking into: Two-step views (view models and templates); passing locals explicitly instead of ivars.

## `lib/`

General purpose libraries that we or someone else couldn't bother to make into gems.

Aggressively extract general purpose code from models. We don't want to mix interesting domain specifics with general problems like figuring out banks from account numbers.

## `spec/`

Tests.

Preferred libraries: RSpec, Capybara.

## `unit/`

Rails-free tests.
