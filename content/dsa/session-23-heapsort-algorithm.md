# Session 23 — Heapsort Algorithm

**Duration** 50 min <!-- placement: inferred — slightly above the 45-min default because the deck's dry run has two distinct phases (build + sort), each needing its own worked walkthrough, plus two activities --> · **Topic** Heaps · **Prerequisite** Implementation of Binary Heap (Session 22)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Heapsort Algorithm | https://docs.google.com/presentation/d/10rgo7gButuNwI0MsSYl6TOrFvKOci7gKaRJDso0V35A/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Describe Heapsort as two phases — heap construction, then repeated extraction — and state which heap type (max/min) produces ascending vs. descending order. *(UNDERSTANDING)*
2. Explain why building a heap from an unsorted array starts at the *last non-leaf node*, not the root, and why this makes construction `O(n)` rather than `O(n log n)`. *(UNDERSTANDING)*
3. Trace the max-heap construction phase on a given unsorted array, computing the last-non-leaf index and each `down_heapify` swap. *(APPLYING)*
4. Trace the sorting phase — swap root with the last live element, shrink the heap, `down_heapify` from the root — until the array is fully sorted. *(APPLYING)*
5. State Heapsort's overall time complexity `O(n log n)` and auxiliary space complexity `O(1)`. *(REMEMBERING)*

---

## Warm-Up Poll — Retrieval Practice on Session 22: Implementation of Binary Heap (0–6 min)

Say: *"Eight on the array-based heap operations from yesterday. Heapsort today is built entirely out of one of those operations, so this needs to be automatic."*

**Q1.** Left child index formula for a node at index `i`?
`A` `i*2` · `B` `2i+1` · `C` `2i-1` · `D` `i/2`
→ **Answer:** B.

**Q2.** Parent index formula for a node at index `i`?
`A` `i/2` · `B` `(i-1)/2` · `C` `2i` · `D` `i-1`
→ **Answer:** B.

**Q3.** *(MSQ — pick 2)* Which are true of heapify direction?
`A` `up_heapify` is used on insert · `B` `up_heapify` moves toward the leaves · `C` `down_heapify` is used when removing an element · `D` `down_heapify` moves toward the root
→ **Answer:** A and C.

**Q4.** Time complexity of `insert()` on an array-based binary heap?
`A` `O(1)` · `B` `O(log n)` · `C` `O(n)` · `D` `O(n log n)`
→ **Answer:** B.

**Q5.** Time complexity of `getMax()`?
`A` `O(1)` · `B` `O(log n)` · `C` `O(n)` · `D` `O(n log n)`
→ **Answer:** A.

**Q6.** During `extractMax()`, what replaces the removed root?
`A` The smallest leaf · `B` The last element in the heap · `C` The left child · `D` Nothing — it stays empty until the next insert
→ **Answer:** B. *Read:* This is exactly the step Heapsort's sorting phase reuses today — flag it.

**Q7.** `size()` and `empty()` are both what complexity?
`A` `O(log n)` · `B` `O(n)` · `C` `O(1)` · `D` `O(n log n)`
→ **Answer:** C.

**Q8.** True or False: `capacity` and `size` are the same variable.
`A` True · `B` False
→ **Answer:** B — `capacity` is the array's fixed length, `size` is the current element count.

**Running it** — poll tool, ~40 s/question. Total 6 min including reads.

---

## Hook (6–9 min)

Say: *"You now have a fully working max heap — insert, extractMax, all in O(log n). Here's a question I want you to actually think about, not answer instantly: what happens if you just call extractMax() over and over, n times in a row, and write down what comes out each time?"*

Let a few guesses land. Someone will say "the elements in decreasing order."

> *"Exactly. You just described a sorting algorithm using nothing but a tool you already built yesterday. That's Heapsort. No new data structure, no new operation — just extractMax(), called repeatedly, with one clever trick for where you park each result."*

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 4–25: Heapsort introduction, explanation of the two phases, full Max Heap Construction dry run on [3, 9, 2, 1, 4, 5, 7], construction complexity -->
Covers: Heapsort definition (two phases: heap construction, then sorting) → worked example array `[3, 9, 2, 1, 4, 5, 7]` → full max-heap construction dry run, starting from the *last non-leaf node* (index `⌊7/2⌋ - 1 = 2`) and working backward to the root, `down_heapify`-ing at each node → resulting max heap `[9, 4, 7, 1, 3, 5, 2]` → construction complexity `O(n)`.

**Beats to emphasise**

- **Two phases, say both names every time:** "heap construction" builds a max heap (for ascending output) or min heap (for descending) from the raw array; "sorting" then repeatedly extracts the root and shrinks the heap. Students who only remember "heapsort uses a heap" miss that it's genuinely a two-stage algorithm.
- **Construction starts at the last non-leaf node, not the root.** This is the single most counter-intuitive beat in the whole session — walk through *why*: leaf nodes trivially satisfy the heap property alone, so there's nothing to fix there. Work backward from the last internal node toward the root, `down_heapify`-ing each one.
- **On the O(n) construction complexity (slide 25):** most nodes live near the leaves and need very little bubble-down work; only a few nodes near the root can move the full height. This is why building a heap this way is `O(n)`, not the `O(n log n)` you'd get from `n` naive insertions.

**Checkpoint (at 19 min)** — cold-call two students:
> *"Array has 7 elements. What's the index of the last non-leaf node, and why do we start there?"*
> **Answer:** Index `⌊7/2⌋ - 1 = 2`. We start there because indices after it are leaves, which trivially satisfy the heap property with no children to compare against.

---

## ⚡ Activity 1 — Predict-the-Output: Where Does Construction Start? (21–26 min)

**Format:** Predict-the-Output · **Exposes:** the instinct to start heapifying from the root, which is how insertion works but not how *construction from a raw array* works.

**Setup line (say this):**
> *"Here's the unsorted array again: 3, 9, 2, 1, 4, 5, 7. Before I show you the deck's answer — which index do we start heapifying from, and what's the very first swap?"*

**What students do:** Compute the last non-leaf index (`⌊7/2⌋ - 1 = 2`, value `2`), identify its children at indices 5 and 6 (values `5` and `7`), and predict the swap: `7 > 2`, so swap index 2 and index 6.

**How it surfaces:** If someone starts from index 0 (the root, value `3`) instead, stop and ask: *"Does the root have a heap-property violation to fix yet, or does something below it need fixing first?"* Point back at the last-non-leaf logic from Slide Block A.

**Debrief line:**
> *"You never build a heap top-down from raw data — you fix the bottom first, then work up. That single choice is the entire reason construction is O(n) instead of O(n log n)."*

**Cut rule:** If running late, skip the swap prediction and just confirm the starting index verbally.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (31–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 26–58: the sorting phase dry run (repeated swap-root-with-last, shrink, down_heapify) through to a fully sorted array, overall complexity, Key Takeaways -->
Covers: sorting-phase mechanics — swap the root (current maximum) with the last *live* element of the heap, shrink `heapSize` by one, `down_heapify` from the root to restore heap order, repeat — worked through completely on the max heap `[9, 4, 7, 1, 3, 5, 2]` until the array reads `[1, 2, 3, 4, 5, 7, 9]` → overall time complexity `O(n log n)`, auxiliary space `O(1)` → Key Takeaways.

**Beats to emphasise**

- **Say the loop invariant out loud, once, before diving into slides:** "swap root with the last live slot, shrink the heap by one, `down_heapify` the new root, repeat until one element is left." Every one of the ~30 remaining dry-run slides is just this loop, one iteration per slide-group — narrate the pattern, don't re-derive it each time.
- **The "sorted" region is everything past the current `heapSize`.** Once an element is swapped to the end and the heap shrinks past it, that position is *frozen* — it's never touched again. This is what makes the sort happen in place with zero extra array.
- **Contrast the complexity numbers explicitly:** construction alone is `O(n)` (Slide Block A), but the sorting phase adds `O(n log n)` on top (n extractions, each `O(log n)`) — so the *overall* algorithm is `O(n log n)`, dominated by the sorting phase, not the construction phase.

**Checkpoint (at 42 min)** — show hands:
> *"Heapsort's auxiliary space complexity — who says O(n), who says O(1)?"*
> **Answer:** `O(1)`. Everything happens by swapping elements within the original array; no second array is ever allocated.

---

## ⚡ Activity 2 — Dry-Run Relay: One Full Extraction (44–48 min)

**Format:** Live Dry-Run Relay · **Exposes:** treating the sorting phase as something new, instead of recognising it as `extractMax()` called on a shrinking heap.

**Setup line (say this):**
> *"We just built this max heap: 9, 4, 7, 1, 3, 5, 2. I'm going to run exactly one iteration of the sorting phase. I move nothing until you tell me the swap, the new heap size, and the down_heapify comparison."*

**What students do:** Call out, in order: *"Swap index 0 (value 9) with index 6 (value 2) — the last live slot."* → *"heapSize drops from 7 to 6 — index 6 is now frozen, sorted."* → *"down_heapify from the new root, value 2: compare its children at index 1 (value 4) and index 2 (value 7) — 7 is bigger, swap."* → *"Now check the new position's child — is a further swap needed?"*

**How it surfaces:** If a student tries to compare the new root against the *frozen* index-6 slot, stop and ask: *"Is that slot still part of the heap, or is it done?"* — reinforcing the `heapSize` boundary from Slide Block B.

**Debrief line:**
> *"That's it. That's the entire sorting phase — extractMax(), the exact operation from yesterday, called n times, with the removed maximum parked at the current heap's last slot each time instead of thrown away. Heapsort isn't a new algorithm bolted onto a heap — it's the heap's own operation, reused."*

**Cut rule:** If running short, relay only the swap and heap-size shrink; state the `down_heapify` comparison yourself.

---

## Exit Ticket (48–50 min)

> On paper or in chat: *"In your own words: why does Heapsort start construction from the last non-leaf node instead of the root, and why is the overall algorithm O(n log n) instead of O(n)?"*
> **Answer:** Starting from the last non-leaf node means most of the work happens near the (cheap) leaves, making construction `O(n)`. But the *sorting* phase still calls the equivalent of `extractMax()` once per element, each costing `O(log n)`, so the overall algorithm is dominated by that phase: `O(n log n)`. <!-- placement: inferred exit-ticket question, built directly from the two complexity beats emphasised in the session -->

**Homework:** re-run the full dry run — construction and sorting — on `[3, 9, 2, 1, 4, 5, 7]` from memory. <!-- placement: inferred; no homework/practice-unit table exists for this course -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Heap construction starts at the root and heapifies downward from there | That's how *insertion* works (bubble up from a leaf), so root-first feels consistent | Activity 1 — force the last-non-leaf-index computation before revealing the deck's answer |
| Building the heap is `O(n log n)` (reasoning: "n elements, each takes log n") | Naive generalisation from the fact that a single `down_heapify` call is `O(log n)` | Slide 25's explanation — most nodes are near the leaves and do very little work; only a few near the root move far |
| Heapsort needs a second array to hold the sorted output | Other sorts they may know (merge sort) use auxiliary arrays | Point at the `heapSize` shrinking each iteration — the "sorted" region is just the tail of the *same* array, frozen in place |
| Once root and last element are swapped, both positions are still "in the heap" together | The swap happens visually within one array, with no visible boundary marker | Emphasise the `heapSize` variable explicitly — anything at or past `heapSize` is finalized and never revisited |
| Heapsort could just as well build its heap by inserting elements one at a time | That's a valid *alternative* construction method, and it's easy to conflate with the one taught here | Name it directly: n one-at-a-time insertions would cost `O(n log n)` for construction alone — the last-non-leaf-node method taught today is chosen specifically because it's `O(n)` |

---

## Instructor Notes

- **Pacing risk is highest in the sorting-phase dry run** (slides 27–56) — it's roughly 30 slides re-running the same four-step loop on a shrinking array. State the loop invariant once, explicitly, before starting, and move through the slides confirming rather than re-teaching.
- **This session is a payoff session, not a new-concept session** — everything here is `extractMax()` and `down_heapify()` from Session 22, applied in a loop. Lean on that framing to keep energy up; students should feel like they already know this.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- Keep both complexity numbers on the board simultaneously by the end of class: construction `O(n)`, overall `O(n log n)`, space `O(1)`. Students frequently misquote Heapsort as `O(n)` overall because they only remember the construction phase's complexity.
