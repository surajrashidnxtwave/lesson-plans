# Session 31 — Bitwise Operators

**Duration** 55 min · **Topic** Bit Manipulation — AND, OR, XOR, NOT, Shifts · **Prerequisite** Session 30 (Code for Binary Conversion)
**Session type** Concept lecture

<!-- placement: inferred — 55 min, not the 45-min default. This session introduces six distinct operators plus real-world context plus INT_MAX/INT_MIN, and every later Bit Manipulation session (32 onward) depends on fluency with all six — under-teaching this one has compounding cost. -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Bitwise Operators | https://docs.google.com/presentation/d/1r3lgW5W1n5JPdDWsRypWIFRPhCqCphmAq205XQhIeVU/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State what AND, OR, XOR, and NOT each do to a single pair (or single value) of bits, using their truth tables. *(REMEMBERING)*
2. Compute the result of AND, OR, XOR, and NOT on two given binary numbers by hand. *(APPLYING)*
3. Explain left shift and right shift as multiplication/division by powers of 2 (`n << k = n * 2^k`, `n >> k = n / 2^k`) and predict their output. *(APPLYING)*
4. Identify at least two real-world uses of bitwise operations (e.g., image compression, cryptography) and state why bitwise operations are faster than arithmetic ones. *(UNDERSTANDING)*
5. Explain why `~5` prints as `-6` in code rather than the "obvious" flipped-bits value. *(ANALYZING)*

<!-- placement: inferred from the Key Takeaways slide (55) and the deck's own C++ output slide (53), which is the only slide that surfaces the NOT/negative-number subtlety. -->

---

## Warm-Up Poll — Retrieval Practice on Session 30 (0–6 min)

> Retrieval practice on **Code for Binary Conversion**. Solo answers, no discussion yet.

**Q1.** Time complexity of `decimal_to_binary`?
`A` O(1) · `B` O(n) · `C` O(log₂ n) · `D` O(n log n)
→ *Read:* Answer C.

**Q2.** Time complexity of `binary_to_decimal`, where n is the length of the input string?
`A` O(1) · `B` O(n) · `C` O(log₂ n) · `D` O(n²)
→ *Read:* Answer B.

**Q3.** Space complexity of `binary_to_decimal`?
`A` O(n) · `B` O(log₂ n) · `C` O(1) · `D` O(n²)
→ *Read:* Answer C — only fixed variables, regardless of string length.

**Q4.** According to the deck's own pseudocode (no zero-guard), what does `decimal_to_binary(0)` actually return?
`A` `"0"` · `B` `""` (empty string) · `C` An error · `D` `"00"`
→ *Read:* Answer B. If this trips people up, that's expected — it's the whole point of yesterday's Spot-the-Bug activity, revisit it in 30 seconds if the miss rate is high.

**Q5.** Why does `decimal_to_binary`'s output string need to be reversed at the end, but `binary_to_decimal`'s loop doesn't need any reversing?
`A` Pure convention, no real reason · `B` Decimal-to-binary collects digits LSB-first; binary-to-decimal reads the string directly from its last character (already LSB) toward the first · `C` Because Python strings are backwards · `D` They both actually need reversing
→ *Read:* Answer B.

**Q6.** *(MSQ — pick 2)* For `n = 100`, which loop counts are correct?
`A` `decimal_to_binary`'s while-loop runs about 7 times · `B` `decimal_to_binary`'s while-loop runs 100 times · `C` `binary_to_decimal`'s for-loop on `"1100100"` runs 7 times · `D` `binary_to_decimal`'s for-loop runs once
→ *Read:* A and C.

**Running it** — poll tool, ~40 s per question. Total 6 min.

---

## Hook (6–9 min)

Write on the board: `n * 2` and `n << 1`. Ask: *"Same result, for any positive integer n. So why would anyone write the second one instead of the first?"*

Let a few guesses land, then say: *"Bitwise operations run directly on the CPU's circuitry — no multiplication logic involved, just a wire shift. They're the fastest operations a processor has. That speed is why they show up everywhere from image compression to encryption to how your phone stores a Boolean flag in a single bit instead of a whole byte. Today you learn the six operators that make all of that possible."*

---

## Slide Block A (9–20 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 4-23: intro, why bit manipulation, real-life use cases, bit & byte, AND, OR -->
Covers: Introduction to bit manipulation → Why Bit Manipulation (efficiency, memory optimization, fine-grained control) → Real Life Use Cases (image compression, cryptography) → Bit & Byte → Bitwise AND (`&`) with truth table and worked example (`0101 & 0011 = 0001`) → Bitwise OR (`|`) with truth table and worked example (`0101 | 0011 = 0111`).

**Beats to emphasise**

- Land the three reasons for bit manipulation as a memorable trio: **efficiency, memory, control.** These come back verbatim in the Key Takeaways slide at the end.
- On the truth tables: read AND as "both must be 1" and OR as "at least one must be 1" — say those two phrases explicitly, they're the cleanest possible distinction and it's the one students blur most.
- Walk the deck's own worked examples (`0101 & 0011`, `0101 | 0011`) bit by bit, column by column, exactly as the slides animate them.

**Checkpoint (at 20 min)** — show of hands:
> *"`1010 & 0110` — what's bit position 1 (second from the right) in the result?"*
> **Answer:** Both operands have `1` there, so AND gives `1`. (Full result: `0010`.)

---

## ⚡ Activity 1 — Real-World Callout: Where Have You Already Seen This? (20–25 min)

**Format:** Real-World Callout · **Exposes:** the belief that bitwise operators are an abstract classroom-only tool with no connection to software students actually use.

**Setup line (say this):**
> *"Thirty seconds. Anywhere you've seen a system store several yes/no flags packed into one number, or combine settings, or mask something out — shout it out. I'll write down what you say."*

**What students do:** Call out examples. Push toward concrete ones if the room stalls: file permissions (`rwx` in Unix, e.g. `chmod 755`), RGB color values packed into a single 24/32-bit integer, checkbox settings stored as a bitmask, network subnet masks. Write up to 6 on the board.

**How it surfaces:** For 2 of the examples, push once: *"Which operator would you use — AND, OR, or XOR — and why?"* e.g., checking if a permission bit is set → AND; combining two sets of flags → OR.

**Debrief line:**
> *"Every one of those systems is doing exactly what you just learned — packing multiple true/false answers into the bits of a single number, and using AND/OR to read or combine them. This isn't a toy topic, it's how real systems save memory and stay fast."*

**Cut rule:** If running late, take 3 callouts instead of 6 and skip the "which operator" push — keep the debrief line, it's the payoff.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (30–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 24-54: XOR, NOT, left shift, right shift, code + output, applications, INT_MAX/INT_MIN -->
Covers: Bitwise XOR (`^`) with truth table, worked example, and the odd/even-count-of-1s rule for multi-value XOR → Bitwise NOT (`~`) with truth table and worked example → Left Shift (`<<`, multiply by 2^k) → Right Shift (`>>`, divide by 2^k) → full code implementation and its printed output → Applications (memory optimization, data compression, cryptography, algorithms/puzzles, low-level hardware control) → INT_MAX / INT_MIN as 32-bit sign-bit boundaries.

**Beats to emphasise**

- XOR: "exactly one must be 1, not both." Show the deck's 3-input XOR truth table (slide 31) and state the general rule out loud: *"XOR of several bits is 1 if the count of 1s among them is odd, 0 if even."*
- NOT: run the deck's own C++ output live — `~0101` on paper looks like `1010` (which would be 10), but the code prints `-6`. Don't resolve this yet — flag it explicitly: *"Hold that thought, we're about to spend an activity on exactly this."*
- Shifts: `n << k = n * 2^k`, `n >> k = n / 2^k` (integer division). Run the deck's own example live: `9 << 2 = 36`, `9 >> 2 = 2`.
- On INT_MAX/INT_MIN: connect directly back to Session 29's sign-bit content — MSB 0 with all other 31 bits set to 1 gives `2^31 - 1` (INT_MAX); MSB 1 with all other bits 0 gives `-2^31` (INT_MIN).

**Checkpoint (at 42 min)** — cold-call:
> *"In one sentence: what does XOR do that AND and OR don't?"*
> **Answer:** XOR is 1 only when the two bits differ (exactly one is 1) — AND needs both, OR needs at least one, XOR needs exactly one.

---

## ⚡ Activity 2 — Predict-the-Output: Fresh Numbers Through AND / OR / XOR (42–47 min)

**Format:** Predict-the-Output · **Exposes:** memorized answers to the deck's own repeated `5` and `3` examples, rather than genuine understanding of the bit-by-bit mechanics.

**Setup line (say this):**
> *"New numbers — not 5 and 3 this time. `1100` and `1010`. Before I write anything, commit out loud to what `1100 & 1010`, `1100 | 1010`, and `1100 ^ 1010` each equal."*

**What students do:** Predict all three results before you reveal. Work column by column live: AND → `1000`, OR → `1110`, XOR → `0110`.

**How to handle wrong answers:** If someone gives the same answer for OR and XOR, that's the exact AND-vs-OR-vs-XOR conflation this activity targets — go back to column 4 (leftmost: both bits are 1) and ask what OR gives there versus what XOR gives there.

**Debrief line:**
> *"OR and XOR only disagree in exactly one situation — both bits are 1. OR still says yes (1), XOR says no (0) because 'exactly one' isn't satisfied. That single column is the entire difference between the two operators — check it first whenever you're unsure which one you're looking at."*

**Cut rule:** If short on time, do only AND and XOR (skip OR) — the AND/XOR pairing carries more of the confusion than OR does.

---

## ⚡ Activity 3 — Spot the Bug: Why Does `~5` Print `-6`? (47–52 min)

**Format:** Spot the Bug · **Exposes:** the assumption that NOT is a simple visible-bit flip (`0101` → `1010` = 10), ignoring that integers are stored as fixed-width 32-bit two's-complement values, so *every* bit flips, including the 28 leading zeros — and the result's sign bit determines how it prints.

**Setup line (say this):**
> *"By hand, `~0101` looks like `1010`, which is 10. The deck's own C++ code runs `bitwiseNOT(5)` and prints `-6`. Somebody is wrong — is it the deck, or is it your hand calculation? Sixty seconds, then defend your answer."*

**What students do:** Discuss in pairs. The deck's own note (slide 49) is the resolving evidence: 5 as a 32-bit integer is `00000000 00000000 00000000 00000101`; NOT flips *all 32 bits*, including every leading zero, giving `11111111 11111111 11111111 11111010`. That bit pattern, interpreted as a signed 32-bit integer (sign bit 1 → negative, take 2's complement to find magnitude), is `-6`.

**How to handle wrong answers:** If a pair insists the deck's output is wrong, walk them through interpreting `11111111...11111010` as a signed integer using the same sign-bit method from Session 29's Slide Block C — MSB is 1, so it's negative; 2's complement of the pattern gives magnitude 6.

**Debrief line:**
> *"Your hand calculation wasn't wrong on the bits you wrote down — it was incomplete. You only flipped the 4 bits you could see, but a computer's integer has 32 bits, and NOT flips every single one, including all those invisible leading zeros. Once you flip all 32 and read the sign bit, `-6` is exactly correct."*

**Cut rule:** If very tight on time, skip the pair discussion and present the resolution directly — the debrief line is the actual payoff and stands alone.

---

## Exit Ticket (52–55 min)

> On paper before anyone leaves: Compute `1011 & 0110`, `1011 | 0110`, and `1011 ^ 0110`. Then in one sentence, say what `9 >> 1` equals and why.
> **Answers:** AND = `0010`, OR = `1111`, XOR = `1101`. `9 >> 1 = 4` (integer division of 9 by 2, dropping the remainder — right shift discards the bit that falls off the end).

Scan on the way out. If OR and XOR answers match, reopen Activity 2's debrief for 60 seconds at the start of Session 32.

**Homework:** re-derive today's `~5 = -6` result from scratch, writing out all 32 bits. <!-- placement: inferred — no homework/practice-unit table exists for this course; this is a natural close, not a platform assignment -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| OR and XOR give the same result | Both only differ from AND by "needing fewer 1s" and feel interchangeable at a glance | Activity 2 — isolating the exact column where both bits are 1, the only place they diverge |
| `~5` should just be the visible bits flipped (`1010` = 10) | Students only write down the 4 bits they can see, forgetting integers are fixed-width (32-bit) | Activity 3 — flipping all 32 bits including leading zeros, then reading the sign bit |
| XOR-ing three or more values always cancels to 0 | Generalizing from `a ^ a = 0`, without checking the odd/even-count-of-1s rule | Slide Block B's 3-input XOR truth table — showing cases where the XOR of three bits is 1, not 0 |
| Left shift is always safe and never loses information | The deck's own examples (`9 << 1`, `9 << 2`) stay comfortably within normal integer range | Connecting forward to INT_MAX — a left shift that pushes past `2^31 - 1` overflows into undefined/negative territory |
| Bitwise operators are just an alternate syntax for the equivalent arithmetic (`<<` is "the same as" `*`) | The results often match for small, positive numbers, so the two seem interchangeable | Session's own framing: bitwise ops work per-bit on the CPU directly, which is *why* they're fast and *why* they can behave differently (e.g., NOT) from the arithmetic operation that shares a "similar" outcome |

---

## Instructor Notes

- **This session is a hard prerequisite for everything from Session 32 onward** — every Bit Manipulation Technique (check/set/clear/toggle a bit) is built directly out of AND, OR, XOR, and shifts. If the class is shaky here, it compounds badly next session; don't compress Slide Block B to save time elsewhere.
- **The `~5 = -6` result (Activity 3) is the single most counterintuitive fact in the session** — budget the full 5 minutes for it even if something else needs to be cut. It also doubles as spaced retrieval practice on Session 29's 2's-complement content.
- **Keep the "efficiency, memory, control" trio and the "AND=both, OR=at least one, XOR=exactly one" phrasing on the board** for the whole session — both get reused as checkpoints and in the exit ticket.
- **Have a second pair of fresh numbers ready** beyond `1100`/`1010` for Activity 2 in case a section finishes early or a re-run is needed with a different cohort.
- **Classroom Quiz question bank does not exist yet for this course** — pull 5–6 MCQs from the platform live at the marked point.
