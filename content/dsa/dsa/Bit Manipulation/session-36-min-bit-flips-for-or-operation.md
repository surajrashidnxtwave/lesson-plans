# Session 36 — Min Bit Flips for OR Operation

**Duration** 45 min <!-- placement: inferred — standard length, single approach with a bit-by-bit rule table and one dry run --> · **Topic** Bit Manipulation — Minimum Bit Flips for OR Equation · **Prerequisite** Single Element 1 (Session 35)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Min Bit Flips for OR Operation | https://docs.google.com/presentation/d/1SSKEUExPi74OVPQqSas6-fe27EZLFFNSHJtb34f4UTE/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given `x`, `y`, `z`, find the minimum number of bit flips (in `x` and/or `y`) needed so that `x | y == z`. *(REMEMBERING)*
2. State the two-case rule, per bit position: if `z`'s bit is `1` and both `x` and `y`'s bits are `0`, one flip is needed; if `z`'s bit is `0`, every `1` among `x` and `y`'s bits at that position must flip (0, 1, or 2 flips). *(UNDERSTANDING)*
3. Trace the bit-by-bit algorithm across all three numbers, least-significant bit to most, accumulating the flip count. *(APPLYING)*
4. Explain why a `z`-bit of `1` never needs more than one flip, while a `z`-bit of `0` can need up to two. *(ANALYZING)*
5. State the algorithm's complexity: `O(max_bits)`, effectively `O(1)` for fixed-width integers; `O(1)` space. *(REMEMBERING)*

---

## Warm-Up Poll — Retrieval Practice on Session 35 (Single Element 1) (0–6 min)

Say: *"Six on yesterday's XOR-cancellation trick, then today we look at individual bits again — but comparing three numbers instead of canceling pairs."*

**Q1.** The optimal Single Element approach works by:
`A` Sorting the array first · `B` XOR-ing every element together · `C` Counting frequencies in a hashmap · `D` Comparing adjacent elements
→ **Answer:** B.

**Q2.** Why does XOR-ing the whole array correctly find the unpaired element?
`A` It doesn't, that's a coincidence · `B` Paired elements cancel to 0 via `a^a=0`, and `a^0=a` leaves the unpaired one · `C` XOR sorts the array internally · `D` It only works if the array is already sorted
→ **Answer:** B.

**Q3.** The XOR trick's precondition is:
`A` The array must be sorted · `B` Exactly one element has no pair · `C` All elements must be positive · `D` The array must have even length

**Q4.** Time and space complexity of the optimal Single Element approach?
`A` `O(n)` time, `O(n)` space · `B` `O(n)` time, `O(1)` space · `C` `O(n²)` time, `O(1)` space · `D` `O(log n)` time, `O(1)` space
→ **Answer:** B.

**Q5.** If an array had two elements without a pair instead of one, would the XOR trick still return one of them correctly?
`A` Yes, always · `B` No — it can produce a value that isn't either original number · `C` Yes, but only if they're adjacent · `D` Yes, but only for even numbers
→ **Answer:** B.

**Q6 (MSQ — pick all correct).** Which are true of XOR's properties used in Session 35?
`A` `a ^ a = 0` · `B` `a ^ 0 = a` · `C` XOR is commutative and associative, so order of operations doesn't matter · `D` XOR requires the array to be traversed twice

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"You have two numbers, `x` and `y`. Their bitwise OR is supposed to equal a third number, `z` — but right now it doesn't. What's the fewest single-bit edits, across `x` and `y` combined, to make that equation true?"*

Let students sit with it. Then:

> *"Yesterday you compared two numbers bit by bit with XOR. Today it's three numbers, and instead of checking 'do these agree,' you're checking 'does this OR result match the target' — bit by bit, independently, then adding up however many edits each position needs."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–16: Problem Statement, Example, Approach, Dry Run -->
Covers: problem statement (find minimum bit flips in `x` and/or `y` so `x | y == z`) → worked example (`x=2, y=3, z=6` → answer `2`) walked visually bit by bit, showing where `x|y` currently disagrees with `z` and which bits get flipped → approach: process bits from least significant to most significant; if `z`'s bit is `1` and both `x`,`y`'s bits are `0`, flip one bit; if `z`'s bit is `0`, flip every `1` among `x`,`y`'s bits at that position → dry run on `x=2 (010), y=3 (011), z=6 (110)`: bit 0 — `z=0`, `x₀+y₀=0+1=1` → 1 flip; bit 1 — `z=1`, `(x₁,y₁)=(1,1)`, already satisfies OR → 0 flips; bit 2 — `z=1`, `(x₂,y₂)=(0,0)` → 1 flip → total 2 flips.

**Beats to emphasise**

- State the two-case rule as one sentence each: *"if the target bit is 1 and neither input has it set, you owe exactly one flip to set one of them. If the target bit is 0, every 1 currently sitting in either input at that position has to go — that's 0, 1, or 2 flips depending on how many are set."*
- **Say explicitly why a `z`-bit of `1` never costs more than one flip:** OR only needs *one* of the two bits to be `1` to satisfy a target `1` — so even if both are currently `0`, flipping just one of them is enough; you never need to flip both.
- **Say explicitly why a `z`-bit of `0` can cost up to two flips:** OR requires *both* bits to be `0` to satisfy a target `0` — so every `1` present in either `x` or `y` at that position is a violation that must be individually flipped.

**Checkpoint (at 17 min)** — cold-call:
> *"Why can a `z`-bit of `0` require two flips, while a `z`-bit of `1` only ever requires at most one?"*
> **Answer:** A target of `1` is satisfied by *either* input bit being `1` — fixing one is always enough. A target of `0` demands *both* input bits be `0` — if both are currently `1`, both must be individually flipped.

---

## Slide Block B (17–25 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 17–44: Pseudocode, Complexity, Code, Logical Approach bit-truth-table walkthrough, additional worked Examples -->
Covers: pseudocode (`while x!=0 or y!=0 or z!=0: if z&1==1: if (x&1)==0 and (y&1)==0: count+=1; else: count += (x&1)+(y&1); shift all three right`) → complexity (`O(max_bits)`, effectively `O(1)` for 32-bit integers; `O(1)` space) → C++/Python code → logical approach — a full truth table over the four `(x-bit, y-bit)` combinations against both possible `z`-bit values, confirming the two-case rule bit-combination by bit-combination → three additional fully worked examples reinforcing the rule at larger bit-widths.

**Beats to emphasise**

- Walk the truth table explicitly: `(x,y)=(0,0)` vs `z=1` → 1 flip; `(x,y)=(0,1)` or `(1,0)` vs `z=1` → 0 flips (already satisfied); `(x,y)=(1,1)` vs `z=1` → 0 flips; then the `z=0` row: `(0,0)`→0 flips, `(0,1)` or `(1,0)`→1 flip, `(1,1)`→2 flips.
- Note the loop terminates once `x`, `y`, and `z` have all reached `0` — this naturally handles operands of different bit-lengths without special-casing.
- Complexity: `max_bits` is a small fixed constant (32 for standard integers), so despite the `while` loop, this is effectively constant time — same shape of argument as prior fixed-width bit-manipulation sessions.

**Checkpoint (at 25 min)** — cold-call:
> *"The loop condition checks `x != 0 || y != 0 || z != 0`, not just one of them. Why check all three, instead of just looping a fixed 32 times?"*
> **Answer:** Checking all three lets the loop stop as soon as every remaining bit in `x`, `y`, and `z` is `0` — avoiding unnecessary iterations once all meaningful bits have been processed, rather than always running the full fixed width.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Bit by Bit, Rule by Rule" (30–36 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can apply the correct rule (the `z=1` rule vs. the `z=0` rule) at each bit position without conflating them, rather than only having watched one worked example.

**Setup line (say this):**
> *"`x = 5 (101), y = 2 (010), z = 3 (011)`. Bit by bit from position 0: tell me which rule applies and how many flips it costs — before I confirm."*

Run **one bit position at a time**:

```
bit 0: x₀=1, y₀=0, z₀=1 → z=1 rule: is (x₀,y₀) both 0? No (x₀=1) → 0 flips, already satisfied.
bit 1: x₁=0, y₁=1, z₁=1 → z=1 rule: is (x₁,y₁) both 0? No (y₁=1) → 0 flips, already satisfied.
bit 2: x₂=1, y₂=0, z₂=0 → z=0 rule: flip every 1 among (x₂,y₂) → x₂=1 counts → 1 flip.
```

**How it surfaces:** At bit 2, ask before revealing: *"z's bit here is 0 — does that mean we check whether both are already 0 like the last two positions, or something different?"* Correct: something different — the `z=0` rule requires counting and flipping *every* 1 present, not checking for an all-zero match.

**Debrief line:**
> *"Total: 1 flip. The two rules aren't interchangeable — get the target bit's value first, and that alone tells you which rule applies for that position. Mixing them up is the single most common error in this problem."*

**Cut rule:** If running short, do only bit 2 (the `z=0` case) live — it's the rule students are more likely to misapply; the two `z=1` bits can be stated directly.

---

## ⚡ Activity 2 — Predict & Discuss: "Worst Case for Flips" (36–41 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students can reason about which bit pattern maximizes total flips, testing genuine understanding of the cost structure rather than just mechanical rule application.

**Setup line (say this):**
> *"For a single bit position, what's the worst possible combination of `x`-bit, `y`-bit, and `z`-bit — the one that forces the most flips at that position? And what's the maximum possible flips for one position?"*

**What students do:** Discuss for a minute, then share out.

**Answer:** The worst case is `z`-bit `= 0` with both `x`-bit and `y`-bit `= 1` — that forces 2 flips (both must be cleared). No single position can ever cost more than 2, since there are only two input bits to flip. A `z`-bit of `1` can never cost more than 1, since only one input bit needs to be set.

**How it surfaces:** Ask a follow-up: *"So what's the absolute maximum number of flips this whole algorithm could ever report, for 32-bit integers?"* Push toward: `64` — 2 flips per bit position, times 32 bit positions, if every single position happened to be the worst case simultaneously.

**Debrief line:**
> *"Knowing the worst-case shape of a problem — not just the average case — is often exactly the kind of check that catches an off-by-one or a mishandled branch before it ships. Here, if your count per bit position is ever computed as 3 or higher, something in your logic is wrong."*

**Cut rule:** If running short, state the worst-case answer directly and skip the open discussion.

---

## Exit Ticket (41–45 min)

> `x = 8 (1000), y = 3 (0011), z = 8 (1000)`. Trace bit by bit and report the minimum flips.
> **Answer:** bit 0: `x=0,y=1,z=0` → `z=0` rule → 1 flip (clear y's 1). bit 1: `x=0,y=1,z=0` → `z=0` rule → 1 flip. bit 2: `x=0,y=0,z=0` → `z=0` rule → 0 flips. bit 3: `x=1,y=0,z=1` → `z=1` rule, not both zero → 0 flips. **Total: 2 flips.** <!-- placement: inferred exit-ticket values, built to exercise both rules across four bit positions -->

**Homework:** trace `x = 11, y = 6, z = 5` bit by bit, listing the rule applied and flip count at each position. <!-- placement: inferred — no homework/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The same rule applies regardless of whether `z`'s bit is 0 or 1 | Both cases involve "checking bits and counting flips," which can blur together | Activity 1 — deliberately switching from a `z=1` bit to a `z=0` bit mid-trace, forcing an explicit rule-switch |
| A `z`-bit of 0 can require up to one flip, matching the `z=1` case | Symmetric-sounding phrasing ("target bit is X") invites assuming symmetric costs | Activity 2 — explicitly deriving that a `z=0` position can cost up to 2 flips, while `z=1` never exceeds 1 |
| The loop must always run a fixed 32 iterations | Prior fixed-width bit sessions used a hardcoded 32-iteration frame | Slide Block B's checkpoint — the loop exits as soon as `x`, `y`, and `z` are all simultaneously 0, which can happen before 32 iterations |
| This problem is solved the same way as yesterday's XOR-cancellation problem | Both are described as "bit manipulation with a flip count" | Contrast explicitly: yesterday compared two numbers for equality via XOR; today evaluates a three-number OR-equation bit by bit, with two distinct rules depending on the target bit |
| Flip count only ever involves `x`, never `y`, or vice versa | The problem statement says "flip bits in `x` and `y`" without specifying which one each time | State plainly: the algorithm never needs to know *which* of `x` or `y` gets flipped, only *how many* flips are needed — for a `z=0` violation with both bits set, either one could be chosen, but both still count |

---

## Instructor Notes

- **The two-rule split (target-bit-1 vs target-bit-0) is this session's entire difficulty — keep returning to it explicitly.** Once a student can state both rules from memory without looking, the rest of the session (pseudocode, complexity, code) is mechanical.
- **Activity 2's worst-case reasoning is worth protecting even under time pressure** — it's a rare moment where students derive a bound themselves rather than being told it, and it reinforces genuine understanding over rule memorization.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- **Bridge to Session 37 at the close:** "Today's bit-by-bit rule table gave us Boolean logic per position. Next session moves to ranges of numbers and cumulative XOR — a different shape of bit problem, so don't expect today's two-rule table to reappear directly."
