# Session 34 — Minimum Bit Flips to Convert a Number

**Duration** 45 min · **Topic** Bit Manipulation — Minimum Bit Flips (source → target) · **Prerequisite** Session 33 (Bit Manipulation Techniques 2)
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Minimum Bit Flips to Convert a Number | https://docs.google.com/presentation/d/1FWrX6ioQQ-C2Vj4A9JNVk3hSD0WILq_EHkIMXwhqxFQ/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given `source` and `target`, find the minimum number of single-bit flips needed to turn `source` into `target`. *(REMEMBERING)*
2. Explain why `source ^ target` identifies exactly the bit positions where the two numbers differ. *(UNDERSTANDING)*
3. Count the set bits in the XOR result using the `N & (N-1)` trick from Session 33 to get the final flip count. *(APPLYING)*
4. Trace the full algorithm by hand for a given source/target pair. *(APPLYING)*
5. State the algorithm's complexity: O(number of set bits) time, O(1) space. *(REMEMBERING)*

<!-- placement: inferred from the Key Takeaways slide (27), which restates exactly this four-point summary. -->

---

## Warm-Up Poll — Retrieval Practice on Session 33 (0–6 min)

> Retrieval practice on **Bit Manipulation Techniques 2**. Solo answers, no discussion yet.

**Q1.** The XOR-based swap trick relies on which identity being applied twice?
`A` `a & a = a` · `B` `a ^ a = 0` and `a ^ 0 = a` · `C` `a | a = a` · `D` `a << 1 = 2a`
→ *Read:* Answer B.

**Q2.** `N & (N - 1)` does what to N's bit pattern?
`A` Sets every bit to 1 · `B` Removes (clears) N's rightmost set bit · `C` Reverses all the bits · `D` Doubles N
→ *Read:* Answer B.

**Q3.** The power-of-2 check requires `N > 0` in addition to `N & (N-1) == 0`. Why?
`A` No real reason, it's a style choice · `B` Because `0 & (0-1)` also equals 0, but 0 has zero set bits, not exactly one, so it isn't a power of 2 · `C` Because negative numbers break the CPU · `D` Because `N-1` is undefined for positive N
→ *Read:* Answer B.

**Q4.** Approach 1 for counting set bits (check-and-shift) has what time complexity?
`A` O(1) · `B` O(number of set bits) · `C` O(log₂ N) — proportional to N's total bit-width · `D` O(N)
→ *Read:* Answer C.

**Q5.** Brian Kernighan's `N & (N-1)` approach for counting set bits has what time complexity?
`A` O(1) · `B` O(number of set bits actually present in N) · `C` O(log₂ N) always, same as Approach 1 · `D` O(N²)
→ *Read:* Answer B.

**Q6.** *(MSQ — pick 2)* For `N = 255` (`11111111`, all 8 bits set), which are true?
`A` Approach 1 takes 8 iterations · `B` Kernighan's approach takes 8 iterations too · `C` Kernighan's approach is strictly faster here · `D` Both approaches tie in iteration count
→ *Read:* A, B, and D — Kernighan's advantage disappears on dense (many-1s) numbers.

**Running it** — poll tool, ~40 s per question. Total 6 min.

---

## Hook (6–9 min)

Write `source = 7` and `target = 5` on the board. Ask: *"Minimum number of single bits I need to flip in 7 to turn it into 5 — not steps, not operations, just flips. Guess before I show you the binary."*

Reveal `7 = 0111`, `5 = 0101`. Say: *"Only one bit differs — the second one from the right. One flip. Today's whole session is one clean idea: find exactly which bits differ, then count them — and you already have both tools for that from the last two sessions."*

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 4-18: problem statement, examples, logical approach, dry run -->
Covers: Problem Statement → Example 1 (`source=7, target=5` → 1 flip) → Example 2 (`source=3, target=15` → 2 flips) → Logical Approach (`source ^ target`, then count the 1s in the result) → full Dry Run (`source=3, target=15` → XOR gives `1100`, count set bits via `N & (N-1)` → 2).

**Beats to emphasise**

- **Why XOR:** `source ^ target` is 1 at exactly the positions where the two numbers disagree — that's the definition of XOR from Session 31, applied here for the first time to *comparison* rather than combination. Say explicitly: *"A 1 in the XOR result means 'these two numbers need a flip here.' A 0 means 'already matching, leave it alone.'"*
- **Why count set bits, not anything else:** each differing bit needs exactly one flip to fix, so the total flip count is just the count of 1s in the XOR result — this is a direct reuse of Session 33's set-bit-counting techniques, not a new counting method.
- Walk the dry run's own numbers (`3, 15`) exactly as animated: XOR → `1100` (12), then apply `N & (N-1)` twice (`1100 & 1011 = 1000`, then `1000 & 0111 = 0000`) to land on count = 2.

**Checkpoint (at 19 min)** — cold-call:
> *"In one sentence: what does the XOR step actually give you, and what does the bit-count step give you?"*
> **Answer:** XOR gives you the positions that differ (as a bit pattern). Counting set bits in that pattern gives you how many positions differ — which is the minimum flip count.

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: `source = 6, target = 9` (19–24 min)

**Format:** Dry-Run Relay · **Exposes:** skipping the XOR step and trying to reason about flips by eyeballing the two numbers directly, or miscounting bits without the `N & (N-1)` method.

**Setup line (say this):**
> *"New pair, not from the slides — `source = 6`, `target = 9`. Row by row, out loud: first the XOR, then the flip count. I only write what you say."*

**What students do:** Relay through: `6 = 0110`, `9 = 1001`. `6 ^ 9 = 1111` (15). Count set bits via `N & (N-1)`: `1111 & 1110 = 1110` (count=1) → `1110 & 1101 = 1100` (count=2) → `1100 & 1011 = 1000` (count=3) → `1000 & 0111 = 0000` (count=4). Minimum flips = **4**.

**How to handle wrong answers:** If someone tries to count flips by comparing `6` and `9` as decimal numbers directly ("9 is 3 more than 6, so 3 flips?"), redirect immediately to the binary representations — decimal difference has no relationship to bit-flip count, which is exactly what this activity is built to surface.

**Debrief line:**
> *"Every one of these problems reduces to the same two steps you just ran: XOR to find where they disagree, then count. There's no shortcut that skips XOR and works from the decimal values directly — the moment you're tempted to eyeball it, go back to binary."*

**Cut rule:** If running short, do only the XOR step live and state the final count directly — the XOR-first habit is the higher-value half of this activity.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–37 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 19-27: pseudocode, complexity analysis, code implementation, key takeaways -->
Covers: Pseudocode (`n = source ^ target; count = 0; while(n != 0){ count += 1; n = n & (n-1) }; return count`) → Time Complexity O(number of set bits) → Space Complexity O(1) → C++/Python code implementation.

**Beats to emphasise**

- Point out that this pseudocode is **literally Session 33's Kernighan set-bit-counting loop**, with one line prepended (`n = source ^ target`). Nothing else about the counting loop has changed.
- Space complexity is O(1) because the algorithm only ever tracks two variables (`n` and `count`) regardless of how large `source` and `target` are — no arrays, no strings.
- Run the Python code live for `source=3, target=15` and confirm the output is 2.

**Checkpoint (at 37 min)** — show of hands:
> *"True or false: this algorithm's time complexity depends on the numeric size of `source` and `target`, not on how many bits actually differ."*
> **Answer:** False. It depends on the number of **set bits in the XOR result** — i.e., how many positions actually differ — not on how large the numbers themselves are.

---

## ⚡ Activity 2 — Spot the Bug: A Solution That Skips XOR (37–42 min)

**Format:** Spot the Bug · **Exposes:** the belief that you can get the flip count by counting set bits in `source` and `target` *separately* and combining them somehow, instead of XOR-ing first.

**Setup line (say this):**
> *"A classmate submits this solution: 'count the set bits in `source`, count the set bits in `target`, and the answer is the difference between those two counts.' Test it against `source=3` (`0011`, 2 set bits) and `target=15` (`1111`, 4 set bits) — the deck's own answer for this pair is 2 flips. Does the classmate's method agree?"*

**What students do:** Compute the classmate's method: `|4 - 2| = 2`. It happens to match here — which is the trap. Then test it against a case where it clearly fails: `source=7` (`0111`, 3 set bits), `target=8` (`1000`, 1 set bit). Classmate's method: `|1 - 3| = 2`. Correct method: `7 ^ 8 = 1111` (15), which has **4** set bits → 4 flips, not 2.

**How to handle wrong answers:** If students assume the first test case "proves" the classmate's method works, push them to run the second case before concluding anything — one matching example is not proof of a correct algorithm, especially in a topic where coincidental matches are common.

**Debrief line:**
> *"Comparing bit *counts* throws away exactly the information you need — *which* positions are set, not how many. Two numbers can have wildly different set bits in completely different places and still coincidentally produce a matching difference. XOR is the only step that actually tells you which positions disagree — there's no shortcut around it."*

**Cut rule:** If short on time, present only the second (failing) test case directly rather than building up through the first — the failure is the whole point.

---

## Exit Ticket (42–45 min)

> On paper before anyone leaves: `source = 10, target = 3`. What's the minimum number of bit flips, and what are the two steps you used to get there?
> **Answer:** `10 = 1010`, `3 = 0011`. `10 ^ 3 = 1001` (9), which has 2 set bits. **Minimum flips = 2.** Steps: XOR to find differing positions, then count set bits in the result.

Scan on the way out. If students try to answer without writing out the XOR step explicitly, that's the signal Activity 2's lesson didn't fully land — reopen with a 90-second recap at the start of Session 35.

**Homework:** re-derive today's dry run for a source/target pair of your own choosing, without looking at the slides. <!-- placement: inferred — no homework/practice-unit table exists for this course; this is a natural close, not a platform assignment -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Minimum flips = (set bits in source) − (set bits in target), or some other combination of separate counts | It reuses a familiar-feeling operation (subtraction of counts) instead of introducing XOR | Activity 2 — showing a case where the counts' difference coincidentally matches, then a case where it clearly doesn't |
| You can estimate flip count from the decimal difference between source and target | Bigger decimal gap "feels" like it should mean more flips | Activity 1 — showing `6→9` (decimal gap of 3) needs 4 flips, breaking any decimal-gap intuition |
| A flip only means turning a 1 into a 0 | "Flip" colloquially suggests removing something, not adding it | Restating explicitly that XOR marks *any* disagreement, whether source has a 0 where target has a 1, or the reverse |
| Time complexity is proportional to the size (value) of source and target | Bigger numbers generally feel like they should take longer to process | Slide Block B checkpoint — tying complexity to the number of set bits in the XOR result specifically, not to the numbers' magnitude |
| You need a separate counting method for this problem, distinct from Session 33's techniques | Each session's problem is presented as new, so the counting step feels like new content too | Slide Block B — pointing out the pseudocode is Session 33's Kernighan loop with one prepended line |

---

## Instructor Notes

- **This is a clean, single-idea session** — XOR to find differences, then count. Resist introducing extra complexity; the deck itself only shows one approach (no brute-force/optimal split), and that's appropriate given the compactness of the idea.
- **Activity 2 is the highest-value moment in this session** because the "coincidental match on the first test case" pattern is a genuinely common trap in how students validate their own solutions — it's worth the full 5 minutes even under time pressure.
- **This session is the first to explicitly chain two previous sessions' techniques together** (Session 31's XOR + Session 33's Kernighan counting) — say so out loud, it reinforces that bit manipulation techniques compose rather than existing as isolated tricks.
- **Have `source=6, target=9` and `source=7, target=8` worked out on scratch paper** before class — these are the numbers used in Activity 1 and Activity 2 respectively.
- **Classroom Quiz question bank does not exist yet for this course** — pull 5–6 MCQs from the platform live at the marked point.
