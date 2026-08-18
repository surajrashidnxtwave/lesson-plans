# Session 57 — Asteroid Collision

**Duration** 50 min <!-- placement: inferred — 106-slide deck with two full worked examples plus a dense dry run; among the longer sessions in this block --> · **Topic** Stack & Queue — Asteroid Collision · **Prerequisite** Session 56 — Next Greater Element
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Asteroid Collision | https://docs.google.com/presentation/d/1trHgk2ucVk3foQoA1rsUq-UXjv5dZd2ixi0HQCXhKGc/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the collision rules: smaller asteroid destroyed, equal-size asteroids both destroyed, same-direction asteroids never collide. *(REMEMBERING)*
2. Explain why a stack models this problem naturally — the most recently surviving asteroid is exactly what a new left-moving asteroid must be compared against first. *(UNDERSTANDING)*
3. Trace the stack-based simulation on a given array, correctly handling multi-step collision chains. *(APPLYING)*
4. Distinguish the three distinct "no collision" cases (same direction, moving apart, one already destroyed) from the two collision-resolution cases (smaller explodes, equal-size both explode). *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 56 (Next Greater Element) (0–6 min)

Say: *"Eight quick ones on Next Greater Element before asteroids start colliding."*

**Q1.** The Next Greater Element for a position is:
`A` The largest element anywhere in the array · `B` The closest larger element to its right · `C` The closest larger element to its left · `D` The average of all elements to its right

**Q2.** The brute-force approach to NGE runs in:
`A` O(n) · `B` O(n log n) · `C` O(n²) · `D` O(1)

**Q3.** The optimal approach scans the array:
`A` Left to right · `B` Right to left · `C` From the middle outward · `D` In sorted order

**Q4.** The optimal approach's monotonic stack is:
`A` Monotonically increasing · `B` Monotonically decreasing · `C` Sorted every step · `D` Not monotonic at all
→ *Read:* B. If this misses, restate the pairing rule in one line — today's stack-based simulation also relies on knowing exactly what's sitting on top of the stack at all times.

**Q5.** In the optimal approach, when does an element get popped from the stack?
`A` Never · `B` When it's smaller-or-equal to the incoming element · `C` When it's larger than the incoming element · `D` Every single step, regardless

**Q6.** For a strictly decreasing array like `[9, 7, 5, 3]`, what's the NGE for every position?
`A` All `-1` · `B` All equal to the next element · `C` All equal to `9` · `D` Undefined

**Q7 (MSQ — pick all correct).** Which are true of both the brute-force and optimal NGE approaches?
`A` They produce identical output on the same input · `B` They both use a stack · `C` The last element's answer is always `-1` · `D` They both run in O(n²)

**Q8.** The optimal approach achieves O(n) because:
`A` It skips some elements entirely · `B` Each element is pushed and popped at most once · `C` It uses recursion · `D` It sorts the array first

**Running it** — poll tool, ~25 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Write this on the board: `[5, 10, -10, -5]` (positive = moving right, negative = moving left).

Ask: *"Picture these as literally asteroids in a straight line in space, each moving at the same speed in the direction its sign shows. Which two are ever going to meet?"*

Let students reason: `10` (moving right) and `-10` (moving left) are heading toward each other. Then:

> *"When they meet, one of two things happens: the bigger one survives and the smaller one is destroyed, or if they're exactly equal, both are destroyed. Same-direction asteroids never catch each other — they're moving at identical speed. Today we simulate an entire line of these collisions, and it turns out a stack is exactly the right tool, for a very physical reason: the only asteroid a new left-mover can possibly hit is whichever one most recently survived and is still in front of it."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–29: What is an Asteroid, Problem Statement, Examples 1 & 2 (full collision walkthroughs) -->
Covers: Problem Statement (array of integers — size is magnitude, sign is direction; same speed; collisions destroy the smaller, or both if equal; same-direction asteroids never collide) → Example 1 (`[4, 8, -3, 9, 7, -8]` → `[4, 8, 9]`) → Example 2 (`[20, 5, 10, -10, -20]` → `[]`, everything destroyed).

**Beats to emphasise**

- Walk Example 1's chain exactly as the deck does: `8` and `-3` collide (opposite directions) → `|8| > |3|` → `-3` explodes. Then `9` and `7` and `-8`: `7` and `-8` collide → `|-8| > |7|` → `7` explodes. Then `9` and `-8` collide → `|9| > |-8|` → `-8` explodes. Final: `[4, 8, 9]`, all moving right, no more collisions possible.
- **Say explicitly why Example 2 ends empty:** every remaining pair keeps colliding until the very last two (`20` and `-20`) are exactly equal — both explode, leaving nothing.
- Name the three genuinely distinct "nothing happens" cases up front, since they get confused with each other: same direction (never collide), moving apart (e.g. positive then further positive — never meet), and "already resolved" (an asteroid that already exploded is simply gone, not compared again).

**Checkpoint (at 17 min)** — cold-call:
> *"In Example 1, why doesn't `4` ever collide with anything, even though there are several collisions happening to its right?"*
> **Answer:** `4` is moving right, and every asteroid to its right that survives is also eventually moving right (or has already been destroyed) — they're moving apart or in the same direction, so `4` never catches up to anything and nothing catches up to `4`.

---

## Slide Block B1 (17–25 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 30–61: Approach, Dry Run on arr = [7, 5, 4, -5, -6, -8, -9, 12] -->
Covers: Approach (use a stack to track surviving asteroids; push positives directly; for negatives, resolve collisions against the stack top before deciding whether to push) → full dry run on `[7, 5, 4, -5, -6, -8, -9, 12]`.

**Beats to emphasise**

- Narrate the dry run's collision chain exactly as the deck does: `7, 5, 4` all push (all positive, no collisions yet). `-5` arrives: collides with top `4` → `|4| < |-5|` → `4` explodes; compare `-5` against new top `5` → `|5| == |-5|` → **both** explode. `-6` arrives: collides with top `7` → `|7| > |-6|` → `-6` explodes, `7` survives untouched.
- Continue: `-8` arrives: collides with `7` → `|-8| > |7|` → `7` explodes; stack now empty → push `-8` directly (nothing left to collide with). `-9` arrives: top is `-8`, **same direction** (both negative) → no collision → push `-9`. `12` arrives: top is `-9`, moving apart (12 right, -9 already past and moving further left) → no collision → push `12`.
- Final stack, bottom to top: `[-8, -9, 12]` — say clearly this is the survivor list, not sorted, not filtered — exactly whatever was left standing.

**Checkpoint (at 25 min)** — cold-call:
> *"When `-9` arrives and the stack top is `-8`, why is there no collision, even though one is negative-ish and could look like a candidate?"*
> **Answer:** Both `-8` and `-9` are moving in the *same* direction (left) — same-direction asteroids never collide, regardless of size.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Spot the Bug: "Collide or Not?" (30–36 min)

**Format:** Spot the Bug / Predict-the-Output · **Exposes:** whether students can correctly classify each pairwise interaction into "collide" vs. "no collision," and if colliding, resolve the outcome — the single skill the whole algorithm depends on.

**Setup line (say this):**
> *"Four pairs of asteroids about to meet or not meet. For each: do they collide? If yes, what survives?"*

Put all four on screen at once:

```
1.  Stack top = 6,  incoming = -6
2.  Stack top = -3, incoming = -9
3.  Stack top = 4,  incoming = 9    (incoming is positive)
4.  Stack top = -2, incoming = 8
```

**What students do:** 45 seconds silent, then hands up. Take one pair per student.

**Answers**

| # | Collide? | Outcome |
|---|---|---|
| 1 | Yes | Equal magnitude, opposite directions → both explode |
| 2 | No | Both negative (same direction, moving left) → no collision, both survive, `-9` pushed on top |
| 3 | No | Incoming is positive (moving right) → same direction as everything already on the stack moving right → no collision, `9` just pushed |
| 4 | Yes | Top is negative (moving left), incoming is positive (moving right) — **wait, check direction of travel**: top already moving left means it's moving *away* from anything arriving after it moving right → no collision, both survive |

**How it surfaces:** Pair 4 is the trap — students will often assume "opposite signs always means collide," but a `-2` sitting on the stack is already moving *left*, and an `8` arriving after it is moving *right*: they're moving apart, not toward each other. Only a *positive* on the stack (moving right, "waiting") followed by a *negative* incoming (moving left, "approaching") is a real collision setup.

**Debrief line:**
> *"Opposite signs are necessary but not sufficient. The one collision shape that matters is: something on the stack moving right, and something new arriving moving left — anything else is either same-direction or already moving apart."*

**Cut rule:** If running short, do pairs 1 and 4 only — pair 1 is the clean collision case, pair 4 is the trap that catches almost everyone.

---

## Slide Block B2 (36–43 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 62–92: Pseudocode, Complexity Analysis, Code, second dry run example -->
Covers: Pseudocode (while-loop collision resolution against the stack top, per incoming negative) → complexity (O(N) time, O(N) space — each element pushed and popped at most once, plus the final array transfer) → the deck's second full dry run, `[-3, 25, 10, 15, 12, 8, -12, -20]` → `[-3, 25]`.

**Beats to emphasise**

- Point directly at the `while` loop in the pseudocode: an incoming negative asteroid doesn't just check the stack top *once* — it keeps colliding and popping as long as the top is positive and smaller, exactly like a monotonic stack's pop loop.
- Complexity: total pushes and pops across the whole run are bounded by roughly `2N` (each element enters the stack at most once, leaves at most once) — same argument students built for themselves back in Session 53's Activity 2.
- In the second example, walk the chain once quickly: `8` and `-12` collide → `8` explodes. `12` and `-12` collide, equal → both explode. `15` and `-20` collide → `15` explodes. `10` and `-20` collide → `10` explodes. `25` and `-20` collide → `-20` explodes (`25` is bigger). Final: `[-3, 25]`.

**Checkpoint (at 43 min)** — cold-call:
> *"In that second dry run, why does `-3` (the very first element) survive untouched the whole time?"*
> **Answer:** `-3` is moving left with nothing to its left to collide with, and it's never compared against anything after it either — everything to its right that survives ends up moving right or already resolved, so `-3` just sits at the bottom of the stack the entire run.

---

## ⚡ Activity 2 — Live Trace: "Run the Full Chain" (43–48 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can execute a multi-step collision chain themselves, including a case where one incoming asteroid destroys more than one stack element in a row.

**Setup line (say this):**
> *"New array: `[6, 3, -8, 2]`. Walk it with me, one element at a time — tell me what's pushed, what collides, and what survives, before I confirm."*

Run **one element at a time**:

```
6   → stack empty → push.                                  Stack: [6]
3   → 3 moving right, same direction as top → push.         Stack: [6, 3]
-8  → collides with top 3: |3| < |-8| → 3 explodes.
      collides with new top 6: |6| < |-8| → 6 explodes.
      Stack now empty → push -8.                            Stack: [-8]
2   → 2 moving right, top is -8 moving left → moving apart, no collision → push.   Stack: [-8, 2]
```

Final stack: `[-8, 2]`.

**How it surfaces:** At `-8`, ask before revealing each step: *"Does it stop after destroying `3`, or keep going?"* Correct: it keeps going — `-8` is not yet resolved, so it must keep checking the new top (`6`) until either something bigger stops it or the stack empties.

**Debrief line:**
> *"One incoming asteroid destroyed two stack elements in a row, in a single step of the outer loop — that's the `while`, not `if`, doing its job. It only stops early if it meets something bigger than itself, or something moving the same direction."*

**Cut rule:** If running short, do only the `-8` step — it's the one that carries the whole "keep colliding until stopped" lesson; `6`, `3`, and `2` are all simple pushes.

---

## Exit Ticket (48–50 min)

> For `arr = [8, -8]`, what survives? For `arr = [8, -9]`? For `arr = [9, -8]`?
> **Answers:** `[8, -8]` → equal magnitude → both explode → `[]`. `[8, -9]` → `|-9| > |8|` → `8` explodes → `[-9]`. `[9, -8]` → `|9| > |-8|` → `-8` explodes → `[9]`.

**Homework:** trace `arr = [5, -3, -4, 9]` by hand and state the final surviving array. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Opposite signs always means a collision | Feels like the obvious reading of "moving toward each other" | Activity 1, pair 4 — a negative already on the stack (moving left) followed by a positive arriving (moving right) are moving *apart*, not colliding |
| An incoming asteroid only ever resolves one collision before being pushed | Carried over from underestimating pop loops generally (same trap as Session 53/56) | Activity 2's `-8` step — one incoming element destroys two stack elements in sequence via a `while`, not an `if` |
| Equal-magnitude collisions leave one survivor | Most collision intuitions expect a "winner" | Explicit callout, both dry runs and Activity 1 pair 1: equal magnitude means **both** are destroyed, no winner |
| Same-direction asteroids can still collide if one is "catching up" | The problem states all asteroids move at the same speed, which is easy to skim past | Restate explicitly: identical speed means same-direction asteroids maintain constant distance forever — same direction always means no collision |
| The stack at the end needs sorting or filtering to get the final answer | The final transfer-to-array step looks like extra processing | Point out the transfer only reverses order (stack is LIFO, answer needs left-to-right) — no filtering or sorting happens, the stack already holds exactly the survivors |

---

## Instructor Notes

- **Activity 1's pair 4 is the single most important five minutes of this session.** Almost every student will initially say "opposite signs, must collide" — do not rush past the correction. This misconception, uncorrected, breaks every dry run for the rest of the session.
- **This is a long deck (106 slides) but most of the length is the two full worked examples, not new mechanism** — after Slide Block A's first example, the algorithm itself doesn't change; you're building fluency, not new rules. Pace accordingly and don't feel obligated to narrate every slide.
- **Pacing risk:** if behind by minute 36, compress Slide Block B2's second example to a summary ("same mechanism, chain of four collisions, ends at `[-3, 25]`") rather than narrating it step by step, and protect Activity 2's live trace instead.
- **Bridge to Session 58:** mention at the close that Largest Rectangle in Histogram uses a monotonic stack again, in yet another physical framing — the pattern recurring for a fourth time by now should be explicit and reassuring, not a surprise.
