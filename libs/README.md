![Barsoom](http://barsoom.se/barsoom.png)

# Libraries

## Authentication

We're wary of full-stack libraries as we value higher flexibility above getting a generic flow running quickly.

We want to move in the direction of many small, focused libraries that do things at the edges of your app.

This is what we have so far:

* [ActiveModel::SecurePassword](http://api.rubyonrails.org/classes/ActiveModel/SecurePassword/ClassMethods.html) for storing passwords. Not happy with how it puts the responsibility for authentication inside the model.
* [UserSession](https://gist.github.com/henrik/5261139) for the session management.
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

## Misc

* [attr_extras](https://github.com/barsoom/attr_extras) to reduce boilerplate.
