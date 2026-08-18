# Session 37 — Bitwise XOR For a Given Range

**Duration** 50 min <!-- placement: inferred — above default because the optimal approach's `n % 4` pattern derivation is the densest single idea in the Bit Manipulation block and needs the extra time to land --> · **Topic** Bit Manipulation — XOR Over a Range · **Prerequisite** Min Bit Flips for OR Operation (Session 36)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Bitwise XOR For a Given Range | https://docs.google.com/presentation/d/17bNkI-vHKfJlll-Rasw2uhWcPEQNGHYyrQH3tM889gI/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given `left` and `right`, compute the XOR of every integer in the inclusive range `[left, right]`. *(REMEMBERING)*
2. Explain the brute-force approach — iterate the range, XOR-accumulating as you go — and its `O(right - left)` cost. *(UNDERSTANDING)*
3. Explain the identity `XOR(left…right) = XOR(1…right) ^ XOR(1…left-1)`, and why prefix-XOR cancellation makes this valid. *(UNDERSTANDING)*
4. State and apply the `n % 4` pattern for computing `XOR(1…n)` in constant time (remainder 0 → `n`; 1 → `1`; 2 → `n+1`; 3 → `0`). *(APPLYING)*
5. Trace the optimal approach end-to-end on a given `left, right` pair, and state its complexity: `O(1)` time, `O(1)` space, versus the brute force's `O(right - left)` time. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 36 (Min Bit Flips for OR Operation) (0–6 min)

Say: *"Six on yesterday's bit-by-bit OR rule table, then we shift from checking single bit positions to XOR-ing an entire numeric range."*

**Q1.** In the Min Bit Flips for OR problem, if `z`'s bit is `1` and both `x`,`y`'s bits are `0`, the flip cost at that position is:
`A` 0 · `B` 1 · `C` 2 · `D` Undefined
→ **Answer:** B.

**Q2.** If `z`'s bit is `0`, the flip cost at that position equals:
`A` Always 0 · `B` Always 1 · `C` The sum of `x`'s bit and `y`'s bit at that position · `D` Always 2`
→ **Answer:** C.

**Q3.** What is the maximum possible flip cost for a single bit position?
`A` 1 · `B` 2 · `C` 3 · `D` Unbounded

**Q4.** The loop in that algorithm terminates when:
`A` It has run exactly 32 times · `B` `x`, `y`, and `z` have all become 0 · `C` `z` alone becomes 0 · `D` It never terminates
→ **Answer:** B.

**Q5.** Time complexity of the Min Bit Flips for OR algorithm?
`A` `O(max_bits)`, effectively `O(1)` for fixed-width integers · `B` `O(n log n)` · `C` `O(n²)` · `D` `O(log n)`
→ **Answer:** A.

**Q6 (MSQ — pick all correct).** Which are true of that session's rule table?
`A` A `z`-bit of 1 never costs more than 1 flip · `B` A `z`-bit of 0 can cost up to 2 flips · `C` The same rule applies regardless of `z`'s bit value · `D` The rule depends entirely on what `z`'s bit is at that position

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"XOR every integer from 5 to 10, one by one — that's not hard for six numbers. Now do it for every integer from 5 to 10 million. Same six-number pattern, or is there a shortcut?"*

Let students react. Then:

> *"There's a genuine shortcut — and it depends on an idea you haven't used yet: XOR-ing from 1 up to some number `n` follows a repeating pattern based on `n`'s remainder when divided by 4. Once you know that pattern, XOR over *any* range collapses to two lookups and one XOR — regardless of how wide the range is."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–22: Problem Statement, Examples, Brute Force Approach, Dry Run, Pseudocode, Complexity, Code -->
Covers: problem statement (XOR every integer in `[left, right]` inclusive) → Example 1 (`left=5, right=10` → `15`) → Example 2 (`left=7, right=13` → `6`) → brute force: initialize `ans=0`, iterate `i` from `left` to `right`, `ans = ans ^ i` → dry run on `[5,10]`: `0^5=5 → 5^6=3 → 3^7=4 → 4^8=12 → 12^9=5 → 5^10=15` → pseudocode → complexity (`O(right - left)` time — one iteration per number in range; `O(1)` space) → C++/Python code.

**Beats to emphasise**

- State the brute force in one line: *"start at 0, XOR in every number from `left` to `right` in order — nothing clever, just a running accumulator, exactly like Session 35's XOR-cancellation loop but over a contiguous range instead of an array."*
- **Say explicitly what's expensive about it:** for a range like `[5, 10,000,000]`, this approach genuinely performs ten million XOR operations — the range's *width*, not its content, drives the cost.
- Complexity is entirely proportional to the range's width, `right - left + 1` — not to the size of the numbers themselves.

**Checkpoint (at 18 min)** — cold-call:
> *"What does the brute-force approach's cost actually scale with — the size of `left` and `right` as numbers, or something else?"*
> **Answer:** The *width* of the range, `right - left + 1` — not the numeric magnitude of `left` or `right` individually. A range from 1 to 10 costs the same as a range from 999,991 to 1,000,000.

---

## Slide Block B (18–30 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 23–41: Optimal Approach, full Dry Run, Pseudocode, Complexity, Code, Logical Approach derivation (slides 69–93 mirror this with the n%4 pattern's algebraic derivation) -->
Covers: optimal approach — use the identity `XOR(left…right) = XOR(1…right) ^ XOR(1…left-1)`, computed via a helper `xorOnetoN(n)` that returns `n` if `n%4==0`, `1` if `n%4==1`, `n+1` if `n%4==2`, `0` if `n%4==3` → full dry run on `left=5, right=10`: `xorOnetoN(10)`: `10%4=2` → return `11`; `xorOnetoN(4)`: `4%4=0` → return `4`; combine `11 ^ 4 = 15` → algebraic derivation of the `n%4` pattern itself, built up recursively as `f(i) = f(i-1) ^ i` for `f(1)` through `f(10)`, observing the pattern repeats every four consecutive values of `n` → pseudocode → complexity (`O(1)` time — two constant-time helper calls plus one XOR; `O(1)` space) → C++/Python code.

**Beats to emphasise**

- **Say the identity as one sentence, this is the whole session:** *"XOR-ing 1 through `left-1` and then XOR-ing that same prefix again as part of `1` through `right` makes the shared prefix cancel out completely — via `a^a=0` — leaving exactly the XOR of `left` through `right`."* This is a direct reuse of Session 35's pairwise-cancellation idea, just applied to a prefix range instead of individual array pairs.
- **Walk the `n%4` pattern derivation carefully, using the deck's own recursive buildup** (`f(1)=1, f(2)=3, f(3)=0, f(4)=4, f(5)=1, f(6)=7, f(7)=0, f(8)=8, ...`) — the pattern of `{n, 1, n+1, 0}` repeating every four values is something students should see emerge from the recursion, not just memorize as a rule.
- Contrast complexity directly: brute force is `O(right-left)`; the optimal approach is `O(1)` regardless of range width, because `xorOnetoN` never loops — it only checks a remainder and returns a constant-time result.

**Checkpoint (at 30 min)** — cold-call:
> *"Why does XOR-ing `1` through `right` and `1` through `left-1` together correctly cancel down to just `left` through `right`?"*
> **Answer:** Both prefixes share the identical sub-range `1` through `left-1`. XOR-ing something with itself cancels to `0` (`a^a=0`), so that shared portion disappears entirely, leaving only the part that `1…right` had but `1…left-1` didn't — which is exactly `left` through `right`.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Two Lookups, One XOR" (35–41 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can apply the `n%4` pattern correctly on fresh numbers, rather than only having watched the deck's `n=10` and `n=4` examples.

**Setup line (say this):**
> *"`left = 8, right = 15`. First, compute `xorOnetoN(15)` and `xorOnetoN(7)` using the `n%4` pattern — tell me the remainder and the result for each, before I confirm. Then combine them."*

Run **step by step**:

```
xorOnetoN(15): 15 % 4 = 3  → pattern says remainder 3 → return 0
xorOnetoN(7):   7 % 4 = 3  → pattern says remainder 3 → return 0
XOR(8...15) = xorOnetoN(15) ^ xorOnetoN(7) = 0 ^ 0 = 0
```

**How it surfaces:** Before revealing the combination, ask: *"Both helper calls returned 0 — does that mean something went wrong, or is a zero answer possible here?"* Correct: a zero answer is entirely possible and not a sign of error — it simply means the range `8` through `15` (8 consecutive integers) happens to XOR to zero.

**Debrief line:**
> *"Two constant-time lookups, one XOR — done, regardless of whether the range was 8 numbers wide or 8 million. That's the entire point of moving from brute force to this identity."*

**Cut rule:** If running short, do only `xorOnetoN(15)` live and state `xorOnetoN(7)` and the final combination directly — one correctly-applied remainder case is enough to confirm the pattern.

---

## ⚡ Activity 2 — Predict & Discuss: "Why Does the Pattern Repeat Every 4?" (41–47 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students have genuine intuition for *why* the `n%4` cycle exists, rather than just memorizing the four-case table.

**Setup line (say this):**
> *"Look at the last two bits of any four consecutive integers — say, 4, 5, 6, 7 in binary: `100, 101, 110, 111`. What happens if you XOR all four together, and why might that explain why the pattern resets every 4 numbers?"*

**What students do:** Discuss for a minute, then share out — computing `4^5^6^7` in binary and noticing the lowest two bits cycle through all four combinations (`00, 01, 10, 11`) exactly once every four consecutive integers, while higher bits stay constant across the group of four.

**Answer:** Every group of 4 consecutive integers has lowest two bits that are a permutation of `00, 01, 10, 11` — XOR-ing all four combinations of two bits together always cancels to `00` for those two bits, while the higher, unchanging bits combine predictably. This is *why* `f(n)` cycles with period 4 — it's a direct consequence of how binary counting rolls over every 4 values, not an arbitrary rule to memorize.

**How it surfaces:** Ask a follow-up: *"So is `n%4` special because of anything about XOR specifically, or because of how binary counting works in general?"* Push toward: it's about binary counting — the lowest 2 bits of any 4 consecutive integers always cycle through all 4 possible 2-bit combinations, and that's a property of counting in binary, independent of XOR itself.

**Debrief line:**
> *"A pattern that looks like 'a rule to memorize' usually has a structural reason underneath it. Here, it's just binary counting's own rollover behavior — XOR is just the operation that happens to expose it cleanly."*

**Cut rule:** If running short, state the structural reason directly and skip the open discussion.

---

## Exit Ticket (47–50 min)

> `left = 11, right = 20`. Using the optimal approach, compute `xorOnetoN(20)`, `xorOnetoN(10)`, and the final answer.
> **Answer:** `xorOnetoN(20)`: `20 % 4 = 0` → return `20`. `xorOnetoN(10)`: `10 % 4 = 2` → return `11`. `XOR(11…20) = 20 ^ 11 = 10111 (in binary: 10100 ^ 01011 = 11111 = 31)`. Final answer: `31`. <!-- placement: inferred exit-ticket range, built to exercise both a remainder-0 and a remainder-2 case in the same computation -->

**Homework:** compute `XOR(23, 40)` using the `n%4` pattern, showing both helper-function calls and the final combination. <!-- placement: inferred — no homework/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| `XOR(left…right)` can be computed as `xorOnetoN(right) - xorOnetoN(left)` (subtraction instead of XOR) | The identity superficially resembles a prefix-sum-style range formula, where subtraction is standard | State plainly, and show with a worked example: prefix cancellation for XOR requires XOR-ing the two prefixes together (since `a^a=0`), not subtracting — subtraction doesn't have the matching cancellation property |
| The `n%4` pattern is an arbitrary rule to be memorized | It's introduced as a four-case lookup table without derivation | Activity 2 — deriving the pattern from how the lowest two bits of any four consecutive integers cycle through all combinations |
| A result of `0` from `xorOnetoN` or from the final range XOR indicates an error | Zero often reads as "nothing happened" or "a bug," especially after a multi-step calculation | Activity 1 — showing a genuine, correct zero result and confirming it's a valid answer rather than a sign of failure |
| The optimal approach only works for ranges that start at 1 | The helper function is explicitly named `xorOnetoN`, which can read as "only for ranges from 1" | Slide Block B — pointing out `xorOnetoN` is always called on `right` and `left-1` as an internal step, but the *range itself* can start anywhere; the two-call combination is what generalizes it to arbitrary `left` |
| This problem's optimal solution is unrelated to Session 35's XOR-cancellation trick | Different-sounding problem framing ("range" vs "array") | Explicitly connect: both rely on the identical algebraic fact, `a^a=0` — Session 35 canceled paired array elements; today cancels a shared numeric prefix |

---

## Instructor Notes

- **Activity 2's structural derivation is this session's highest-value five minutes** — resist the urge to skip it under time pressure. A student who can explain *why* the pattern has period 4 has understood something durable; a student who only memorized the four-case table will forget it by next week.
- **The subtraction-vs-XOR misconception is worth actively watching for**, since it's the most natural-looking wrong answer a student familiar with prefix-sum techniques (from arrays or ranges elsewhere) would produce.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- **This closes the Bit Manipulation block.** If time allows, briefly recap the block's throughline at the end: Sessions 29–33 built the core toolkit (binary conversion, bitwise operators, set-bit techniques); Sessions 34–37 applied that toolkit to four distinct problem shapes (bit-flip counting, XOR-cancellation, bit-by-bit rule tables, and prefix-XOR range identities) — the tools stayed the same, only what they were pointed at changed.
