# How we (want to) work

We are little-a agile. That means we are always trying to improve how we work, but we don't subscribe to any particular methodology (Scrum, XP, …).

We use the practices that work for us, namely:

### Test-driven development.

We try to work test-first.

We aim for 100% code coverage. Not because that guarantees a perfect test suite, but because the coverage reports help us find dead code and other things we miss.

### Collective code ownership.

While it's fine that different developers gravitate towards different things, no developer should be the only person that knows how to do any particular thing.

If we notice that the same person always does the same task because no one else knows how to, we "forbid" them from doing it the next time.

We also try to automate or at least document common tasks to help spread the knowledge.

### Own the task.

When you're assigned a task, you should understand its purpose.

If you pair with someone who owns the task, understand its purpose before you start coding.

If you disagree with the person who assigned you the task, probably one or both of you doesn't understand the purpose. Discuss it until you do. Involving a third party can help.

### Continuous delivery.

We work [trunk-based](https://thepugautomatic.com/2014/02/code-review/) and deploy several times a day.

Any difference between development and production is a liability. Small and frequent changes means smaller failures when they happen.

### Open source everything.

We use a lot of open source, contribute patches and release [libs of our own](http://github.com/barsoom).

If a problem is not too particular to the app we're working on, and does not represent an important competitive advantage for that business, we encourage extracting and sharing it.

### A blame-free environment.

The important thing when something goes wrong is to figure out why it happened and how to all learn from that; not to assign blame.

This process may involve determining the people involved, perhaps via `git blame`, but only to learn more about how it happened.

If the site has issues, it's better if people are comfortable saying "I recently changed X, could that explain it?" than if they try to hide any possible involvement out of fear or embarrassment.

Assigning personal blame, *even in the form of teasing or joking*, is likely to make that person feel bad, reluctant to do similar tasks in the future, and reluctant to be forthcoming about their involvement in any future issues.

### Leaving some slack.

We don't plan all of our time, leaving some slack. It counters stress and leads to a higher level of polish.

We spend two weeks out of every six [on cooldown](https://github.com/barsoom/devbook/tree/master/process#shape-up) fixing things we want to fix: tools, visual polish, fun features, improve workflows, refactoring, documents like this one etc.

And when we do planned tasks, we try to have enough slack to do them well the first time.

### Zero tolerance for exceptions.

We want to be notified about every exception, but ideally only once or twice; then they should be fixed. If you glance at an exception and discard it because it's a known issue, you're doing it wrong.

If it's known, make your app handle or swallow it. Exceptions should be exceptional. Whatever you're doing in your head to handle or ignore that exception, put it in code.

This is aspirational – we're always a little short of this goal, but it's what we strive for.

### Code review.

We review all our code in our review tool [Remit](https://github.com/barsoom/ex-remit).

### Pair programming by default.

We default to assigning tasks to a pair.

We pair program to spread knowledge, for problems that don't easily fit inside one head, and because collaboration is joyful.

The pair will choose when to work together and when to work separately. We recognise that pairing is sometimes less valuable, and that the flow and speed you can get working solo is hard to beat. Pair programming is a tool; not dogma.

Often it's good to work together when there's more thinking than typing; often it's good to work apart when there's more typing than thinking. We think of this as "distributed pair programming" – you can work apart but consult each other without any major context switching.

### Respectful discussions.

Let others finish.

Be brief.

Be specific.

### Meetings.

We respect the starting time of our meetings. We do not wait for latecomers unless explicitly requested.

Recurring meetings should have an explicit purpose.

One-off meetings should have an agenda, including at least the desired outcome.

### Spreading knowledge.

When we add new technology and/or build applications or parts of applications using new concepts, we actively try to spread the knowledge in the whole team by deliberately involving new people in improvements in the new parts.

We accept that during some parts of a project, knowledge silos might be created, but work actively to reduce the impact.
