![Barsoom](http://barsoom.se/barsoom.png)

# Code review guidelines

We review all code commit-by-commit after the fact using [Remit](https://github.com/henrik/remit/).

Committers and reviewers should read these guidelines to do it better.

### Inspiration:

* https://github.com/thoughtbot/guides/tree/master/code-review


## As a reviewer


### Don't mention small style changes.

For small, mostly unimportant stylistic matters like quote style and whitespace, don't point them out unless that person has said they want that feedback.

It takes more time for the committer to open that project, find the file file and fix the quote style from `'` to `"` than it's worth.

Fix it yourself or let it go.

We still have a "right" way of doing things: do complain if someone intentionally changes from the "right" to the "wrong" style, and please clean it up if you see it. It's just not efficient to enforce this in commit-by-commit review.


### Don't mention harmless typos/grammar in internal docs or code comments.

Runnable code and customer-facing copy should not have typos or grammar errors.

But if someone makes a harmless typo or grammar error in internal docs or code comments, it probably takes more time for them to open that project, find the file and fix it than it's worth.

Fix it yourself or let it go.

Do point out if documentation is hard to understand or misleading, though, whether or not it has typos.



## As a committer


### Obey the styleguide.

If someone points out a [styleguide](/barsoom/devbook/tree/master/styleguide#how-and-when-to-follow-this-styleguide) violation, fix it. If you want to debate it, take that debate to the styleguide – after fixing it.


### Don't obey comments you don't understand or agree with.

Every review comment should be seen as a suggestion. Don't follow them blindly because of things like the reviewer's seniority.

Ask questions (in person, if that helps) until you understand and agree that the change would be an improvement.

If you don't understand their point, don't change it until you do.

If you understand and still disagree, don't change it.

Understanding their point means understanding the *underlying* reasons this would be good or bad ("not DRYing this up could easily lead to bugs here"). An appeal to authority ("DHH says this is the right way") or to rules or smells ("this is not DRY", "this violates SRP") is not enough.


### If you're asked for clarification, clarify in the *source*.

If a reviewer asks what a piece of documentation or a snippet of code means, don't just reply in the review tool; try to clarify the documentation or code itself.

A reply in the review tool is hard to dig up later, if you even know to try.

Clarified code, on the other hand, helps everyone who deals with that code in the future.

One of the big wins of code review is to catch hard-to-understand things when *someone* still understands it (having just written it).
