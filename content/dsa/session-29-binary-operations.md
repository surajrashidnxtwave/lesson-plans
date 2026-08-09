# Session 29 — Binary Operations

**Duration** 75 min · **Topic** Bit Manipulation — Binary Number System · **Prerequisite** Session 28 (Top K Frequent Elements — Heaps)
**Session type** Concept lecture

<!-- placement: inferred — duration set to 75 min, not the 45-min default. Deck spans 91 slides across four genuinely distinct sub-skills (conversion, addition, subtraction-without-borrow, subtraction-with-borrow/complements) — compressing to 45 min would force cutting one entirely. -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Binary Operations | https://docs.google.com/presentation/d/153LZGni1xef_OfEY2p7qdybI5EgPvjxf5IPRvr8konE/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define bit, LSB, and MSB, and state the place values they represent. *(REMEMBERING)*
2. Convert a decimal number to binary using the division-by-2 method, and a binary string back to decimal using positional (place) values. *(APPLYING)*
3. Perform binary addition and binary subtraction (both the no-borrow/XOR shortcut and the borrowing method) by hand. *(APPLYING)*
4. Compute the 1's complement and 2's complement of a binary number. *(APPLYING)*
5. Use 2's complement addition to perform subtraction and correctly interpret a negative result from its sign bit. *(ANALYZING)*

<!-- placement: inferred from the "Key Takeaways" recap slides (84–85) and the closing recap slides (88–91), which restate exactly these five skills as the session's summary. -->

---

## Warm-Up Poll — Retrieval Practice on Session 28 (0–7 min)

> Retrieval practice on **Top K Frequent Elements** (the last Heaps session). No new content — this is recall.

Say: *"Eight quick ones on yesterday's heap problem. Answer solo, no discussion yet."*

**Q1.** In the brute-force approach for Top K Frequent Elements, what do you build first?
`A` A min-heap of size k · `B` A frequency map (dictionary) of element→count · `C` A sorted array of the input · `D` A max-heap of all elements
→ *Read:* Answer B. If many pick A, they've mentally skipped straight to the optimal approach — fine instinct, but they need to be able to describe brute force too.

**Q2.** After building the frequency map in the brute-force approach, what's the very next step?
`A` Return the top k keys in insertion order · `B` Convert to (frequency, element) pairs and sort descending · `C` Push all pairs into a min-heap · `D` Binary search for k
→ *Read:* Answer B.

**Q3.** Worst-case time complexity of the brute-force approach (u = n unique elements)?
`A` O(n) · `B` O(n log k) · `C` O(n log n) · `D` O(n²)
→ *Read:* Answer C — sorting the frequency list dominates.

**Q4.** In the optimal approach, what data structure holds the "top k so far" while scanning the frequency map?
`A` A max-heap of size n · `B` A min-heap of size k · `C` A stack · `D` A sorted list rebuilt every step
→ *Read:* Answer B. If this doesn't stick, restate the one-liner live: min-heap because you want to evict the *smallest* to protect the largest.

**Q5.** When does an element get popped from the heap in the optimal approach?
`A` Every time a new element is pushed, regardless of size · `B` Only when the heap size exceeds k · `C` Only when the new element is larger than everything already in the heap · `D` Never — the heap only grows
→ *Read:* Answer B.

**Q6.** *(MSQ — pick 2)* Which are true about the optimal approach's complexity?
`A` Time is O(n log k) · `B` Time is O(n log n) always · `C` Space is O(n + k) · `D` Space is O(1)
→ *Read:* A and C.

**Q7.** Why is O(n log k) better than O(n log n) when k is much smaller than the number of unique elements?
`A` They're the same thing written differently · `B` log k grows far slower than log n when k << n, so each heap operation does less work · `C` Heaps are always faster than sorting, period · `D` It isn't actually better — common misconception
→ *Read:* Answer B. If C gets picked, correct it now — heaps aren't unconditionally faster, the *k* is doing the work here.

**Q8.** In the dry run, after processing element 3 (frequency 1) with k=2, why did the heap immediately pop (1,3)?
`A` 3 is a prime number · `B` The heap size (3) exceeded k (2), so the smallest pair was evicted · `C` 3 was already in the heap · `D` The algorithm made an error
→ *Read:* Answer B.

**Running it** — poll tool, ~40 s per question. Total 7 min.

---

## Hook (7–11 min)

Write **25** on the board. Ask: *"That's how you and I see this number. A computer has never seen a '2' or a '5' — it only has switches that are on or off. How does it actually store 25?"*

Take two or three guesses out loud, then reveal the deck's own framing: everything a computer stores — this number, an image, this sentence — is ultimately a sequence of two symbols, 0 and 1. Say: *"By the end of today you'll convert between what you see and what the machine sees, in both directions, and you'll know how it stores negative numbers too — which is the part that surprises everyone."*

Tie forward: *"Hold onto that curiosity — we come back to how computers store negative numbers near the end of the session, and it's stranger than 'put a minus sign in front.'"*

---

## Slide Block A (11–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — grouped as "bit fundamentals + both conversion directions", slides 4–23 -->
Covers: What is a bit / LSB / MSB → Decimal-to-Binary conversion (division-by-2 method, pseudocode, code) → Binary-to-Decimal conversion (positional-value method, pseudocode, code).

**Beats to emphasise**

- **LSB is rightmost, MSB is leftmost** — say it out loud with the word "significant" attached each time; this pairing is the single most reversed fact in this topic.
- On decimal→binary: the algorithm collects remainders **from LSB to MSB**, so the string must be **reversed** at the end. This is the exact bug source for Activity 1 — flag it now, don't wait.
- On binary→decimal: walk the deck's own example (`11001` → 25) bit by bit, reading right to left, and say the power of 2 out loud each time (2⁰, 2¹, 2², …).

**Checkpoint (at 24 min)** — cold-call two students:
> *"One sentence each: how do you go from decimal to binary, and how do you go back?"*
> **Answer:** Decimal→binary: repeatedly divide by 2, record remainders, reverse them. Binary→decimal: multiply each bit by 2 raised to its position (from the right, starting at 0), and sum.

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: Convert 37 Both Ways (24–30 min)

**Format:** Dry-Run Relay · **Exposes:** the reverse-the-remainders step being skipped, and position-index errors (starting at 1 instead of 0) in the binary-to-decimal direction.

**Setup line (say this):**
> *"New number, not from the slides — 37. Row by row, out loud, you tell me what to write. I only write what you say."*

**What students do:** One student at a time calls out the next division step (`37 / 2 = 18 remainder 1`, `18 / 2 = 9 remainder 0`, …) until the quotient hits 0. Once the remainder column is done, ask the class: *"Read it off top to bottom — is that the answer?"* Let someone catch that it must be reversed. Then flip to binary→decimal: put `100101` on the board and relay the positional-value sum back to 37.

**How to handle wrong answers:** If a student reads the remainders top-to-bottom as the final answer (skipping the reverse), don't correct immediately — write it exactly as they say, then ask the class to verify by converting it back. The mismatch is the correction.

**Debrief line:**
> *"Two mirror-image algorithms. One walks right-to-left collecting remainders and has to flip the string at the end. The other walks right-to-left multiplying by powers of 2 and never needs to flip anything. If you mix up which one needs the reverse, you'll get a backwards number that looks plausible — always sanity-check by converting back."*

**Cut rule:** If running short, do only the decimal→binary direction (37) and skip the binary→decimal relay — the reverse-step bug is the higher-value catch of the two.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (35–48 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — grouped as "binary arithmetic", slides 24–54 -->
Covers: Binary Addition (carry propagation) → Binary Subtraction Without Borrow (the bit-difference/XOR shortcut) → Binary Subtraction With Borrow (the borrowing method) → how a computer pads a number to 32 bits with a sign bit.

**Beats to emphasise**

- Run the deck's addition example (`1101 + 1011`) live, column by column, narrating each carry the way the deck does: *"1 + 1 = 2, write 0, carry 1."*
- On subtraction without borrow: point out explicitly that `bitA - bitB` with no borrowing is **the same operation as XOR** on those two bits (1-0=1, 0-0=0, 1-1=0) — the deck's own pseudocode literally computes `bitA XOR bitB`. Say this is only valid when no borrowing is ever needed across the whole subtraction (every top bit ≥ corresponding bottom bit) — this sets up Activity 2.
- The 32-bit padding slides (51–52) are a preview, not a distraction — say explicitly: *"File this away, we need it in twelve minutes."*

**Checkpoint (at 48 min)** — show of hands:
> *"`1010 - 0101` — can I use the no-borrow/XOR shortcut here, yes or no?"*
> **Answer:** No — the top bits are smaller than the bottom bits in more than one column (this is exactly the deck's Borrowing Method example), so the shortcut breaks and you need the borrowing method.

---

## ⚡ Activity 2 — Spot the Bug: When Does "Subtraction = XOR" Break? (48–54 min)

**Format:** Spot the Bug · **Exposes:** the belief that the no-borrow/XOR trick from Slide Block B works for *any* binary subtraction, not just the special case where no column ever needs to borrow.

**Setup line (say this):**
> *"I'm going to apply the XOR shortcut to a subtraction it was never meant for, and it's going to hand me a wrong answer with total confidence. Your job: catch where it breaks, not just that it's wrong."*

Put this on screen and apply the without-borrow method to it, exactly as if it were valid:

```
  1010   (10)
- 0111   (7)
```

Bit-by-bit XOR gives `1101` (13) — which is nonsense for `10 - 7 = 3`.

**What students do:** 60 seconds to discuss in pairs: is `1101` right? If not, why did the shortcut fail here but work fine for the deck's own `1001 - 1000` example?

**How to handle wrong answers:** If students say "the arithmetic is just wrong," push them to compare column-by-column against `1001 - 1000` (where it worked) — the difference is that here, more than one column has a top bit smaller than the bottom bit, meaning a borrow is required and XOR has no concept of "borrowing from the next column."

**Debrief line:**
> *"XOR-as-subtraction is a shortcut for one specific shape of problem: every column's top bit is already at least as big as the bottom bit, so nothing ever needs to borrow. The moment even one column needs a borrow, XOR gives you a bit pattern, not an answer. That's exactly why the deck has a second method — the Borrowing Method — for the general case."*

**Cut rule:** If running late, skip the pair-discussion and just run the broken example live, then state the debrief line directly — the core catch survives without the discussion step.

---

## Slide Block C (54–65 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — grouped as "complements", slides 55–83 -->
Covers: 1's Complement (invert every bit) → 2's Complement (invert, then add 1) → Binary Subtraction Using 2's Complement (convert to 2's complement of the number being subtracted, add, then interpret the sign bit of the result).

**Beats to emphasise**

- Drill the two-step recipe for 2's complement as a chant: *"Flip every bit. Add one."* Nothing more.
- On the 2's-complement subtraction algorithm, slow down hard on **step 4 — interpreting the result**: if the MSB of the result is 1, the true answer is negative, and you must take the 2's complement of *that result* to find its magnitude. This double-take-the-complement step is where almost everyone drops a step.
- Connect back to the Hook and the 32-bit padding slides from Block B: this is the actual mechanism — computers represent negative numbers using 2's complement, with the sign bit as the MSB.

**Checkpoint (at 65 min)** — cold-call:
> *"I hand you a 4-bit subtraction result of `1110` from a 2's-complement subtraction. Is the true answer positive or negative, and how do you find its size?"*
> **Answer:** MSB is 1, so negative. Take the 2's complement of `1110` (invert → `0001`, add 1 → `0010` = 2) to get the magnitude: the true answer is **-2**. (This is the deck's own Example 1.)

---

## ⚡ Activity 3 — Predict-the-Output: 2's Complement Subtraction (65–71 min)

**Format:** Predict-the-Output · **Exposes:** stopping at the raw XOR/addition result instead of running the sign-bit interpretation step, and forgetting that a 1 in the MSB means "go take the complement again."

**Setup line (say this):**
> *"4-bit numbers, new pair: subtract 6 from 2 using 2's complement — `0010 - 0110`. Before I add anything, commit out loud: is this going to come out positive or negative, and why do you already know that before we've done a single operation?"*

**What students do:** Someone should reason "2 < 6, so the true answer is negative" from plain arithmetic *before* touching binary. Then walk the four steps as a class: convert both to 4-bit binary, find the 2's complement of 6 (`0110` → invert `1001` → +1 → `1010`), add to `0010` (`0010 + 1010 = 1100`), interpret (MSB = 1 → negative → complement of `1100` is `0100` = 4 → answer is **-4**).

**How to handle wrong answers:** If a group reports the raw sum `1100` as the final answer, that's the exact bug this activity is built to catch — send them back to the checkpoint's chant: MSB is 1, so take the complement of the *result*, don't report the raw sum.

**Debrief line:**
> *"Every 2's-complement subtraction ends with a question: is the sign bit 1? If yes, you are not done — you owe the class one more complement operation before you can say what the number actually is."*

**Cut rule:** If very tight on time, drop this activity entirely — Slide Block C's checkpoint already forces the same reasoning once, cold-call style, which covers the highest-risk gap at lower time cost.

---

## Exit Ticket (71–75 min)

> On paper before anyone leaves: Convert **18** to binary using the division-by-2 method. Then write its 1's complement and its 2's complement.
> **Answers:** 18 = `10010`. 1's complement = `01101`. 2's complement = `01101 + 1` = `01110`.

Scan on the way out. A wrong 1's complement (forgetting to invert every bit, including leading zeros to a fixed width) is the signal to open Session 30 with a 2-minute recap of the complement chant.

**Homework:** re-attempt today's dry runs (decimal↔binary conversion, both subtraction methods, both complements) from memory, no notes. <!-- placement: inferred — no homework/practice-unit table exists for this course; this is a natural close, not a platform assignment -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The remainder string from decimal→binary conversion doesn't need reversing | The division steps run top-to-bottom, and reading top-to-bottom "feels" like the natural order | Activity 1 — have them verify by converting their un-reversed answer back to decimal and watching it fail |
| Binary subtraction "without borrow" (the XOR trick) works for any two binary numbers | It worked cleanly on the deck's first example (`1001 - 1000`), so it looks general-purpose | Activity 2 — break it deliberately on a case that needs borrowing |
| MSB means "the biggest bit," so a leading 1 always means a big positive number | Outside of signed representations, more significant digit = bigger, no exceptions | Slide Block C — 32-bit padding slides, then the sign-bit checkpoint |
| 2's complement of a number is just "put a minus sign on it" | The chant ("flip bits, add one") feels like a formality once you know the answer should be negative | Making them actually invert and add 1 in Activity 3 before revealing the answer |
| A 2's-complement subtraction is done once you've added the two numbers | The addition step feels like the "answer," and the sign-bit interpretation step is easy to forget | Checkpoint after Slide Block C, then Activity 3's debrief chant |

---

## Instructor Notes

- **Pacing risk:** Slide Block C (complements) is the densest 11 minutes in the session — two complement types plus a 4-step subtraction algorithm plus sign-bit interpretation. If you're behind schedule entering this block, cut Activity 3 per its cut rule rather than rushing the block itself; the complement chant needs to land cleanly or the exit ticket will fail wholesale.
- **Reuse fresh numbers, not the deck's own.** The deck reuses 5, 3, 6, 9, 10 across almost every sub-topic. Students can pattern-match the *answer* without doing the *method*. Every activity above deliberately uses numbers the deck never shows (37, 18, the 1010−0111 break case) — keep doing this in the checkpoints too if you improvise more examples.
- **Have the "flip bits, add one" chant on the board early** in Slide Block C and leave it up — it is the single highest-leverage phrase in the whole session.
- **This is the first Bit Manipulation session** after a full Heaps unit — the warm-up poll is intentionally the only backward-looking content; everything after the Hook is new. Don't assume familiarity with binary beyond what Slide Block A itself delivers.
- **Classroom Quiz question bank does not exist yet for this course** (per the confirmed deviation) — you will need to pull 5–6 MCQs from the platform live. Consider flagging to content ops that Sessions 29–37 all have this same gap.
