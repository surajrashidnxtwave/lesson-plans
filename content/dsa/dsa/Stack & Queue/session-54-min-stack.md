# Session 54 — Min Stack

**Duration** 50 min <!-- placement: inferred — 83-slide deck covering two full approaches, the richest session in this block --> · **Topic** Stack & Queue — Implement Min Stack · **Prerequisite** Session 53 — Monotonic Stack
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Implement Min Stack | https://docs.google.com/presentation/d/1zvk5bu2qxqDY8Ccnd6i09yAhnaLxxM04Ge0MYChq9pw/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the Min Stack requirement: `push`, `pop`, `top`, and `getMin` must all run in O(1) time. *(REMEMBERING)*
2. Explain the pair-stack approach — storing `(value, currentMinAtThatPoint)` at every level — and why popping it automatically restores the previous minimum. *(UNDERSTANDING)*
3. Trace both the pair-stack approach and the single-stack encoded-value approach on the same input sequence. *(APPLYING)*
4. Analyse why the encoded-value approach still achieves O(1) per operation while cutting space from O(2n) to O(n), and decode an encoded sentinel value by hand. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 53 (Monotonic Stack) (0–6 min)

Say: *"Eight quick ones on yesterday's monotonic stack before we bolt a new trick onto plain stacks."*

**Q1.** A monotonically increasing stack is useful for finding:
`A` The next/previous smaller element · `B` The next/previous greater element · `C` The maximum subarray · `D` A balanced bracket sequence

**Q2.** A monotonically decreasing stack is useful for finding:
`A` The next/previous smaller element · `B` The next/previous greater element · `C` A cycle in a list · `D` A palindrome

**Q3.** For `arr = [5, 2, 8, 6, 3]`, building a monotonically increasing stack left to right, what is the final stack (bottom → top)?
`A` `[5, 2, 8, 6, 3]` · `B` `[2, 3]` · `C` `[8, 6, 3]` · `D` `[5]`
→ *Read:* B. If this misses, re-run yesterday's dry run in one sentence before moving on — today's Min Stack dry run assumes this trace is fluent.

**Q4.** The pop rule for a monotonically increasing stack is: while the top is ___ the incoming element, pop.
`A` less than · `B` greater than · `C` equal to · `D` not equal to

**Q5.** Across a full monotonic-stack build over n elements, each element is pushed and popped at most how many times?
`A` n times each · `B` Once each · `C` log n times each · `D` It depends on the array

**Q6.** What makes a monotonic stack O(n) instead of O(n²)?
`A` It uses recursion · `B` Each element is touched a bounded number of times, not compared against every other element · `C` It sorts the array first · `D` It only works on small arrays

**Q7 (MSQ — pick all correct).** Which of these are still true of a monotonic stack, same as any other stack?
`A` You only ever push/pop from the top · `B` It never stores more than one copy of an element · `C` You can peek the top without removing it · `D` It requires extra memory proportional to the array size, same as a regular stack

**Q8.** True or false: a monotonic stack changes *what* you can store in a stack, not *how* a stack works mechanically.
`A` True · `B` False
→ *Read:* True. That's the bridge into today — Min Stack is another case of "same stack mechanics, smarter idea about what you store at each level."

**Running it** — poll tool, ~25 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Put this on the board:

> *"You're tracking stock prices as they come in, one at a time, and pushing each new one. At any moment, someone can ask you: 'what's the lowest price we've seen so far?' You need to answer instantly — not by scanning the whole list of prices every time."*

Ask: *"If I just kept a plain stack of prices, how would you answer 'what's the minimum' right now?"*

Let a student say "scan the whole stack." Then:

> *"That's O(n) every single time someone asks. Today we build a stack that answers 'what's the minimum right now' in O(1) — constant time, no matter how many prices are in it — without breaking `push`, `pop`, or `top`. Two different ways to do it, same result."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–13: Problem Statement, Examples 1 & 2, Approach 1 (pair-stack) -->
Covers: Problem Statement (`MinStack()`, `push(x)`, `pop()`, `top()`, `getMin()`, all O(1)) → two worked examples showing expected output sequences → Approach 1: store `(value, minSoFar)` pairs.

**Beats to emphasise**

- Read the operation list slowly: `push`, `pop`, `top`, `getMin` — all four, O(1), no exceptions. This is the whole spec.
- **Approach 1's core idea, said as one sentence:** "Every time you push, you also push what the minimum would be *including* this new element — so the minimum is always sitting right at the top, no digging required."
- Point at Example 1's output list (`[-5, -10, 1, -5]`) and note it's just the answers to the `getMin`/`top` calls in the order they were asked — the dry run in the next block builds exactly this.

**Checkpoint (at 18 min)** — cold-call:
> *"If I push a pair `(x, m)`, what does that second value `m` actually represent?"*
> **Answer:** The minimum of the entire stack *including* `x`, at the moment `x` was pushed — not just `x` itself.

---

## Slide Block B1 (18–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 14–36: Dry Run of Approach 1, Pseudocode, Complexity Analysis, Code -->
Covers: Full dry run of `str = ["push -5", "push 1", "getMin", "push -10", "getMin", "pop", "top", "getMin"]` using pairs, then pseudocode, complexity (all O(1) time; O(2n) space), and C++ code.

**Beats to emphasise**

- Narrate every push as a pair decision: push `-5` → stack empty → pair `(-5, -5)`. Push `1` → compare `1` vs current min `-5` → `1 > -5` so min stays `-5` → pair `(1, -5)`. Push `-10` → compare vs `-5` → `-10 < -5` → new min `-10` → pair `(-10, -10)`.
- On `pop()`: remove the top pair entirely. Say explicitly — *"the previous minimum comes back for free, because it was sitting one level down the whole time."*
- Flag the space cost directly from the deck: O(2n), because every single element carries a second integer alongside it.

**Checkpoint (at 24 min)** — show hands:
> *"After popping the pair `(-10, -10)`, what does `getMin()` return? Who says `-10`? Who says `-5`?"*
> **Answer:** `-5` — the pair `(1, -5)` is now on top, and its stored minimum is `-5`.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Predict the Pair" (29–35 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can compute the *paired minimum* themselves at each push, rather than just having watched it computed — the exact skill the dry run just demonstrated.

**Setup line (say this):**
> *"New sequence, pair-stack approach. I'll call the operation, you tell me the pair that gets pushed — or the value that gets returned — before I confirm. Sequence: `push(8)`, `push(3)`, `push(3)`, `getMin()`, `push(1)`, `pop()`, `getMin()`."*

Run **one operation at a time**, taking a prediction before each:

```
push(8)   → stack empty → pair (8, 8)                    Stack: [(8,8)]
push(3)   → 3 < 8 → new min 3 → pair (3, 3)               Stack: [(8,8),(3,3)]
push(3)   → 3 < 3? No (equal) → min stays 3 → pair (3, 3)  Stack: [(8,8),(3,3),(3,3)]
getMin()  → top pair's second value → 3
push(1)   → 1 < 3 → new min 1 → pair (1, 1)               Stack: [(8,8),(3,3),(3,3),(1,1)]
pop()     → remove (1,1)                                  Stack: [(8,8),(3,3),(3,3)]
getMin()  → top pair's second value → 3
```

**How it surfaces:** At the second `push(3)`, ask before revealing: *"Does the minimum change here? It's equal, not smaller."* Correct: minimum stays `3` — the rule is "strictly less than" triggers a new minimum; ties keep the existing one.

**Common wrong answer:** students say the second `push(3)` doesn't need a pair update at all, or push just the bare value `3` without a paired minimum. Correct by re-pointing at the invariant: *every* push gets a pair, even when the minimum doesn't change — that's what keeps `getMin()` O(1) at every single level, not just some.

**Debrief line:**
> *"Every push costs one comparison and one pair — that's the whole trick. No scanning, ever, no matter how deep the stack gets."*

**Cut rule:** If running short, do just `push(8)`, `push(3)`, and the `getMin()` after — that's the one moment (a value smaller than everything before it) that proves the mechanism; skip the tie and the pop.

---

## Slide Block B2 (35–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 37–66: Approach 2 (single-stack encoding), Dry Run, Pseudocode, Complexity, Code -->
Covers: Approach 2 — one stack, one `mini` variable. When pushing a value smaller than `mini`, push an **encoded** value (`2×x − oldMini`) instead of `x`, and update `mini = x`. On pop, if the popped value is *less than* `mini`, it was an encoding — decode the old minimum as `2×mini − poppedValue` before restoring.

**Beats to emphasise**

- This is the harder half of the session — go slower here than the pacing looks like it allows.
- Walk the deck's own dry run: `push(-5)` → stack empty → push `-5` directly, `mini = -5`. `push(1)` → `1 > -5` → push `1` directly, `mini` unchanged. `push(-10)` → `-10 < -5` → push the **encoded** value `2×(-10) − (-5) = -15`, update `mini = -10`.
- **Say this explicitly, it's the crux:** "`-15` sitting on the stack is not a real element of this Min Stack. It's a flag that means 'the real minimum just changed, and the old one was `-5`.'"
- On `pop()` of that `-15`: since `-15 < mini (-10)`, it's an encoding. Decode: `mini = 2×(-10) − (-15) = -5`. The old minimum is restored, and the encoded value itself is discarded — it was never a real stack element.

**Checkpoint (at 42 min)** — cold-call:
> *"How does `pop()` know whether the value it just removed was a real element or an encoded sentinel?"*
> **Answer:** Compare it to the current `mini` — if the popped value is *less than* `mini`, it was an encoding (a real element can never be smaller than the tracked minimum); otherwise it's a genuine value.

---

## ⚡ Activity 2 — Spot the Bug: "Decode It Yourself" (42–47 min)

**Format:** Spot the Bug / Predict-the-Output · **Exposes:** whether students actually understand the decode formula, versus having just watched it happen once — this is the single most error-prone step in the whole session.

**Setup line (say this):**
> *"Stack currently holds an encoded value of `-21` on top, and `mini` is currently `-8`. I'm about to `pop()`. Before I show you the arithmetic: is `-21` a real element or an encoding? And if it's an encoding, what does `mini` become after the pop?"*

**What students do:** 30 seconds silent, then hands up.

**Answer:** `-21 < mini (-8)` → it's an encoding. Decode: `mini_new = 2 × mini_old − poppedValue = 2×(-8) − (-21) = -16 + 21 = 5`. Wait — deliberately let a student catch that this looks like an unreasonably large jump, then re-anchor: the formula is mechanical and always correct *provided* the encoding was written correctly at push time; the "reasonableness" check is not part of the algorithm, just a sanity habit.

**How it surfaces:** The most common wrong move is applying the *push* formula (`2×x − mini`) instead of the *pop/decode* formula (`2×mini − encodedValue`) — same two numbers, opposite arrangement. Write both formulas side by side on the board and have students say out loud which one is "going in" (push) and which is "coming out" (pop).

**Debrief line:**
> *"Two formulas, mirror images of each other. Push encodes: `2x − mini`. Pop decodes: `2·mini − encoded`. Mix them up and you get a number that looks plausible and is completely wrong — which is exactly why this bug is dangerous."*

**Cut rule:** If running short, skip having students compute the decode by hand and instead just verify the answer already worked out above, spending the saved time re-stating the two-formula distinction once more.

---

## Exit Ticket (47–50 min)

> In one sentence: why does the encoded-value approach (Approach 2) use less space than the pair-stack approach (Approach 1)?
> **Answer:** Approach 1 stores two integers per element always (O(2n) space); Approach 2 stores exactly one integer per element — a real value most of the time, an encoded sentinel only when a new minimum is set — so it stays O(n).

**Homework:** trace Approach 2 by hand on `push(4), push(4), push(1), push(3), pop(), getMin()`. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| An encoded value on the stack is a real, usable element | It sits in the same stack, looks like just another number | Activity 2's decode exercise — explicitly naming `-21` as a sentinel, not data, before showing the arithmetic |
| The push-encode formula and the pop-decode formula are interchangeable | Both involve `2 × mini` and one other value, so they look structurally identical | Writing both formulas side by side on the board in Activity 2 and naming which direction each runs |
| A new pair/encoding is only needed when the minimum actually changes | Feels wasteful to "do work" when nothing changed | Activity 1's tie case (`push(3)` when `3` is already the minimum) — still gets a full pair even though nothing changed |
| Approach 2 saves space by not tracking history at all | The single `mini` variable looks like the *only* thing being tracked | Point out the encoded values *are* the hidden history — they're just stored inline instead of in a second array |
| `getMin()` after several pops requires re-scanning what's left | Natural instinct when "the top changed" is to assume a re-check is needed | Both dry runs — `getMin()` is always a direct read (the pair's second value, or `mini` directly), never a scan |

---

## Instructor Notes

- **Pacing risk:** Approach 2's push/pop encode-decode logic is genuinely the hardest single idea in the Stack & Queue block so far. If you're behind by minute 35, do not compress Slide Block B2 — cut Activity 2 down to its stated cut rule instead, or drop Activity 1 to a single push/getMin pair.
- **Do the two formulas on the board, not just the slide.** `push`: `2x − mini`. `pop` (when encoded): `2·mini − encoded`. Leave both up for the rest of the session — students will refer back to them during Activity 2.
- **Approach 1 is not a "wrong" answer to be discarded** — frame it as the natural first idea (more space, simpler to reason about), with Approach 2 as the space-optimised refinement. Both are legitimate; interviewers accept either unless O(n) space is explicitly required.
- **Have both dry-run sequences ready before class** (the deck's `[-5, 1, -10, ...]` for Approach 1, and a fresh one for Activity 1) so you're not improvising numbers live.
- **Quiz placement note:** this session's Classroom Quiz sits after Approach 1 is fully covered but before Approach 2 begins — a natural seam, since the two approaches are independently testable.
