![Barsoom](http://barsoom.se/barsoom.png)

# Definition of done

When is a feature ["done done"](https://web.archive.org/web/20170725135300/https://chrislema.com/what-is-done-done/) so its ticket can be closed?

## Checklist

* Working code
* Automatic tests
  * Tests updated or added
    * Covered by an integration test
    * Details tested at as low a level as reasonable
* Clean (idiomatic, readable to others, maintainable)
* Automated (consider whether manual tasks are the right trade-off – ask the team for expensive tasks)
* Optimized (not slow when run with realistic amounts of data and traffic)
* Secure (consider the security aspects)
* Localized
* Documented (updating or adding relevant docs, especially for new tasks/routines)
* Approved by the product owner
* Used in production (deployed, feature-toggled on, rolled out to everyone)
  * Feature toggle removed if not needed, or scheduled for removal if we're not keeping it forever
* Stakeholders have been informed that the feature is available
* Old data and old code has been removed
* Likely errors handled so the hatter isn't notified if devs don't need to act on it (e.g. showing an error only to the user, retries – especially for network requests)
* If it's an email: there's a `mail_view` preview
* If it's a document (e.g. PDF): there's a `document_previews` preview

## Changes to the public site

* Front-end code
  * Works in all modern browsers, desktop and mobile
  * Is responsive
  * Has been verified in the print preview, if printing this page makes sense
* The support team has been informed about it and tried using it
* The FAQ or equivalent has been changed (if applicable)
* Copy has correct spelling, grammar and pluralization
* Works in our Turbolinks-based app

## What's *not* part of the definition of done?

* Having addressed all code review comments (since we review after the fact and don't want to block on review)
