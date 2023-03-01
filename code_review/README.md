# Code review guidelines

We review all code commit-by-commit after the fact using [Remit](https://github.com/barsoom/ex-remit/), for [reasons](https://thepugautomatic.com/2014/02/code-review/).

Some of this reflects our conventions prior to splitting into teams in 2022. Teams may experiment with their own conventions and with how/whether to do review at all. See this less as a list of rules to follow, and more as a descriptive guide to understanding how we've approached it so far.

## Inspiration

* https://github.com/thoughtbot/guides/tree/master/code-review

## Both as reviewer and committer

### Change medium if you talk past one another.

If you think you might be talking past one another, stop writing low-context, low-bandwidth comments. Talk IRL or over voice or video instead.

### Don't have style debates.

Pointing out a difference in style is fine: it can help us learn from each other.

But don't let it go beyond pointing out the difference and saying "cool, I'll try that" or "thanks, but I prefer it this way".

If you feel strongly enough to want to advocate "your" style beyond this, propose a change in our styleguide and have the discussion on that pull request, or in a meeting.


## As a reviewer

### Use "Minor: …" to distinguish less important feedback.

A review comment like "Minor: We could do this instead" can be read as "If you don't agree or don't think it's worth the effort, never mind, and no need to tell me".

Sometimes we miss this and a comment without this prefix is obviously intended as minor.

### Don't mention small style changes and typos/grammar.

It's usually not worth the overhead. Fix it yourself, or let it go.

For code style, try to automatically enforce it with a linter.

If you see the same error a lot (e.g. the same person is always confused about a style convention, or the spelling of a word) that *could* be worth pointing out.

Do point out if documentation is hard to understand or misleading, though, whether or not it has typos.

### Feel free to make uncontroversial fixes yourself.

CI broken due to a syntax error? Feel free to just fix it.

### Be careful about making controversial changes yourself.

If someone else is actively working on some code, refactoring it without involving them could step on toes and lead to double work.

You don't have their context, and perhaps your change makes future changes harder.

You can suggest the refactoring, or offer to pair on it.

Sometimes we do make and push the change – it can be quicker than explaining it or trying to find a time to pair up. But then we explicitly tell the person e.g. "I gave this a shot. WDYT? Revert if you disagree."

## As a committer

### It's OK to silently act on feedback.

If you act on the feedback, there's no need to respond with an "OK" or a link to the commit fixing it, though sometimes we do this to be extra clear.

The reviewer can see that the comment is resolved and can see that the change has been made.

### Respond to non-minor feedback you don't act on.

If you don't act on the feedback (and it's not minor), it's usually courteous to briefly comment.

Otherwise the reviewer will see that the comment is resolved but no change has been made.

These comments could be e.g. "I disagree for this reason" or "Not a priority for me, but feel free if it is for you".

### Obey the styleguide.

If someone points out a [styleguide](/barsoom/devbook/tree/master/styleguide#how-and-when-to-follow-this-styleguide) violation, fix it.

If you want to debate it, take that debate to the styleguide – after fixing it.

### Don't blindly implement comments you don't understand or agree with.

Every review comment should be seen as a suggestion. Don't follow them blindly because of things like the reviewer's seniority.

We've had situations where a change is made that neither the reviewer nor the reviewee wanted, because of misunderstandings combined with making the change you think someone else wants.

Ask questions (ideally in person or a meeting to increase bandwidth) until you understand and agree that the change would be an improvement.

If you don't understand their point, don't change it until you do.

If you understand and still disagree, don't change it.

Understanding their point means understanding the *underlying* reasons this would be good or bad ("not DRYing this up could easily lead to bugs here"). An appeal to authority ("DHH says this is the right way") or to rules or smells ("this is not DRY", "this violates SRP") is not enough.

An appeal to the styleguide [*is* enough](#obey-the-styleguide) – but if it doesn't give clear reasons why, we should clarify.

### Don't spend much time on things you've already considered.

If you already thought of but rejected something that a reviewer suggests, feel free to skip it with a quick explanation.

We want to keep review low-cost: you don't need to spend time justifying every decision.

### If you're asked for clarification, clarify in the *source*.

If a reviewer asks what a piece of documentation or code means, don't just reply in the review tool; try to clarify the documentation or code itself.

A reply in the review tool is hard to dig up later, if you even know to look for it.

Clarified code, on the other hand, helps everyone who deals with that code in the future.

One of the big wins of code review is to catch hard-to-understand things when *someone* still understands it (having just written it).

### If you commit translation updates, take some responsibility for them.

When you pull translations from the translation tool and commit them and push, they will go live on the site – most likely before a reviewer has had a chance to look at them.

You should scan through them and look for obvious issues – broken interpolation syntax, misunderstood context etc. We can spot some of these even in languages we don't know.
