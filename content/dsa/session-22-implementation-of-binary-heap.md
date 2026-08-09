# Session 22 — Implementation of Binary Heap

**Duration** 60 min <!-- placement: inferred — matches Session 21's pacing; this session has a full worked insert, a full worked extractMax, plus array-representation and index-formula content that session 21 didn't cover, so it doesn't compress into 45 --> · **Topic** Heaps · **Prerequisite** Introduction to Heaps (Session 21)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Implementation of Binary Heap | https://docs.google.com/presentation/d/1vvJCSr9J7R6iDbHufVqSWLRXYfRDl77iaNVfBakXIcA/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the two conditions a Binary Heap must satisfy (complete binary tree + heap property). *(REMEMBERING)*
2. Compute the array-index formulas for a node's left child, right child, and parent (`2i+1`, `2i+2`, `(i-1)/2`), given an arbitrary index. *(APPLYING)*
3. Distinguish `up_heapify()` (used on insert) from `down_heapify()` (used on delete), and state which direction each moves through the heap. *(UNDERSTANDING)*
4. Trace the array-based `insert()` and `extractMax()` operations on a worked example, including every index recomputation. *(APPLYING)*
5. State the time and space complexity of all five heap operations — `insert`, `extractMax`, `getMax`, `size`, `empty`. *(REMEMBERING)* <!-- placement: inferred from the deck's own complexity summary table, slide 47 -->

---

## Warm-Up Poll — Retrieval Practice on Session 21: Introduction to Heaps (0–7 min)

Say: *"Eight quick ones on yesterday's heap basics. We're about to rebuild everything you learned on top of a plain array, so I need to know it's solid."*

**Q1.** What are the two properties every heap must satisfy?
`A` Complete binary tree + heap-order property · `B` Balanced binary tree + sorted order · `C` Binary-search property + complete tree · `D` No properties — any tree qualifies
→ **Answer:** A.

**Q2.** In a max heap, where is the largest element always located?
`A` Any leaf · `B` The root · `C` Wherever it was last inserted · `D` The middle level
→ **Answer:** B.

**Q3.** *(MSQ — pick 2)* Which are true about heap shape?
`A` All levels are full except possibly the last, filled left to right · `B` Left and right children are sorted relative to each other · `C` Height is always `log(n)` · `D` Height can be `O(n)` for a skewed heap
→ **Answer:** A and C. *Read:* D is the BST-carryover trap from two sessions ago — if anyone picks it, that's still unresolved and worth a 10-second correction before moving on.

**Q4.** When inserting a new element, where does it first get placed?
`A` Wherever it belongs in sorted order · `B` The first vacant slot in the last level · `C` As the new root · `D` As a randomly chosen leaf
→ **Answer:** B.

**Q5.** What happens immediately after that placement?
`A` Nothing — insertion is done · `B` Compare against its parent and swap upward while it's bigger ("bubble up") · `C` Compare against both children and swap downward · `D` Rebuild the whole heap from scratch
→ **Answer:** B.

**Q6.** Why is `extractMax()` `O(log n)`?
`A` It isn't — it's `O(1)` · `B` It has to scan the entire heap · `C` Replacing the root and bubbling the replacement down costs at most one swap per level, and there are `log n` levels · `D` It's `O(n log n)`
→ **Answer:** C.

**Q7.** Why is `getMax()` cheaper than `extractMax()`?
`A` `getMax()` just reads the root; `extractMax()` has to remove it and restore heap order · `B` They cost the same · `C` `getMax()` needs to search, `extractMax()` doesn't · `D` There's no real difference
→ **Answer:** A.

**Q8.** Name one real-world application of heaps from yesterday's deck.
`A` Binary search · `B` Priority queues / heap sort / graph algorithms / kth largest-smallest · `C` Hashing · `D` Recursion
→ **Answer:** B.

**Running it** — poll tool, ~40 s/question. Total 7 min including reads.

---

## Hook (7–11 min)

Say: *"Yesterday, every heap you saw was drawn as a tree — circles and lines, parent pointers, child pointers. Here's the twist: almost nobody actually implements a heap that way. In real code, a heap usually lives in one flat array, with no pointers at all."*

Draw a max heap tree quickly (or reuse the board from yesterday), then next to it write a plain array: `[40, 25, 20, 10, 5, 30, 15, 35]`.

Ask: *"Same heap. No pointers anywhere. How does the array know who's whose parent and whose child?"*

Take one or two guesses, then land it: *"Three formulas. That's the entire trick, and it's what today's session is really about."*

---

## Slide Block A (11–22 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 4–10: Binary Heap definition, array representation, index formulas, operations overview, heapify concept -->
Covers: Binary Heap definition → array representation with root at `arr[0]` → index formulas (`left = 2i+1`, `right = 2i+2`, `parent = (i-1)/2`) → the six operations (heapify, insert, getMax, extractMax, size, empty) → `up_heapify()` vs `down_heapify()`.

**Beats to emphasise**

- **Zero-indexed, always.** Write all three formulas on the board and keep them visible for the whole session — `left = 2i+1`, `right = 2i+2`, `parent = (i-1)/2`. This single set of formulas drives every operation for the rest of the session.
- **`up_heapify()` runs on insert, `down_heapify()` runs on delete.** Name both explicitly and tie each to a direction: up_heapify walks *toward the root*, down_heapify walks *toward the leaves*. Students conflated "heapify" with "just the insert thing" yesterday — this slide is where that gets corrected.
- The **operations list (slide 7)** is your map for the rest of the session — insert, getMax, extractMax, size, empty, all built on the same three index formulas.

**Checkpoint (at 22 min)** — cold-call two students:
> *"Node at index 3. What's its left child index, right child index, and parent index?"*
> **Answer:** left = `2(3)+1 = 7`, right = `2(3)+2 = 8`, parent = `(3-1)/2 = 1`.

---

## ⚡ Activity 1 — Predict-the-Output: Index Arithmetic (22–27 min)

**Format:** Predict-the-Output · **Exposes:** shaky or off-by-one index arithmetic — the one thing every later operation in this session (and course) depends on.

**Setup line (say this):**
> *"I'll give you an index. Before I write the formula's answer, you compute left child, right child, and parent — out loud, no calculators, just the three formulas on the board."*

Use the deck's own array `[40, 25, 20, 10, 5, 30, 15, 35]` (indices 0–7).

**What students do:** For index `1` (value 25): predict left = 3, right = 4, parent = 0. For index `3` (value 10): predict left = 7, right = 8, parent = 1 — then flag that right child index 8 is out of range (array only has indices 0–7), so node 10 has only one child.

**How it surfaces:** If someone answers with `2i` and `2i+1` instead of `2i+1` and `2i+2`, they're using the 1-indexed formulas some textbooks teach. Correct it immediately and re-anchor: *"We are always 0-indexed here — `arr[0]` is the root."*

**Debrief line:**
> *"Every operation today — insert, extractMax, heapify — is just these three formulas, applied over and over. Get the formula wrong once and the heap silently breaks; it won't crash, it'll just quietly stop being a heap."*

**Cut rule:** If running late, do index `1` only and state the index-`3` out-of-range case yourself rather than asking students to spot it.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (34–48 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 11–42: initialization, full worked insert (value 50), insert complexity, getMax, getMax complexity, full worked extractMax, extractMax complexity -->
Covers: heap initialization (capacity + size variable) → worked insert of `50` into a 9-element max heap, tracked through every swap and index recomputation → insert complexity `O(log n)` → `getMax()` (return `arr[0]`, `O(1)`) → worked `extractMax()` on the resulting heap, tracked through every `down_heapify` comparison and swap → extractMax complexity `O(log n)`.

**Beats to emphasise**

- **`capacity` and `size` are two different numbers, both tracked explicitly.** `capacity` is the array's fixed length; `size` is how many elements are currently in use. Point this out on the initialization slide — it resolves the "how does the array know when to stop" question before it gets asked.
- **On the insert walkthrough:** narrate the pattern once — "place at index `size`, then repeatedly compute `parent = (i-1)/2`, compare, swap if the new element is bigger, move `i` to the parent's index, repeat" — then move through the remaining slides at a confirming pace rather than re-deriving the arithmetic each time.
- **On the extractMax walkthrough:** the two-step shape is identical to yesterday's tree version — replace root with the last element, then `down_heapify` — but every single comparison is now driven by computing `2i+1` and `2i+2` for children, checking bounds, and picking whichever child is *larger* before swapping. This is the part that trips students moving from pointers to arrays: they can describe "bubble down" in words but stumble computing which child index actually wins.

**Checkpoint (at 48 min)** — show hands:
> *"getMax() is O(1). extractMax() is O(log n). In one sentence — why the difference?"*
> **Answer:** `getMax()` only reads `arr[0]`; `extractMax()` has to remove the root, promote the last element, and then restore heap order by bubbling it down through up to `log n` levels.

---

## ⚡ Activity 2 — Dry-Run Relay: extractMax() by Index Arithmetic (50–55 min)

**Format:** Live Dry-Run Relay · **Exposes:** students who can describe "bubble down" correctly in words but stumble translating it into which array index actually gets compared and swapped next.

**Setup line (say this):**
> *"Same extractMax() operation you just watched, one more time — but I move nothing unless you give me the exact index math. Not 'swap with the bigger child.' Tell me the index."*

**What students do:** Using the deck's own worked extractMax example (root value replaced by the last element, size drops from 10 to 9): at index `0`, call out `left = 2(0)+1 = 1`, `right = 2(0)+2 = 2`, then state which of those two array positions holds the larger value and that a swap with that index is needed. After the swap, recompute for the new index and repeat.

**How it surfaces:** If a student says "swap with the left child" without computing `2i+1` and checking it against `2i+2`'s value, stop and make them state both indices and both values before allowing the swap. This is the exact gap the activity exists to catch.

**Debrief line:**
> *"The idea — bubble the replacement down until it's no longer smaller than either child — is exactly what you did with pointers yesterday. The only thing that changed is that 'child' is now a formula, not something you can just point at. If the formula's wrong, nothing crashes — the heap just quietly stops being a heap."*

**Cut rule:** If running short, relay only the first swap (index 0 → its larger child) and state the remaining down-heapify steps yourself.

---

## Slide Block C (55–58 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 43–49: size(), empty(), complexity summary table, Key Takeaways -->
Covers: `size()` (return the `size` variable, `O(1)`) → `empty()` (check `size == 0`, `O(1)`) → complexity summary table for all five operations → Key Takeaways.

**Beats to emphasise**

- `size()` and `empty()` are the two operations students are most likely to over-think — both are a single variable read/comparison, `O(1)`, no traversal involved. Say this plainly; don't let the slide's brevity make it feel like there's a catch.
- Put the complexity summary table (Insert `O(log n)`, extractMax `O(log n)`, getMax `O(1)`, size `O(1)`, empty `O(1)`, all `O(1)` space) on the board and leave it up — it's the single most quotable slide of the session.

---

## Exit Ticket (58–60 min)

> On paper or in chat: *"Array-based max heap: `[45, 30, 20, 10, 25, 15]` (indices 0–5). What are the left child, right child, and parent index of the node at index 2? Is `insert()` or `extractMax()` more expensive, and why?"*
> **Answer:** For index 2: left = `2(2)+1 = 5`, right = `2(2)+2 = 6` (out of range — no right child), parent = `(2-1)/2 = 0`. Both `insert()` and `extractMax()` are the same order of growth, `O(log n)` — neither is "more expensive" in Big-O terms, though `extractMax()` does strictly more work per level (two comparisons per step vs. one). <!-- placement: inferred exit-ticket scenario, built on the same index formulas taught in the session -->

**Homework:** re-derive the three index formulas from scratch and re-run the extractMax dry run on paper, without looking at the slides. <!-- placement: inferred; no homework/practice-unit table exists for this course -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Left child = `2i`, right child = `2i+1` | Some references use 1-indexed heap formulas (`2i`, `2i+1`, `i/2`) | Keep the 0-indexed formulas (`2i+1`, `2i+2`, `(i-1)/2`) on the board all session; Activity 1 forces students to apply them, not just recite them |
| "Heapify" only means the bubble-up step from insertion | Session 21 spent more airtime on insertion's bubble-up than on extraction's bubble-down | Name both explicitly on Slide Block A: `up_heapify()` for insert, `down_heapify()` for delete — different directions, different triggers |
| Arrays need shifting/resizing on every insert, like a sorted array | Prior experience with sorted arrays where insertion is expensive | Point out insertion here places the new element at `arr[size]` (the next open slot) and only *swaps* upward — no shifting of other elements |
| `size()` and `empty()` require some kind of traversal or computation | Unfamiliarity with a data structure that tracks its own count explicitly | Point at the `capacity`/`size` variables from the initialization slide — both are `O(1)` reads of a variable that's already being maintained |
| During `down_heapify`, swap with whichever child "looks bigger" without checking both | Bubble-down in words sounds like "compare with a child," singular | Activity 2's dry-run relay — force students to state both child indices and both values before naming the swap target |

---

## Instructor Notes

- **The index formulas are the whole session.** If Activity 1 reveals the class is shaky on `2i+1`/`2i+2`/`(i-1)/2`, do not proceed into the worked insert until it's solid — every subsequent slide assumes fluency with it.
- **Pacing risk:** the worked insert (slides 12–24) and worked extractMax (slides 30–41) are each roughly a dozen slides for a handful of repeated comparisons. State the pattern once per operation, then move briskly — dwelling on each slide's arithmetic will run the session long.
- **Contrast with Session 21 explicitly.** Students just spent a session reasoning about heaps with tree pointers; this session's entire cognitive load is *translating* that same logic into array indices. Say out loud, more than once, "same idea, different representation."
- **Classroom Quiz placement is a placeholder.** There is no MCQ bank in the source material for this course — pull 5–6 questions live from the platform bank; do not invent questions.
- Have the complexity summary table (slide 47) written on the board by the end of class and left visible — it's the cleanest single artifact from this session and the one most likely to show up again in Sessions 26–28.
