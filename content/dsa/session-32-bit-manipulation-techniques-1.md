# Session 32 — Bit Manipulation Techniques 1

**Duration** 45 min · **Topic** Bit Manipulation — Check / Set / Clear / Toggle a Bit · **Prerequisite** Session 31 (Bitwise Operators)
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Bit Manipulation Techniques 1 | https://docs.google.com/presentation/d/1vWWyWe-rYEbSH1WZNv_qQbcS6xQ2Te4XswExeEBISM8/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the formula to check whether the i-th bit of N is set: `N & (1 << i)`. *(REMEMBERING)*
2. State the formula to set the i-th bit: `N | (1 << i)`. *(REMEMBERING)*
3. State the formula to clear the i-th bit: `N & ~(1 << i)`. *(REMEMBERING)*
4. State the formula to toggle the i-th bit: `N ^ (1 << i)`. *(REMEMBERING)*
5. Apply each of the four formulas by hand to compute the result for a given N and i. *(APPLYING)*
6. Explain why each formula uses the specific operator it does — tying the operator's truth-table behavior (from Session 31) to what each technique needs to accomplish. *(ANALYZING)*

<!-- placement: inferred from the Key Takeaways slide (24), which restates exactly these four formulas as the session's summary. -->

---

## Warm-Up Poll — Retrieval Practice on Session 31 (0–6 min)

> Retrieval practice on **Bitwise Operators**. Solo answers, no discussion yet.

**Q1.** Bitwise AND (`&`) sets a result bit to 1 when:
`A` At least one input bit is 1 · `B` Both input bits are 1 · `C` Exactly one input bit is 1 · `D` Neither input bit is 1
→ *Read:* Answer B.

**Q2.** Bitwise OR (`|`) sets a result bit to 1 when:
`A` At least one input bit is 1 · `B` Both input bits must be 1 · `C` Exactly one input bit is 1 · `D` Never
→ *Read:* Answer A.

**Q3.** Bitwise XOR (`^`) sets a result bit to 1 when:
`A` Both input bits are 1 · `B` Neither is 1 · `C` Exactly one input bit is 1 · `D` At least one is 1
→ *Read:* Answer C.

**Q4.** What does `~5` print as, in a 32-bit signed integer system?
`A` `10` (the naive 4-bit flip) · `B` `-6` · `C` `-5` · `D` An error
→ *Read:* Answer B — NOT flips all 32 bits, including the leading zeros, and the sign bit makes the printed value negative.

**Q5.** `n << k` is equivalent to:
`A` `n / 2^k` · `B` `n * 2^k` · `C` `n - k` · `D` `n mod k`
→ *Read:* Answer B.

**Q6.** `n >> k` is equivalent to:
`A` `n * 2^k` · `B` `n + k` · `C` `n / 2^k` (integer division) · `D` `n XOR k`
→ *Read:* Answer C.

**Q7.** *(MSQ — pick 2)* Which are real-world uses of bitwise operations named in the deck?
`A` Image compression · `B` Sorting a linked list · `C` Cryptography · `D` Recursion
→ *Read:* A and C.

**Running it** — poll tool, ~40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Imagine a control panel with 32 switches in a single row, packed into one number. I want to know if switch number 5 is on — without touching or even looking at any other switch. How would you isolate exactly one bit out of 32?"*

Let a few guesses land — most will say something like "shift it to the front" or "mask everything else out." Say: *"You've basically just described today's first formula. Today is four small formulas, all built entirely out of AND, OR, XOR, and shift — the four operators from yesterday, aimed at one bit at a time."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 4-13: check if a bit is set, and set a bit -->
Covers: Checking if the i-th bit is set (`N & (1 << i)`) → worked example (`N=21, i=2` → non-zero result 4, so bit is set) → Setting the i-th bit (`N = N | (1 << i)`) → worked example (`N=20, i=3` → result 28).

**Beats to emphasise**

- **Check-if-set is not "does the result equal 1?"** — say this explicitly, it's the deck's own phrasing (slide 6): *"If the ith bit in N is set, this operation will result in a non-zero value"* — the non-zero value is `2^i`, not necessarily `1`. This single sentence prevents the most common bug in this technique.
- **Set-a-bit uses OR because OR can only turn a bit *on*, never off** — `N | (1<<i)` guarantees the i-th bit becomes 1 while every other bit stays exactly as it was, since OR-ing anything with 0 leaves it unchanged.
- Walk both worked examples (`N=21, i=2` and `N=20, i=3`) column by column exactly as the deck's bit diagrams show.

**Checkpoint (at 17 min)** — cold-call:
> *"`N = 8` (binary `1000`), `i = 0`. Is bit 0 set? What does `N & (1 << 0)` actually evaluate to?"*
> **Answer:** Bit 0 is not set. `1 << 0 = 1`; `1000 & 0001 = 0000 = 0` — zero result means not set.

---

## ⚡ Activity 1 — Predict-the-Output: Check and Set on Fresh Numbers (17–22 min)

**Format:** Predict-the-Output · **Exposes:** the "result must literally equal 1" misconception, and confusion about which bit position corresponds to which power of 2.

**Setup line (say this):**
> *"New number: `N = 26`, which is `11010` in binary. Three questions, commit out loud before I confirm: is bit 1 set? Is bit 3 set? And what does `N | (1 << 0)` give you?"*

**What students do:** Reason through `11010`: bit 1 (value 2) is `1` → set, confirmed by `26 & 2 = 2` (non-zero, not necessarily `1`). Bit 3 (value 8) is `1` → set, confirmed by `26 & 8 = 8`. Setting bit 0 (`1 << 0 = 1`): `11010 | 00001 = 11011 = 27`.

**How to handle wrong answers:** If someone says "bit 3 isn't set because `26 & 8 = 8`, not `1`," that's the exact misconception this activity targets — point back to the checkpoint phrasing: *non-zero* means set, and the non-zero value tells you nothing extra, it's just a side effect of which power of 2 you multiplied by.

**Debrief line:**
> *"The check-bit formula never promises to hand you a clean `0` or `1` — it hands you `0` or `2^i`. Treat anything non-zero as 'set' and stop expecting a tidy 1. That single habit removes most of the bugs people write with this formula."*

**Cut rule:** If running short, drop the "set bit 0" prediction and keep only the two check-bit questions — the non-zero misconception is the higher-value catch.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (27–36 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 14-24 (plus recap worked examples on slides 30-35): clearing a bit, and toggling a bit -->
Covers: Clearing the i-th bit (`N = N & ~(1 << i)`) → worked example (`N=28, i=3` → result 20) → Toggling the i-th bit (`N = N ^ (1 << i)`) → worked example (`N=10, i=3` → result 2).

**Beats to emphasise**

- **Clear-a-bit is a two-step build, not one operator:** first left-shift 1 by i to mark the target bit, *then* invert everything with `~` (turning the target into the only 0 in a sea of 1s), *then* AND with N. Narrate all three steps every time — skipping the middle inversion is the most common way students misremember this formula.
- **Toggle uses XOR because XOR is the only operator whose behavior depends on the current state of the bit** — AND and OR always push a bit toward a fixed value (0 or 1) regardless of what it started as; XOR flips whatever is already there.
- Point out the parenthesization in `N & ~(1 << i)`: the shift happens first, then the NOT, then the AND. Getting this order wrong (applying `~` before the shift) produces a completely different, wrong bitmask — flag this now, it's the whole point of the next activity.

**Checkpoint (at 36 min)** — show of hands:
> *"If I toggle bit 2 of a number twice in a row, what happens to the number?"*
> **Answer:** Nothing — it returns to its original value. Toggling twice is `N ^ (1<<i) ^ (1<<i)`, and `x ^ x = 0`, so the second toggle cancels the first (same identity from Session 29/31's XOR properties).

---

## ⚡ Activity 2 — Spot the Bug: Missing Parentheses in the Clear-Bit Formula (36–41 min)

**Format:** Spot the Bug · **Exposes:** the operator-precedence trap where `~` binds *tighter* than `<<`, so dropping the inner parentheses silently changes what the code computes.

**Setup line (say this):**
> *"Two lines of code. One is the formula from the slide. One is what happens if a student 'cleans up' the parentheses because they look redundant. Tell me if they're the same."*

Put both on screen:
```cpp
int clearBit_correct = N & ~(1 << i);   // as taught
int clearBit_buggy   = N & ~1 << i;     // parentheses removed
```

**What students do:** 60 seconds to reason about operator precedence. In C++ (and most C-family languages), unary `~` binds tighter than `<<`, so `~1 << i` actually evaluates as `(~1) << i` — NOT `~(1 << i)`. `~1` is already a huge negative-looking bit pattern (all bits set except bit 0), and shifting *that* left by `i` produces a completely different mask than the intended "every bit is 1 except position i."

**How to handle wrong answers:** If students assume the two lines must be equivalent because "the parentheses look decorative," have them evaluate both for a concrete case (`N=28, i=3`) and compare to the deck's known-correct answer (20) — the buggy version will not match.

**Debrief line:**
> *"Parentheses around `1 << i` inside a `~(...)` are never decorative in this formula — `~` binds tighter than `<<`, so removing them changes the order of operations and silently breaks the mask. When a bit-manipulation formula has nested operators, assume every parenthesis is load-bearing until you've proven otherwise."*

**Cut rule:** If short on time, skip the live precedence evaluation and just state the rule directly (`~` binds tighter than `<<`) with the two code lines on screen as visual evidence — the core warning survives without the full derivation.

---

## Exit Ticket (41–45 min)

> On paper before anyone leaves: For `N = 9` (binary `1001`), write the formula and result for (a) checking if bit 1 is set, and (b) toggling bit 1.
> **Answers:** (a) `9 & (1 << 1)` = `1001 & 0010` = `0000` = 0 → bit 1 is **not** set. (b) `9 ^ (1 << 1)` = `1001 ^ 0010` = `1011` = **11**.

Scan on the way out. If students report the check-bit answer as anything other than "not set" based on a non-zero-vs-zero read (rather than expecting a specific number), Activity 1's misconception is still active — reopen briefly at the start of Session 33.

**Homework:** re-derive all four formulas (check, set, clear, toggle) from memory for a number and bit index of your own choosing. <!-- placement: inferred — no homework/practice-unit table exists for this course; this is a natural close, not a platform assignment -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The check-bit formula must return exactly `1` when the bit is set | "Set" intuitively means "true," and true feels like it should be `1` | Activity 1 — showing the result is `2^i`, and treating anything non-zero as "set" |
| Removing "redundant-looking" parentheses in `N & ~(1 << i)` doesn't change the result | Parentheses around a single shift expression look stylistic rather than functional | Activity 2 — showing `~1 << i` evaluates completely differently from `~(1 << i)` due to `~` binding tighter than `<<` |
| Clearing a bit is a single-operator operation, like setting or checking | Set uses one OR, check uses one AND — clear "should" be similarly simple | Slide Block B — narrating clear as a three-step build (shift, invert, AND), not a single operator |
| Toggling a bit twice does something (like clearing it, or leaving it "twice as toggled") | Doubling an operation intuitively feels like it should compound, not cancel | Checkpoint after Slide Block B — connecting to the `x ^ x = 0` identity already seen in Sessions 29 and 31 |
| Bit index `i` starts counting from 1 (the first bit), not 0 | Everyday counting starts at 1 | Referring back to Session 29's LSB definition — bit 0 is the LSB, by convention |

---

## Instructor Notes

- **This is a compact, four-formula session** — the whole session is essentially session 31's operators aimed at a single bit. Resist over-explaining; the value is in students being able to recite and apply all four formulas fluently, not in novel content.
- **The non-zero-vs-exactly-1 subtlety (check-bit) and the parentheses precedence trap (clear-bit) are the two highest-value catches** — if time runs short, protect these two over the toggle-twice checkpoint.
- **Keep all four formulas on the board simultaneously** for the whole session (`N & (1<<i)`, `N | (1<<i)`, `N & ~(1<<i)`, `N ^ (1<<i)`) — side-by-side visibility is what makes the "which operator, and why" reasoning in the learning objectives land.
- **Session 33 (Bit Manipulation Techniques 2) builds directly on toggle and the `N & (N-1)` trick** — if this session's pacing slips, protect Slide Block B over Activity time, since the formulas themselves are the load-bearing content going forward.
- **Classroom Quiz question bank does not exist yet for this course** — pull 5–6 MCQs from the platform live at the marked point.
