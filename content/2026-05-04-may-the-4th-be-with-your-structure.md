# Design over Frameworks: Why modernizing isn't just swapping tools

It's May the 4th, and my reflection today isn't about lightsabers. It's about architecture.

In Star Wars, the Empire made the same fatal mistake twice: they focused on scale and firepower while ignoring a critical design flaw. The Death Star wasn't defeated by a better weapon. It was defeated because someone forgot to think about what happens when a single point fails.

Software teams do this all the time.

## The "Framework-First" trap

When a project starts to feel old, the instinct is to modernize. New framework, new stack, new tooling. The team celebrates. The architecture stays exactly the same.

I've seen this up close. One of our enterprise clients has a build that reaches 70GB inside a single container. Others sit at 18 to 20GB, 12 to 14GB, 5 to 8GB. All of them running inside the same environment.

That's not a microservice. That's a Death Star carrying the entire galaxy's worth of accumulated decisions nobody wanted to revisit.

Swapping the framework doesn't fix that. It just makes the mistake more expensive to run in production.

## Architecture is about decisions that are hard to change

Frameworks are implementation details. You can replace them. Architecture is what you can't easily undo: how data flows, where responsibilities live, what depends on what.

If a build process requires 70GB of dependencies and artifacts to serve a single client, the problem isn't the framework. The problem is that nobody asked why the build is that heavy in the first place. Nobody enforced a boundary. Nobody said "this should be isolated."

Good design is born from understanding constraints and data flows, not from picking the most popular library on GitHub this quarter.

## The questions that actually matter

When starting something new, the technology choice is almost never the hardest part. The hard part is being willing to ask uncomfortable questions before the first line of code is written.

Why is this build so heavy? How can we isolate responsibilities so the feedback loop is seconds, not hours? Does the current design allow the system to evolve, or are we already building tomorrow's legacy?

Modernizing without rethinking the design is just scheduling the next expensive refactor.

May the architecture be with you. 🛸
