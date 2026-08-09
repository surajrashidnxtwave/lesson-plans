# Session 33 — Bit Manipulation Techniques 2

**Duration** 65 min · **Topic** Bit Manipulation — Swap Without Temp, Remove Rightmost Set Bit, Power-of-2 Check, Count Set Bits · **Prerequisite** Session 32 (Bit Manipulation Techniques 1)
**Session type** Concept lecture

<!-- placement: inferred — 65 min, not the 45-min default. The deck (119 slides, mostly repeated dry-run animation frames) covers four genuinely distinct techniques, two of which (power-of-2 check, count set bits) build directly on the `N & (N-1)` idea introduced in the same session — compressing risks the class leaving with a shallow grasp of the shared trick. -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Bit Manipulation Techniques 2 | https://docs.google.com/presentation/d/1y-24MHseXfRzFL0upADHstJHa8pxXdyQtnlx0t8KLU0/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Swap two numbers without a temporary variable using three XOR operations, and explain why it works using `a ^ a = 0` and `a ^ 0 = a`. *(APPLYING / ANALYZING)*
2. State and apply the formula to remove a number's rightmost set bit: `N & (N - 1)`. *(APPLYING)*
3. Determine whether a number is a power of 2 using `N & (N - 1) == 0` (with `N > 0`), and explain why exactly-one-set-bit is the defining property of powers of 2. *(ANALYZING)*
4. Count the set bits in an integer using two approaches — check-and-shift bit by bit (O(log₂ N)) and Brian Kernighan's repeated `N & (N-1)` (O(number of set bits)). *(APPLYING)*
5. Compare the two set-bit-counting approaches and state when Kernighan's approach is actually faster (and when it isn't). *(ANALYZING)*

<!-- placement: inferred from the Key Takeaways slide (38), which lists these four techniques as the session's summary. -->

---

## Warm-Up Poll — Retrieval Practice on Session 32 (0–6 min)

> Retrieval practice on **Bit Manipulation Techniques 1**. Solo answers, no discussion yet.

**Q1.** Formula to check if bit `i` of `N` is set?
`A` `N | (1 << i)` · `B` `N & (1 << i)` · `C` `N ^ (1 << i)` · `D` `N & ~(1 << i)`
→ *Read:* Answer B.

**Q2.** Formula to set bit `i` of `N`?
`A` `N & (1 << i)` · `B` `N | (1 << i)` · `C` `N ^ (1 << i)` · `D` `~N`
→ *Read:* Answer B.

**Q3.** Formula to clear bit `i` of `N`?
`A` `N ^ (1 << i)` · `B` `N | (1 << i)` · `C` `N & ~(1 << i)` · `D` `N & (1 << i)`
→ *Read:* Answer C.

**Q4.** True or false: the check-bit formula always returns exactly `1` when the bit is set.
`A` True · `B` False — it returns `2^i`, which is non-zero but not necessarily 1
→ *Read:* Answer B.

**Q5.** What happens if you toggle the same bit twice in a row?
`A` It ends up cleared regardless of its starting value · `B` It returns to its original value · `C` It's undefined behavior · `D` It sets the adjacent bit
→ *Read:* Answer B — `x ^ x = 0`, so the second toggle cancels the first.

**Q6.** *(MSQ — pick 2)* Which are true of `N & ~1 << i` (parentheses removed) versus `N & ~(1 << i)` (as taught)?
`A` They are always identical · `B` `~` binds tighter than `<<`, so they can evaluate differently · `C` The un-parenthesized version can silently compute the wrong mask · `D` Parentheses are purely stylistic here
→ *Read:* B and C.

**Running it** — poll tool, ~40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Classic interview puzzle. Swap the values of two variables, `a` and `b`, without using a third variable anywhere. Thirty seconds, shout your approach."*

Let a few attempts land — some will try arithmetic (`a = a+b; b = a-b; a = a-b`), which works but risks overflow. Say: *"There's a bitwise version of this that never risks overflow, and it's built entirely out of one property you already know: XOR-ing something with itself gives zero. That's technique one of four today — the other three all reuse a single formula, `N & (N-1)`, in three different disguises."*

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 4-10, 44-59: swap two numbers via XOR, contrasted with the third-variable swap -->
Covers: Swap Two Numbers using XOR (`a = a^b; b = a^b; a = a^b`) → worked example (`a=5, b=10` → swapped to `a=10, b=5`) → contrast against the traditional third-variable (`temp`) swap, shown step by step.

**Beats to emphasise**

- Derive the three lines live using the identities from Session 29/31, don't just state them: after `a = a^b`, `a` holds `a^b`. Then `b = a^b = (a^b)^b = a^(b^b) = a^0 = a` (original a!) — so `b` now holds the original `a`. Then `a = a^b = (a^b)^a = b^(a^a) = b^0 = b` (original b!) — so `a` now holds the original `b`.
- Show the third-variable version immediately after, side by side, so students see both are valid — the XOR version is the "no extra memory" flex, not a strictly superior replacement.
- Flag the real-world caveat explicitly (this is not on the deck, but is the standard danger of this idiom): *"If `a` and `b` ever refer to the exact same memory location, this trick zeroes the value out instead of swapping it — a genuine bug people hit in practice."*

**Checkpoint (at 19 min)** — cold-call:
> *"Why does `b = a ^ b` (the second line) end up holding the original value of `a`?"*
> **Answer:** At that point `a` already holds `a^b` and `b` still holds the original `b`. So `a ^ b = (a^b) ^ b = a ^ (b^b) = a ^ 0 = a` — the original `a`.

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: Swap 12 and 7 (19–24 min)

**Format:** Dry-Run Relay · **Exposes:** treating the XOR-swap lines as a memorized incantation rather than tracking what each variable actually holds at each step.

**Setup line (say this):**
> *"New numbers, not from the slides — `a = 12`, `b = 7`. Row by row, you tell me what each line computes and what `a` and `b` hold after it runs. I only write what you say."*

**What students do:** Relay through the three lines: `a = 12^7 = 11` (binary `1100^0111=1011`); `b = a^b = 11^7 = 12` (original `a`!); `a = a^b = 11^12 = 7` (original `b`!). Final: `a=7, b=12`.

**How to handle wrong answers:** If a student re-substitutes the *original* value of `a` or `b` instead of the *current* value after each line, stop and rewrite that line's inputs explicitly — the whole trick depends on using each variable's live value, not its starting value.

**Debrief line:**
> *"Every bug in this idiom comes from forgetting that `a` and `b` change after every line. Track current values, not original ones, and the three lines just fall out of the XOR identities you already know."*

**Cut rule:** If running short, skip the full relay and do only the first and third lines out loud, taking the middle line as given — the third line is where most tracking errors surface anyway.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–39 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 11-24, 60-82: remove the rightmost set bit, and check if a number is a power of 2 -->
Covers: Remove the Rightmost Set Bit (`N & (N-1)`) → worked examples (`12 → 8`, `21 → 20`, `72 → 64`) → Check if a Number is a Power of 2 (verify `N > 0`, then `N & (N-1) == 0`) → worked examples (`16` → true, `10` → false, `5` → false).

**Beats to emphasise**

- Derive `N & (N-1)` concretely: subtracting 1 from `N` flips the rightmost set bit to 0 and every bit *after* it (to its right) to 1. ANDing with the original `N` then cancels that rightmost set bit while leaving everything to its left untouched. Walk this on `12 = 1100` → `11 = 1011` → AND gives `1000 = 8`.
- The power-of-2 check is a direct *application* of the same formula: a power of 2 has **exactly one set bit** — so removing its (only) rightmost set bit must leave zero. Any number with more than one set bit will have something left over.
- Flag the `N > 0` guard explicitly: `0 & (0-1)` also evaluates to 0 in most languages' bit representations, but 0 is **not** a power of 2 — the guard exists specifically to rule this edge case out.

**Checkpoint (at 39 min)** — show of hands:
> *"Is `18` a power of 2? Walk the formula, don't just guess."*
> **Answer:** `18 = 10010`, `17 = 10001`. `10010 & 10001 = 10000 ≠ 0` → not a power of 2 (correctly — 18 isn't).

---

## ⚡ Activity 2 — Predict-the-Output: Power-of-2 Check Under Time Pressure (39–44 min)

**Format:** Predict-the-Output · **Exposes:** forgetting the `N > 0` guard, and applying the formula without actually checking the bit pattern (guessing from familiarity with small powers of 2 instead).

**Setup line (say this):**
> *"Four numbers, one formula, no calculators: `32`, `24`, `1`, `0`. For each, is it a power of 2? You must justify with the `N & (N-1)` result, not just 'I know 32 is a power of 2.'"*

**What students do:** `32 = 100000`, `31 = 011111`, AND = `0` → power of 2. `24 = 11000`, `23 = 10111`, AND = `10000 ≠ 0` → not a power of 2. `1 = 1`, `0 = 0`, AND = `0` → power of 2 (`2^0 = 1`, correctly). `0`: the guard `N > 0` fails immediately, so it's **not** a power of 2 by definition, regardless of what `0 & (-1)` would compute to.

**How to handle wrong answers:** If someone says `0` is a power of 2 "because the formula gives 0," that's exactly the edge case this activity targets — walk back to the checkpoint's `N > 0` guard and ask what property of 0 (no set bits at all, not exactly one) makes it fail the actual definition.

**Debrief line:**
> *"'Exactly one set bit' is stricter than 'the formula returns zero.' Zero has *no* set bits, so it accidentally satisfies the arithmetic without satisfying the definition — that's precisely why the algorithm's very first step is a plain `N > 0` check, before any bit trick runs at all."*

**Cut rule:** If short on time, drop `1` from the list (it's the least error-prone case) and keep `32`, `24`, and `0` — the `0` edge case is the load-bearing part of this activity.

---

## Slide Block C (44–54 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 21-38, 79-120: counting set bits, two approaches -->
Covers: Count Set Bits — Approach 1 (loop, check `N & 1`, right-shift, repeat until N is 0 — O(log₂ N) time) → Approach 2 (Brian Kernighan's trick: repeatedly apply `N = N & (N-1)` and count iterations until N is 0 — O(number of set bits) time) → both traced on `N = 13` (binary `1101`, 3 set bits).

**Beats to emphasise**

- Approach 1 runs once **per bit position** in N, whether that bit is 0 or 1 — that's why it's O(log₂ N) (proportional to the total number of bits, i.e. the size of N, not how many are actually set).
- Approach 2 runs once **per set bit**, because each `N & (N-1)` application removes exactly one set bit and nothing else — that's the same removal mechanic from Slide Block B, just looped until N hits 0.
- The direct payoff: for a **sparse** number (few 1s, e.g. `10000000`), Approach 2 finishes in 1 iteration where Approach 1 needs 8. For a **dense** number (all 1s, e.g. `11111111`), the two approaches do the *same* number of iterations — there's no free lunch when every bit is set.

**Checkpoint (at 54 min)** — cold-call:
> *"For `N = 8` (binary `1000`, one set bit), how many loop iterations does Approach 1 need? How many does Approach 2 need?"*
> **Answer:** Approach 1: 4 iterations (it walks all 4 bit positions before N becomes 0). Approach 2: 1 iteration (`1000 & 0111 = 0000` immediately).

---

## ⚡ Activity 3 — Spot the Bug: "Kernighan's Trick Is Always Faster" (54–59 min)

**Format:** Spot the Bug · **Exposes:** the over-generalization that `N & (N-1)` counting is unconditionally faster than the check-and-shift approach, when in fact its advantage depends entirely on how many bits are actually set.

**Setup line (say this):**
> *"A student tells you: 'Always use the `N & (N-1)` approach for counting set bits — it's strictly faster than checking every bit.' Is that a true statement? Test it on `N = 255` (binary `11111111`, all 8 bits set) before you answer."*

**What students do:** Trace both approaches for `N = 255`. Approach 1: 8 iterations (one per bit position, all the way down). Approach 2: also 8 iterations — every single `N & (N-1)` step removes exactly one set bit, and there are 8 of them to remove. Neither approach is faster here.

**How to handle wrong answers:** If a group insists Kernighan's approach "must" still be faster because it's the more advanced technique, have them count the actual loop iterations for both, side by side, rather than reasoning from the technique's reputation.

**Debrief line:**
> *"Kernighan's trick is O(number of set bits), not O(1) and not unconditionally faster than O(log₂ N). Its real advantage only shows up for sparse numbers — few 1s scattered in a wide number. For a dense number like `255`, the two approaches tie exactly. 'Advanced-sounding' and 'always faster' are not the same claim — check the actual bit pattern before you pick an approach."*

**Cut rule:** If very tight on time, skip the group trace and present `N = 255` as a worked example directly, stating both iteration counts — the core "no free lunch on dense numbers" lesson survives.

---

## Exit Ticket (59–65 min)

> On paper before anyone leaves: For `N = 40` (binary `101000`), (a) what does `N & (N-1)` give you, and what did it remove? (b) Is 40 a power of 2? (c) How many set bits does 40 have, and which approach would reach the answer in fewer iterations?
> **Answers:** (a) `40 = 101000`, `39 = 100111`, AND = `100000 = 32` — removed the rightmost set bit (the `8`'s place). (b) No — `N & (N-1) = 32 ≠ 0`, so more than one set bit remains. (c) 2 set bits (`101000`); Kernighan's approach reaches the answer in 2 iterations versus Approach 1's 6 (one per bit position up to bit 5) — Kernighan's is faster here because the number is sparse.

Scan responses on the way out. If most students still pick Approach 1 as "faster" for part (c) without checking the actual bit count, Activity 3's lesson needs a 2-minute recap at the start of Session 34.

**Homework:** re-derive `N & (N-1)`'s effect from scratch for a number of your own choosing, and use it to confirm whether that number is a power of 2. <!-- placement: inferred — no homework/practice-unit table exists for this course; this is a natural close, not a platform assignment -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The XOR-swap trick works safely in every situation | The three-line derivation looks clean and general | Naming the same-variable edge case explicitly in Slide Block A (swapping a variable with itself zeroes it out) |
| `N & (N-1)` removes the "leftmost" or "most significant" set bit | "Rightmost" and "leftmost" get swapped easily when reading bit diagrams left to right on screen | Tracing the mechanic explicitly: subtracting 1 only ever affects the rightmost set bit and the bits after it |
| `0` counts as a power of 2 because `0 & (0-1) == 0` | The arithmetic "passes" the core formula without the `N > 0` guard being visible in memory | Activity 2 — walking the `N > 0` guard as the actual first step of the algorithm, separate from the AND check |
| Brian Kernighan's `N & (N-1)` bit-counting approach is always faster than the check-and-shift approach | It's presented as the "optimized" version, and optimized things are assumed to always win | Activity 3 — tracing both approaches on `N = 255` (all bits set) and finding identical iteration counts |
| "Number of set bits" (Hamming weight) is the same thing as the number's value | Both are just "a number associated with N," and it's easy to conflate the two without a clear worked contrast | Slide Block C's own Hamming-weight framing — explicitly counting *how many* bits are 1, not *what* the bits represent as a value |

---

## Instructor Notes

- **This session's four techniques share one formula (`N & (N-1)`) wearing three different hats** (remove rightmost bit → power-of-2 check → Kernighan's counting) — say this explicitly early in Slide Block B, it's the single biggest pacing and retention win available in this session.
- **The deck itself is almost entirely repeated dry-run animation slides** (119 total, but only 4 distinct techniques) — do not attempt to "deliver every slide" at face value; deliver the technique, the formula, one worked example, and move on. The repeated slides are there for a slower self-paced video viewer, not for live-class pacing.
- **Activity 3 (dense vs. sparse numbers) is the highest-value catch in the session** — it's the only place students are forced to check a claim about performance against an actual trace, rather than trusting which technique "sounds" more advanced. Protect this over Activity 1 if time is short.
- **Have `N = 12`, `N = 40`, and `N = 255` worked out on scratch paper before class starts** — these are the numbers used in the activities and exit ticket, and you'll want to move through their bit patterns without hesitation live.
- **Classroom Quiz question bank does not exist yet for this course** — pull 5–6 MCQs from the platform live at the marked point.
