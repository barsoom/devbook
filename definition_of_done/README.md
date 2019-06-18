![Barsoom](http://barsoom.se/barsoom.png)

# Definition of done

When is a feature ["done done"](http://chrislema.com/what-is-done-done/) so its ticket can be closed?

## Checklist

* Working code
* Automatic tests
  * Tests updated or added
  * All old tests pass
  * Have considered if it can be a unit test (and the test pyramid in general)
* Refactored (readable to others, maintainable)
  * Spend at least a few minutes, to the best of your ability
  * When in doubt, consider naming and [Sandi's rules](https://gist.github.com/henrik/4509394)
* Optimized enough (not slow when run with realistic amounts of data)
* Secure enough (consider the security aspects)
* Localized
* Documented if necessary (others can use it)
* Stakeholders have approved it
* In production (deployed, feature-toggled on, rolled out to everyone)
* Stakeholders have been informed that the feature is available
* Old data and old code has been removed (if applicable)
* Code making network requests, e.g. calling APIs, gracefully handles common network errors (see: [Sidekiq retries](http://disq.us/p/1wr05yx), [NetHttpTimeoutErrors](https://github.com/barsoom/net_http_timeout_errors))
* If it's an email: there's a `mail_view` preview
* If it's a document (e.g. PDF): there's a `document_previews` preview

## Changes to the public site

* Front-end code
  * Has been verified in browsers (for the general public: IE11+, latest Firefox, Chrome, Safari, Mobile Safari)
  * Has been verified to be responsive, if the project has that goal
  * Has been verified in the print preview, if printing this page makes sense
* The support team has been informed about it and tried using it
* The FAQ or equivalent has been changed (if applicable)
* Copy has correct spelling, grammar and pluralization

## What's *not* part of the definition of done?

* Having addressed all code review comments (since we review after the fact and don't want to block on review)
