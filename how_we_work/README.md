![Barsoom](http://barsoom.se/barsoom.png)

# How we (want to) work

We are little-a agile. That means we are always trying to improve how we work, but we don't subscribe to any particular methodology (Scrum, XP, …).

We use the practices that work for us, namely:

#### Test-driven development.

We try to work test-first.

We aim for 100% code coverage. Not because that guarantees a perfect test suite, but because the coverage reports help us find dead code and other things we miss.

#### Collective code ownership.

While it's fine that different developers gravitate towards different things, no developer should be the only person that knows how to do any particular thing.

If we notice that the same person always does the same task because no one else knows how to, we "forbid" them from doing it the next time.

We also try to automate or at least document common tasks to help spread the knowledge.

#### Own the task.

When you're assigned a task, you should understand its purpose.

If you pair with someone who owns the task, understand its purpose before you start coding.

If you disagree with the person who assigned you the task, probably one or both of you doesn't understand the purpose. Discuss it until you do. Involving a third party can help.

#### Continuous deployment.

We deploy several times a day.

Any difference between development and production is a liability. Small and frequent changes means smaller failures when they happen.

#### Open source everything.

We use a lot of open source, contribute patches and release [libs of our own](http://github.com/barsoom).

If a problem is not too particular to the app we're working on, and does not represent an important competitive advantage for that business, we encourage extracting and sharing it.

#### Post mortems in a blame-free environment.

The important thing when something goes wrong is to figure out why it happened and how to all learn from that, not to assign blame.

Looking into the error *will* certainly involve `git blame`, and possibly some mild teasing, but making mistakes is considered part of the process.

#### Leaving some slack.

We don't plan all of our time, leaving some slack. It counters stress and leads to a higher level of polish.

We try to spend at least two days every two weeks fixing things we want to fix: tools, visual polish, fun features, improve workflows, refactoring, documents like this one etc.

And when we do planned tasks, we try to have enough slack to do them well the first time.

#### Zero tolerance for bugs and exceptions.

We want to be notified about every exception, but ideally only once or twice; then they should be fixed. If you glance at an exception and discard it because it's a known issue, you're doing it wrong.

If it's known, make your app handle or swallow it. Exceptions should be exceptional. Whatever you're doing in your head to handle or ignore that exception, put it in code.

Bugs should be fixed when they crop up, before starting work on new features. The only way to win against entropy is to fix the broken stuff before adding new <strike>broken stuff</strike> features.

We're not perfect, so we're always a little short of this goal, but not very.

#### Code review.

We review all our code in our review tool [Remit](https://github.com/henrik/remit).

#### Some pair programming.

We pair program to spread knowledge or for problems that don't easily fit inside one head.

We try to be receptive to someone wanting to pair.

But we don't always pair program, as the flow and speed of the lone developer is hard to beat.

Sometimes we do "distributed pair programming" where two or more developers work on different parts of the same task, so they can consult each other without any major context switching.

#### Respectful discussions.

Let others finish.

Be brief.

Be specific.

#### Meetings

We respect the planned starting time of meetings. We will start the meeting on time, even if there are someone missing.

We prefer to have meetings right away as long as everyone who should be in the meeting is present. 
