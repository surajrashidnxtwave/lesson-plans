# Session 58 — Largest Rectangle in Histogram

**Duration** 55 min <!-- placement: inferred — 103-slide deck, two full approaches (precompute-based and one-pass optimal), the hardest problem in the Stack & Queue block --> · **Topic** Stack & Queue — Largest Rectangle in Histogram · **Prerequisite** Session 57 — Asteroid Collision
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Largest Rectangle in Histogram | https://docs.google.com/presentation/d/1_OHNtNxlKJdYOX6LT_zqx1NwQnuQF8T6onInuzNhN2I/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given bar heights of width 1, find the largest rectangular area that fits within the histogram's outline. *(REMEMBERING)*
2. Explain why a rectangle anchored at a given bar's height can only extend as far as the nearest shorter bar on each side. *(UNDERSTANDING)*
3. Compute Next Smaller Element (NSE) and Previous Smaller Element (PSE) arrays using a monotonic stack, and use them to calculate every bar's maximum rectangle area. *(APPLYING)*
4. Trace the optimal one-pass approach, where a single stack replaces the two separate NSE/PSE passes, and explain why the result is identical. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 57 (Asteroid Collision) (0–6 min)

Say: *"Eight quick ones on asteroid collisions before we hit the hardest problem in this block."*

**Q1.** Two asteroids of equal magnitude, moving toward each other:
`A` The larger one survives · `B` Both are destroyed · `C` Neither is affected · `D` They pass through each other

**Q2.** Two asteroids moving in the same direction:
`A` Always collide · `B` Never collide · `C` Collide only if adjacent · `D` Collide only if equal size

**Q3.** In the stack-based simulation, when a negative (left-moving) asteroid arrives, it's compared against:
`A` Every element in the array · `B` The stack top, repeatedly, until stopped or the stack empties · `C` Only the very first element pushed · `D` Nothing — it's always pushed directly

**Q4.** What is the overall time complexity of the stack-based asteroid simulation?
`A` O(1) · `B` O(N) · `C` O(N²) · `D` O(N log N)
→ *Read:* B. If this misses, restate the "each element pushed and popped at most once" argument in one line — today's problem reuses that exact argument twice, for two separate passes.

**Q5.** A positive (right-moving) asteroid meeting a negative asteroid already on the stack that's also moving left:
`A` Collides — opposite signs · `B` Doesn't collide — they're moving apart · `C` Always destroys the negative one · `D` Is an invalid input

**Q6 (MSQ — pick all correct).** Which are true of the collision simulation?
`A` It uses a stack · `B` A single incoming asteroid can destroy more than one stack element · `C` The final stack, transferred to an array, is the answer · `D` It requires sorting the input first

**Q7.** For `arr = [3, -3]`, what survives?
`A` `[3]` · `B` `[-3]` · `C` `[]` · `D` `[3, -3]`

**Q8.** True or false: same-speed movement is why same-direction asteroids never collide.
`A` True · `B` False

**Running it** — poll tool, ~25 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Draw a rough skyline on the board — bars of heights `2, 1, 5, 6, 2, 3` sitting side by side, each width 1.

Ask: *"Somewhere in this skyline is the single largest rectangle that fits entirely under the outline — not a rectangle that goes outside any bar's height. Just eyeball it — where do you think it is, and how big?"*

Let a few guesses land (the actual answer here is height 5, width 2, area 10 — bars at height 5 and 6). Then:

> *"You just did that by squinting at the whole picture at once. A computer can't squint — it needs a rule. And the rule turns out to depend on something you already know how to compute: for every single bar, how far can a rectangle at *that bar's height* stretch left and right before it hits something shorter? That 'how far' is exactly Previous Smaller Element and Next Smaller Element — the same monotonic stack from three sessions ago, run twice."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–22: Problem Statement, Examples 1 & 2 (manual rectangle enumeration), Better Approach intro (NSE/PSE) -->
Covers: Problem Statement (bars of width 1, find the largest enclosed rectangular area) → Example 1 (`[4, 2, 7, 6, 1, 5]` → area `12`) → Example 2 (`[2, 6, 4, 1, 5, 2, 7, 3]` → area `8`) → Better Approach: precompute Next Smaller Element and Previous Smaller Element for every bar using stacks, then use them to compute each bar's maximum area.

**Beats to emphasise**

- Walk Example 1's winning rectangle exactly as the deck builds it: height `6`, width `2` (bars at index 2 and 3, heights `7` and `6`) → area `12`. Say explicitly: the rectangle's height is capped by the *shorter* of the two bars it spans.
- **State the core idea as one sentence, this is the entire session:** "for every bar, if I use *its own height* as the rectangle's height, how wide can that rectangle get before it hits a shorter bar on the left, and a shorter bar on the right?"
- Name the two arrays before the dry run touches them: NSE (Next Smaller Element — nearest shorter bar to the right) and PSE (Previous Smaller Element — nearest shorter bar to the left). Width for bar `i` = `NSE[i] - PSE[i] - 1`.

**Checkpoint (at 18 min)** — cold-call:
> *"If a bar's PSE is at index 2 and its NSE is at index 7, how wide is the rectangle anchored at that bar?"*
> **Answer:** `7 - 2 - 1 = 4` — the rectangle spans strictly between the two boundary indices, not including them.

---

## Slide Block B1 (18–27 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 23–68: Dry Run computing NSE then PSE via two stack passes on arr = [2, 3, 8, 10, 6, 7, 5], area calculation per bar, Pseudocode, Complexity, Code -->
Covers: Full dry run on `arr = [2, 3, 8, 10, 6, 7, 5]` — first pass right-to-left builds NSE using a stack of indices; second pass left-to-right builds PSE the same way; then area is computed per index using `height × (NSE − PSE − 1)`.

**Beats to emphasise**

- Narrate the NSE pass exactly as the deck does: scanning right to left, at each index pop any stack index whose value is `≥` the current bar, then the new top (if any) is the NSE index. For `arr[6] = 5`: stack empty → `NSE[6] = 7` (past the end, meaning "nothing smaller to the right"). For `arr[4] = 6`: pop index 5 (`arr[5]=7 ≥ 6`), top becomes index 6 (`arr[6]=5 < 6`) → `NSE[4] = 6`.
- Then the PSE pass, same mechanism, left to right: for `arr[4] = 6`: pop indices where `arr[stk.top()] ≥ 6` (pops `10` at index 3, pops `8` at index 2), top becomes index 1 (`arr[1]=3 < 6`) → `PSE[4] = 1`.
- Compute one area together on the board: bar at index 4, height `6`, `NSE[4]=6, PSE[4]=1` → width `6-1-1=4` → area `6×4=24`. The deck's final max across all bars is `25` (bar at index 6, height `5`, spanning the full remaining width).

**Checkpoint (at 27 min)** — cold-call:
> *"Why do we need two separate passes — one for NSE, one for PSE — instead of one?"*
> **Answer:** NSE looks *forward* from each bar (nearest shorter bar to its right), so it's naturally computed scanning right-to-left; PSE looks *backward*, so it's naturally computed scanning left-to-right. They're mirror-image questions needing mirror-image scan directions.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Compute NSE and PSE Yourself" (32–38 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can build the NSE/PSE stack passes themselves on a fresh, smaller array — the exact mechanical skill the two-pass approach depends on.

**Setup line (say this):**
> *"New array: `[3, 1, 4, 2]`. I want NSE for every index, scanning right to left. Call out what gets popped and what the answer is, before I confirm."*

Run **right to left, one index at a time** (indices 3, 2, 1, 0; values `2, 4, 1, 3`):

```
i=3 (val=2) → stack empty → NSE[3] = 4 (past end) → push 3.        Stack: [3]
i=2 (val=4) → top (idx 3, val 2) < 4 → no pop → NSE[2] = 3 → push 2.   Stack: [3, 2]
i=1 (val=1) → top (idx 2, val 4) ≥ 1 → pop.
              top (idx 3, val 2) ≥ 1 → pop.
              stack empty → NSE[1] = 4 (past end) → push 1.         Stack: [1]
i=0 (val=3) → top (idx 1, val 1) < 3 → no pop → NSE[0] = 1 → push 0.   Stack: [1, 0]
```

Final NSE: `[1, 4, 3, 4]`.

**How it surfaces:** At `i=1` (value `1`), ask before revealing: *"How many pops happen, and why?"* Correct: two pops — both `4` and `2` are `≥ 1`, so both get thrown away before landing on an empty stack.

**Debrief line:**
> *"Same discipline as Next Greater Element two sessions ago — the stack only ever holds bars that could still matter, and anything that can't possibly be someone's answer anymore gets popped immediately."*

**Cut rule:** If running short, do just `i=1` — it's the only step with more than one pop, and it's the step that proves the mechanism.

---

## Slide Block B2 (38–46 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 69–100: Optimal Approach (single stack, one pass), Dry Run on arr = [2, 3, 8, 10, 6, 7, 5], Pseudocode, Complexity, Code -->
Covers: Optimal Approach — one stack, one left-to-right pass. When a bar shorter than the stack's top arrives, pop the top and *immediately* compute its area (using the just-arrived index as its right boundary and the new stack top as its left boundary), instead of precomputing NSE and PSE as separate arrays first.

**Beats to emphasise**

- State the conceptual leap plainly: "Approach 1 computes *both* boundaries for every bar before calculating any area. Approach 2 computes each bar's area the *moment* its right boundary becomes known — during the same single pass that's still running."
- Walk the deck's dry run at the key moment: at `i=4` (value `6`), the stack holds indices for `10` and `8`, both `≥ 6` — pop `10` first (right boundary = current index `4`, left boundary = new stack top), compute its area immediately, then pop `8` the same way, then push `6`.
- At the end of the array, anything still on the stack has **no bar to its right that's ever shorter** — their right boundary is simply the end of the array (`n`), handled in one final cleanup loop.

**Checkpoint (at 46 min)** — cold-call:
> *"In the optimal approach, when exactly does a bar's area get computed — at the start, at the end, or somewhere in between?"*
> **Answer:** The moment it gets popped — which happens exactly when a shorter bar arrives to its right, giving it its right boundary right then and there.

---

## ⚡ Activity 2 — Live Trace: "One Pass, Compute as You Go" (46–52 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can run the optimal one-pass approach themselves, computing an area at the exact moment of a pop rather than deferring it — the core difference from Activity 1's two-pass approach.

**Setup line (say this):**
> *"Same array as Activity 1: `[3, 1, 4, 2]`. One pass, left to right. Every time something gets popped, tell me its area before I confirm — height × (current index − new top index − 1), or height × current index if the stack goes empty."*

Run **left to right, one index at a time**:

```
i=0 (val=3) → stack empty → push.                                    Stack: [0]
i=1 (val=1) → top (idx 0, val 3) ≥ 1 → pop.
              stack empty → area = 3 × 1 = 3 (width = current index 1)
              → push 1.                                              Stack: [1]
i=2 (val=4) → top (idx 1, val 1) < 4 → no pop → push.                 Stack: [1, 2]
i=3 (val=2) → top (idx 2, val 4) ≥ 2 → pop.
              new top (idx 1, val 1) < 2 → area = 4 × (3 − 1 − 1) = 4
              → push 3.                                              Stack: [1, 3]
End of array → remaining: pop idx 3 (val 2): new top idx 1 → area = 2 × (4 − 1 − 1) = 4
              pop idx 1 (val 1): stack empty → area = 1 × 4 = 4
```

Maximum area across all pops: `4`.

**How it surfaces:** At `i=1`, ask before revealing: *"The stack goes empty after this pop — what's the width?"* Correct: when the stack empties, there's no left boundary at all, so the width is simply the current index itself (everything from the start of the array up to here).

**Debrief line:**
> *"Every bar gets its area computed exactly once, the instant its right boundary is known — no separate NSE and PSE arrays needed. Same answer as Activity 1's two-pass method, one pass instead of two."*

**Cut rule:** If running short, do only `i=1` and the end-of-array cleanup — together they cover both the "normal pop" case and the "stack goes empty" edge case.

---

## Exit Ticket (52–55 min)

> In one sentence: what does the optimal one-pass approach do differently from the NSE/PSE approach, given that both produce the same final answer?
> **Answer:** The NSE/PSE approach computes both boundaries for every bar first, in two separate passes, then calculates all areas afterward; the one-pass approach calculates each bar's area immediately when it's popped, using whatever boundary information is available at that exact moment — one pass, not two.

**Homework:** compute the largest rectangle for `[6, 2, 5, 4, 5, 1, 6]` using either approach, by hand. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The largest rectangle must use the tallest bar in the array | Instinct: "biggest number should matter most" | Session hook — the tallest bar is often narrow, and a shorter-but-wider rectangle can beat it (Example 1: `6` beats `10`'s isolated width of 1) |
| NSE and PSE can both be computed in a single pass | Feels redundant to scan the array twice | Slide Block B1's checkpoint — NSE looks forward, PSE looks backward; they need opposite scan directions in the two-pass method |
| In the one-pass approach, area is computed once at the very end, for every bar at once | The two-pass method primed this expectation | Activity 2 — each area appears mid-pass, exactly at the pop that reveals a bar's right boundary |
| When the stack empties during a pop, the width is `0` or undefined | No left boundary feels like a broken case rather than a simple one | Activity 2, `i=1` — width is just the current index itself; "no smaller bar to the left" simply means the rectangle reaches all the way to the start |
| The end-of-array cleanup loop is a separate algorithm bolted on | It looks structurally different from the main loop | Frame it as the exact same pop-and-compute logic, just using `n` (the array length) as the right boundary instead of a real index, since nothing shorter ever showed up |

---

## Instructor Notes

- **This is the hardest session in the block — protect time for it elsewhere in the week if the schedule allows.** Two full approaches, each requiring a full stack-mechanics trace, is a lot for one sitting.
- **Do not skip Approach 1 (NSE/PSE) to save time and jump straight to the optimal approach.** Students need the "compute boundaries first, then areas" version to appreciate what the one-pass approach is actually optimising away. This is spelled out explicitly in the Exit Ticket for a reason.
- **Pacing risk:** if behind by minute 38, compress Activity 1 to its cut rule (the single `i=1` step) rather than cutting Slide Block B1's dry run — the two-pass mechanism has to land solidly before the one-pass version makes any sense.
- **This is the fourth and final monotonic-stack problem in this block** (after Monotonic Stack itself, Next Greater Element, and Asteroid Collision). If time allows, close with an explicit one-line recap tying all four together: "one invariant — pop while the top violates your order — four completely different-looking problems."
