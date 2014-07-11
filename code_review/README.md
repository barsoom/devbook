![Barsoom](http://barsoom.se/barsoom.png)

# Code review guidelines

We review all code commit-by-commit after the fact using [Remit](https://github.com/henrik/remit/).

Committers and reviewers should read these guidelines to do it better.

### Inspiration:

* https://github.com/thoughtbot/guides/tree/master/code-review


## As a reviewer


### Don't mention small style changes.

For small, mostly unimportant stylistic matters like quote style and whitespace, don't point them out unless that person has said they want that feedback.

It takes more time for the committer to open that file and fix the quote style from `'` to `"` than the value of a consistent codebase.

Fix it yourself or let it go.

We still have a "right" way of doing things: do complain if someone intentionally changes from the "right" to the "wrong" style, and please clean it up if you see it. It's just not efficient to enforce this in commit-by-commit review.


### Don't mention harmless typos/grammar in internal docs or code comments.

Runnable code and customer-facing copy should not have typos or grammar errors.

But if someone makes a harmless typo or grammar error in internal docs or code comments, it probably takes more time to fix than it's worth.

Fix it yourself or let it go.

Do point out if documentation is hard to understand or misleading, though, whether or not it has typos.



## As a committer

WiP… suggest additions by pull request.
