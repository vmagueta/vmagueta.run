+++
title = "Know What You Ship"
date = 2026-04-01
tags = ["Platform Engineering", "Python", "Rust", "DevEx", "Security"]
description = "How Anthropic's accidental leak of 512k lines of Claude Code internals made me think about the tools I've been building — and the instinct behind them."
+++

Yesterday, Anthropic accidentally published 512k lines of Claude Code internals. Source maps. Internal logic. Things that weren't meant to be public.

Not a breach. Not a hack. An `.npmignore` file that didn't do its job.

I'm not here to pile on. Mistakes happen — even at billion-dollar companies with world-class engineers. What caught my attention wasn't the incident itself. It was *how* it happened.

---

Nobody sat down and said "let's ship our source maps." It just... slipped through.

Modern build pipelines are layers on top of layers. You write TypeScript. It compiles. Source maps get generated. A bundler runs. An `.npmignore` gets evaluated. At some point in that chain, something ships that shouldn't — and nobody notices until it's already out there.

That's the uncomfortable part. Not malice. Not negligence. Just complexity that accumulates quietly until it bites you.

I've been thinking about this a lot lately, because it's the exact failure mode I've been trying to design against in my own projects.

---

**envgate** started from a simple frustration.

I was tired of apps that start fine, accept traffic, and then crash 10 minutes later because some environment variable wasn't set. The error shows up in production. The damage is already done.

The fix is obvious once you say it out loud: **validate at startup, before the app does anything else.**

```python
from envgate import validate

config = validate({
    "DATABASE_URL": {"type": "str"},
    "PORT": {"type": "int", "default": 8000},
    "DEBUG": {"type": "bool", "default": False},
})
```

If `DATABASE_URL` is missing, you get this immediately — before any request is handled, before any connection is attempted:

```
MissingEnvVarError: Environment variable 'DATABASE_URL' is not set.
```

The logic is intentionally boring. Under the hood, it's a dictionary-based validator that resolves types and defaults before your application logic even breathes. Zero dependencies. No metaclasses, no decorators, no magic — just pure Python sanity checks that run once at import time.

Catching a missing variable via an exception deep inside a running service is expensive: you've already accepted the request, you've already done work, and now you're rolling back. Failing at startup costs nothing. The process just doesn't start.

The Anthropic incident wasn't a missing env var — but the failure pattern rhymes. **Invisible state producing visible damage, later than it should.**

→ [Source on GitHub](https://github.com/vmagueta/envgate) · [PyPI](https://pypi.org/project/envgate/)

---

**forgekey** came from the same instinct, from a different angle.

When I need to generate credentials in a CI/CD pipeline, I don't want a Python script pulling in a dozen transitive dependencies. I don't want a Node tool either. I want one thing, one job, no surprises.

So I wrote a Rust CLI. Single binary. No runtime. Nothing to misconfigure.

```sh
forgekey --length 32 -n 5
```

The core generation is straightforward — and that's the point:

```rust
pub fn generate_key(length: usize, charset: &[u8]) -> String {
    let mut rng = rand::thread_rng();
    (0..length)
        .map(|_| charset[rng.gen_range(0..charset.len())] as char)
        .collect()
}
```

`rand::thread_rng()` gives cryptographically secure randomness. The rest is just sampling a charset and collecting into a `String`. No heap allocations you didn't ask for. No hidden I/O. You can read the entire source and know exactly what runs.

Compare that to a Node tool with 400 transitive packages — half of which you've never audited — doing the same job. That's the environment where an `.npmignore` misconfiguration becomes a 512k-line leak. Not because the engineers were careless, but because the surface area was too large to fully reason about.

→ [Source on GitHub](https://github.com/vmagueta/forgekey) · [crates.io](https://crates.io/crates/forgekey)

---

I'm not claiming these tools would have prevented Anthropic's leak. They wouldn't have.

But the *instinct* is the same.

The instinct to keep things small. To fail loudly instead of silently. To know, at any given moment, exactly what you're shipping and what it does.

That instinct doesn't come from reading security blogs. It comes from being burned enough times by complexity you didn't know was there.

---

If you've ever had a deployment surprise you — these are for you.
