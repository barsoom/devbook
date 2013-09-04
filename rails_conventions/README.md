![Barsoom](http://barsoom.se/barsoom.png)

# Rails conventions

This is where we document how our Rails conventions deviate (or not) from the [omakase stack](http://david.heinemeierhansson.com/2012/rails-is-omakase.html).

## `app/`

### `app/assets/`

Preferred libraries: SCSS (Sass) and CoffeeScript.

### `app/breadcrumbs/`

For [breadcrumb builders](https://github.com/barsoom/devbook/tree/master/libs#breadcrumbs).

### `app/controllers/`

Take HTTP input (params, cookies etc) and pass data to views. Not much else.

Very simple CRUD can be inline as in omakase Rails.
Complexity should probably be moved into a model or interactor.

Shouldn't know SQL or much of the Active Record API; models should encapsulate that.

Looking into: Two-step views (view objects and templates); passing locals explicitly instead of ivars.

### `app/documents/`

PDF document generators. E.g. `ContractDocument`.

Preferred library: [ours on top of pdfkit or Prawn](https://github.com/barsoom/devbook/tree/master/libs#pdf-generation).

### `app/forms/`

Rails-compatible form objects not backed by a DB, when we need them.

Preferred library: [`Minimapper::Entity`](https://github.com/joakimk/minimapper).

Looking into: Maybe `ActiveModel::Model` in Rails 4?

### `app/helpers/`

As in omakase Rails, but we prefer *presenters* and *view helpers* (see below) if they're model-centric or complex enough that we want something more OO.

When convenient, a helper can delegate to a more OO implementation elsewhere.

### `app/jobs/`

Preferred library: Resque. Looking into: Sidekiq.

Job classes should be minimal, much like controllers.

Their responsibility is to configure themselves for retry handling, queues and such; and to parse and pass on parameters; but not much else.

The actual task to perform belongs in a *model* or *interactor*.

### `app/mailers/`

We like to use one mailer per action to facilitate refactoring. Action name: `build`. E.g. `Buyer::WonItemMailer.build(item.id)` (ids for [resque\_mailer](https://github.com/barsoom/resque_mailer_with_retries)), wrapped in `Buyer::WonItemMailer.deliver(item)`.

### `app/mappers/`

[Minimapper](https://github.com/joakimk/minimapper) mappers and records.

### `app/models/`

Models don't have to inherit from `ActiveRecord::Base`. In fact, it's nice if they don't as they'll be easier to test.

Models never send mail (or similar) in callbacks. See *interactors* for that.

Looking into: Separating persistence from domain logic with [Minimapper](https://github.com/joakimk/minimapper). With Minimapper, this is where the entity models go.

### `app/observers/`

Avoid them. They're implicit model callbacks.

If possible, create an *interactor* instead.

### `app/presenters/`

For object-oriented helpers that wrap a single model.

Use for:

1. Things that involve both models and helpers (e.g. a due date wrapped in a CSS class).
2. Localized strings based on non-localized state (e.g. turning "sold" state into "Sold item").

Do not use for:

1. Derived attributes that are a domain concept, e.g. age derived from a birthdate column.
   Add to an existing model or introduce a new one. General-purpose calculations like age should probably be delegated from the model to a gem or to code in `lib`.

Preferred library: none, just POROs.

We like the view context to be passed in explicitly and for presenters to be clearly named as such in view variables.

(FIXME: We're in disagreement about where derived attributes go.)

### `app/tasks/`

For models that are only used from the console for maintenance tasks that don't have a UI yet.

E.g. a `ContractChanger`.

Sort of like one-off scripts that are run often, but not often enough that we've made a proper UI.

### `app/uploaders/`

For [CarrierWave](https://github.com/jnicklas/carrierwave).

### `app/interactors/`

Also known as services or use cases. E.g. `SignUpUser.from_params(params)`.

Use for:

1. Representing interactions that involve persistence (via a model) *and* sending mail, like a user signup.
2. More generally, to perform an action that doesn't go in the *controller*, because it's domain logic and not HTTP; nor in a *model*, because it is more of an interaction flow than just one action involving one or two models.

E.g. placing a bid in an eBay-like auction system, with max bids (that you place) and bids (that the system places on your behalf). It should be an interactor like `PlaceMaxBid#place` and not a model method like `MaxBid#place`. Because it doesn't just involve that model and doesn't just deal with its internal concerns. It may create a record via that model, but may also create more than one `Bid` record and trigger mail notifications.

Interactors are high-level coordinators called by controllers, background jobs, scripts or other models.

They may communicate back to their caller by return value or callbacks on a passed-in object.

### `app/view_helpers/`

For object-oriented helpers.

Unlike presenters, these don't wrap a single model.

Can be things like a `SearchTitle` or `ItemBreadcrumbBuilder`.

### `app/views/`

Preferred library: [Slim](http://slim-lang.com/).

## `lib/`

General purpose libraries that we or someone else couldn't bother to make into gems.

Aggressively extract general purpose code from models. We don't want to mix interesting domain specifics with general problems like figuring out banks from account numbers.

## `spec/`

Tests.

Preferred libraries: RSpec, Capybara.

## `unit/`

Rails-free tests.
