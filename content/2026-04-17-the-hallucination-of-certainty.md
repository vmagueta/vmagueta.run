---
title: "The Hallucination of Certainty"
date: 2026-04-17
tags: ["AI", "Infrastructure", "DNS", "Incident Response", "Platform Engineering"]
description: "Two DNS incidents, months apart, same pattern. What happens when someone without technical background meets an AI willing to answer anything."
---

There is a specific type of infrastructure accident that happens when someone without technical background meets an AI willing to answer anything. This post is about two of those accidents. Months apart, same context, same people, different products broken.

## Two Fridays, Same Pattern

The first time, a product went down on a Friday afternoon. Someone decided to "fix" something in the domain panel without knowing that the domain was registered with one provider while the DNS records lived in another. They started creating records at the registrar. At some point, they swapped the nameservers to point to the registrar itself. The real DNS, living elsewhere, was cut off. Product down.

The second time: a site migration from one platform to another, done mid-week. They swapped nameservers without exporting the zone first. The new provider took over the domain, saw no existing zone, and applied its default template.

Along with the old zone went five Google Workspace MX records, five SPF includes, six DKIM keys, DMARC, and a CNAME pointing to an internal API. The site went live. For three days, no one noticed the email had stopped. When they did, it was Friday morning.

Both were resolved after they called me. The fix was mechanical. What stayed with me was the pattern.

## The AI in the Room

When the email went down, before calling me, they consulted an AI. The AI responded with confidence. It diagnosed that the MX records were pointing to a legacy platform server that no longer existed, and that the solution was to reconfigure the email from scratch. They forwarded the entire diagnosis to the technical team as if it were useful context.

The answer was plausible to anyone who has seen similar migrations. It just didn't correspond to what had actually happened.

The AI didn't know where the email was hosted before the migration, nor that the entire zone had been overwritten, nor that the email service appearing in the current DNS was just the new provider's default template. It performed pattern matching with the most common failure mode it had seen and delivered a confident answer.

Zero hesitation. No "before proceeding, confirm X." No recognition that the diagnosis depended on information no one had verified. The answer came out with the same cadence an AI uses for a cake recipe. On the other end, no one had the context to differentiate a well-founded answer from a plausible one.

Following the AI's advice would have buried the problem deeper. The diagnosis was wrong at every layer that mattered.

## It's Not About the AI

I use AI every day. For code, reviews, diagnostics, for writing posts like this. I'm not here to steer anyone away from it.

What happens in these incidents isn't a failure of the tool. It's the tool working as designed. AI responds with the confidence the prompt demands. If you ask "how do I fix X," it answers "do Y." If you don't know enough to ask "what if it's not X?", it won't insert that doubt for you.

The mechanism that protects against this error is the human operator. If you have enough context to read the response and say "this doesn't match what I'm seeing," the AI becomes a productivity multiplier. If you don't, it becomes an amplifier of misplaced confidence. The confidence leaves the model and goes straight to the finger that clicks. No filter in between.

## What Worries Me

There's a dangerous idea floating around that AI democratizes technical skill. That anyone can now do what previously required a specialist. In some domains, this is partially true. In infrastructure, it isn't.

Infrastructure has a property that disarms this premise: the cost of the error only appears later. You can execute a completely wrong sequence of commands and everything seems to work for minutes, hours, sometimes days. Feedback is delayed, indirect, and usually comes from someone else complaining that something stopped. By then, it's usually too late to revert cleanly.

The validation an AI can do in real time ("does this command look valid?") is a small fraction of the validation the work actually requires ("is this coherent with the history of this system, with the other systems that depend on it, with the implicit agreements someone made when configuring this?"). The second layer needs context. Context needs technical background. Background doesn't come from a prompt.

## What I Tell People Now

If you need to touch production DNS and don't know what MX, SPF, TTL, or the difference between a registrar and a DNS provider is: call someone who does. Open a ticket. Tag someone on Slack. Wait two hours for a review. The cost of waiting is always lower than the cost of spending a weekend rebuilding a broken zone.

This isn't gatekeeping. It's respect for how failure happens. AI changed how we work. It didn't change the fact that wrong DNS on a Friday afternoon eats your weekend.

If you don't know, don't use AI to pretend you do. Call someone who knows. It costs less.
