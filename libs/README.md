🫡 **Retired 2025-04-01.** Moved to Notion. We're leaving the repo around for now, but note that it might not reflect current practice.

---

# Libraries

Checklist/reference when setting up new projects.

## Authentication

We're wary of full-stack libraries as we value higher flexibility above getting a generic flow running quickly.

We want to move in the direction of many small, focused libraries that do things at the edges of your app.

This is what we have so far:

* [ActiveModel::SecurePassword](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html) for encrypting passwords, validating confirmation etc.
* [go_to_param](https://github.com/henrik/go_to_param/) to redirect where you wanted to go after logging in.

## Breadcrumbs

We currently use [breadcrumbs_on_rails](https://github.com/weppos/breadcrumbs_on_rails), mostly because it's good enough and we're used to it.

More importantly, we keep breadcrumb building outside the controller with [something like this](https://gist.github.com/henrik/5637729).

## Form generation

[simple_form](https://github.com/plataformatec/simple_form) is decent.

## PDF generation

When we value speed of development over point-perfect control, we use [our own abstraction](https://gist.github.com/henrik/5523767) on top of [pdfkit](https://github.com/pdfkit/pdfkit).

When we value precise control, we use our [PrawnCocktail](https://github.com/barsoom/prawn_cocktail) on top of [Prawn](http://prawn.majesticseacreature.com/).

We have a custom tool (not yet public) to visually preview them in a browser during development.

## Excel generation

If we don't need anything fancy, we use our own [ESV](https://github.com/barsoom/excel-esv).

## Misc

* [attr_extras](https://github.com/barsoom/attr_extras) to reduce boilerplate.
* [PaperTrail](https://github.com/airblade/paper_trail) for data versioning.
* [will_paginate](https://github.com/mislav/will_paginate) for pagination.
* [Traco](https://github.com/barsoom/traco) for translatable columns.
* [I18nUtils](https://github.com/henrik/i18n_utils) for some i18n additions.
* [validates_email_format_of](https://github.com/barsoom/validates_email_format_of) for validation of email.
* [pry-rails](https://github.com/rweng/pry-rails) console.
