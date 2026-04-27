# My First Contribution to Real Space Software

In aerospace engineering, time is everything. But manipulating time at the nanosecond scale across centuries is a minefield of edge cases.

It was in one of those edge cases that I made my first contribution to a software project used in real space missions.

## The context

I'm a Software & DevOps Engineer. I work with production infrastructure for luxury brands like Dior and Hermès. Rust is my learning language (I have two projects published on crates.io), but no external contribution until this one.

Not long ago I decided I want to work with space software. This contribution was my first intentional step toward that.

Fast? Yes. Intentional? Completely.

## The project: hifitime

`hifitime` is a high-precision Rust time management library, maintained by nyx-space. It supports multiple time scales (TAI, UTC, GPS, TT, TDB, UT1) with guaranteed nanosecond precision.

It's the kind of library that can't be wrong. In space, wrong time means wrong trajectory.

## The bug

Issue #475 was opened during formal verification work with Kani, a tool that proves mathematical properties of code for **every possible input**, not just the ones you tested.

The problem was in the `try_truncated_nanoseconds()` function:

```rust
// Before — buggy
if self.centuries == i16::MIN || self.centuries.abs() >= 3 {
    Err(HifitimeError::Duration { source: DurationError::Underflow })
}
```

Subtle, but semantically wrong. `centuries >= 3` is **overflow**, not underflow. The function was returning the wrong error variant for positive overflow.

## The fix journey and what I learned about code review

My first solution was surgical, split the two cases in 4 lines. It would have solved the issue as described.

The maintainer, Christopher Rabotin, had a better idea.

Instead of fixing the symptom, he suggested rewriting the entire function using `total_nanoseconds()`, which operates in `i128` and eliminates overflow risk by construction:

```rust
// After — correct and elegant
pub fn try_truncated_nanoseconds(&self) -> Result<i64, HifitimeError> {
    let total = self.total_nanoseconds(); // i128, no overflow risk
    if total > i64::MAX as i128 {
        Err(HifitimeError::Duration { source: DurationError::Overflow })
    } else if total < i64::MIN as i128 {
        Err(HifitimeError::Duration { source: DurationError::Underflow })
    } else {
        Ok(total as i64)
    }
}
```

That wasn't "you're wrong, redo it". It was "there's a more elegant path, want to try?"

That distinction matters. Good code review doesn't police, it guides.

## 3 bugs for the price of 1

The rewrite fixed more than the original bug:

1. ✅ **Correct error variant** - the bug from the issue
2. ✅ **Removed an overly aggressive check** - `centuries.abs() >= 3` was rejecting durations that fit perfectly in `i64`. For example, `from_truncated_nanoseconds(i64::MIN)` produces `centuries == -3` but the total fits, before it was rejected, now it round-trips correctly
3. ✅ **Fixed an off-by-one in the deeply-negative branch**

## The Kani harness, a proof, not a test

The original formal harness had 5 branches mirroring the buggy implementation, essentially proving "the function does what the (buggy) function does."

After the fix, it collapsed into a single round-trip identity assertion for all `i64`:

```rust
#[kani::proof]
fn formal_duration_truncated_ns_reciprocity() {
    let nanoseconds: i64 = kani::any();
    let dur = Duration::from_truncated_nanoseconds(nanoseconds);
    assert_eq!(dur.try_truncated_nanoseconds().unwrap(), nanoseconds);
}
```

Kani proves this for all **2^64 possible inputs**. That's not a test, it's a mathematical proof.

## Lessons learned

**Formal verification is a bug magnet.** Kani finds what traditional unit tests miss, not by luck, but by exhaustive proof over every possible input.

**Code review as a design tool.** Good maintainers don't just police code, they guide you toward better architecture. Christopher's feedback wasn't "it's wrong", it was "there's a more elegant path."

**Systems engineering is about boundaries.** Handling the edges, `i16::MIN`, `i64::MAX`, `centuries >= 3`, is where the real work happens.

PR #476 was merged. `hifitime` is a little more robust, and `periapsis` now runs on a more solid foundation.

Onwards to the next orbit. 🛰️🦀

*Contribution to [nyx-space/hifitime](https://github.com/nyx-space/hifitime) — PR #476, merged on April 25, 2026.*
