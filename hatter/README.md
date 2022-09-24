# Hatter

The developers take turns being the "hatter" (or "hat").

They are the designated person(s) keeping an eye on incoming exceptions and requests from the rest of the organisation. This lets the rest of the dev team focus on planned tasks.

## 🎩 The Hatter's Guide 🎩

### Your first priority is to triage

The hatter triages incoming requests (from Slack to the "Hatten" Trello) and the dev dashboard (mainly logged exceptions), routing them where they belong.

Look at requests pretty soon after they arrive, to ensure we don't miss anything important, and so we can ask for clarifying questions while the requester has context.

Look at exceptions pretty soon after they arrive as well. If you can't fix it immediately: make a ticket, link to it, and resolve the exception (unless it keeps recurring), keeping the dashboard green. This makes it easier to spot new exceptions and to determine priority of ticketed ones.

Ensure the prio of requests is correct. A "today" may actually be a "this week" or "anytime", or the other way around. Ask the requester if the prio is unclear.

Keep the requester in the loop – let them know when the issue is fixed, or paused, or rejected.

### Only keep smaller, urgent tasks

It's not an exact science, but basically, the hatter should protect the team from smaller, more urgent tasks that would be wasteful to take through planning. Bigger tasks are often unsuitable for the hatter since they don't get to focus for long.

- If it is small and urgent enough, the hatter moves it to "To do".
- If the request isn't urgent enough, the hatter moves it to the "Till planering" column and tells the requester.
- If it's urgent but the hatter doesn't have time or it requires extended focus, they ask other developers to help out.

The hatter should be pessimistic – it's better to move something to "Till planering" and tell the requester to plan it, than to optimistically keep it in "To do" for weeks. It's kinder to the requester and avoids the hatter feeling overwhelmed. And you could still do these tasks *if* the "To do" list is ever empty.

Sometimes the hatter can find a small fix in a bigger task – e.g. change a recurring logged exception into a user-facing helpful error ("this is not currently supported").

#### Typical tasks suitable for the hatter

- Answer a technical question from support
- Change data in batch or that isn't exposed via UI, perhaps by running or writing a script (no more than a few hours of work)
- Simple data exports or Rubyclips (no more than a few hours of work)
- Fixing simple bugs (no more than a few hours of work)

#### Typical tasks not suitable for the hatter

- Build a feature
- Make a complex data export or Rubyclip (days of work)
- Fixing complex bugs (days of work)
