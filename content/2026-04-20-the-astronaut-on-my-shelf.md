# The Astronaut on My Shelf
## Why I'm building an orbital mechanics engine in Rust

I have an astronaut figurine on my shelf. I always had. But for a long time I was so caught up in day-to-day work (servers, deployments, production incidents at 2am) that I forgot to look at it.

The Artemis 2 launch made me remember.

---

## The moment of clarity

In a recent job interview, someone asked me about the objects in my workspace. I looked at the shelf and realized: I have astronaut figurines, my phone case is an astronaut coming out of a black hole, I'm watching For All Mandkind and I used to watch NASA launches as a kid.

I've always been passionate about space. I just forgot.

That clarity changed something in me. I don't just want to be a software engineer. I want to be a software engineer who works at companies that send things to space.

---

## Why Rust?

I work with Python every day. Python is my main language, I use it in production, I trust it.

But when I started researching the space software industry, one thing became clear: Rust is growing fast in this sector. ESA is working to certify Rust for safety-critical systems. NASA has published guidelines for Rust usage. NewSpace startups like D-Orbit and others are already using it.

It makes sense. Systems that control satellites can't have memory bugs. Rust solves that at compile time, not at runtime (when it's already too late).

I also already have two published Rust projects: [`forgekey`](https://github.com/vmagueta/forgekey) a fast, minimal password generator CLI. And now `periapsis`. I'm not an expert. I'm someone learning through real projects, the way I learn best.

---

## What is periapsis?

Periapsis is the closest point of an orbit relative to the central body. The ISS reaches periapsis on each of the 15 orbits it completes around Earth every day.

The `periapsis` project is an orbital mechanics engine in Rust. The idea is to build a tool capable of:

- Parsing **TLE** (Two-Line Element) data, the standard format for describing satellite orbits
- Propagating orbits using **SGP4**, the algorithm used by NASA and NORAD to track objects in orbit
- Visualizing trajectories in an interactive **TUI** (terminal interface)
- Exposing calculations via **REST API**
- Eventually, rendering orbits graphically

It's ambitious. I'm in early development. But the roadmap is clear and I'm building in public.

---

## Why am I talking about this now?

Because I think the journey matters as much as the destination.

When `periapsis` has a working TUI, I'll write about it. When I implement SGP4, I'll write about it. When I make mistakes (and I will), I'll write about those too.

If you work at a space company and made it this far: hello. I'm building something that brings me closer to your world, and I'm doing it on purpose.

If you're a developer with an astronaut figurine on your shelf: maybe it's time to look at it again.

---

*`periapsis` is on [GitHub](https://github.com/vmagueta/periapsis) and [crates.io](https://crates.io/crates/periapsis). Early development, building in public. 🛰️🦀*
