# Session 25 — Convert Min Heap to Max Heap

**Duration** 50 min <!-- placement: inferred — above the 45-min default because the deck teaches both a recursive and an iterative approach to the same algorithm, each with its own complexity analysis --> · **Topic** Heaps · **Prerequisite** Max Heap Validation (Session 24)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Convert Min Heap to Max Heap | https://docs.google.com/presentation/d/1H6jFQA7yOrTxBtqJVy-daT3NTYzsfBtzRlL47CcoLgA/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given an array that's a valid min-heap, rearrange it in place into a valid max-heap. *(REMEMBERING)*
2. Explain why the conversion starts at the last non-leaf node and moves toward the root, using `downHeapify` at each step. *(UNDERSTANDING)*
3. Trace the recursive `downHeapify`-based conversion on a given min-heap array, identifying the larger child and each swap. *(APPLYING)*
4. Contrast the recursive and iterative implementations of the same algorithm, and state why the iterative version uses `O(1)` space versus the recursive version's `O(log n)`. *(ANALYZING)*
5. State the overall time complexity, `O(n)`, for both approaches. *(REMEMBERING)*

---

## Warm-Up Poll — Retrieval Practice on Session 24: Max Heap Validation (0–6 min)

Say: *"Seven on yesterday's validation problem before we move to fixing a heap instead of just checking one."*

**Q1.** Max Heap Validation checks which indices?
`A` All indices `0` to `n-1` · `B` Only non-leaf nodes, `0` to `n/2 - 1` · `C` Only the root · `D` Only leaf nodes
→ **Answer:** B.

**Q2.** What triggers an immediate `false` return?
`A` Any child value equal to its parent · `B` Any child value greater than its parent · `C` The array being unsorted · `D` The array having odd length
→ **Answer:** B.

**Q3.** *(MSQ — pick 2)* Which are true about the validation algorithm?
`A` It stops at the first violation found · `B` It counts all violations before returning · `C` It needs bounds checks because a node may have 0, 1, or 2 children · `D` It always checks both children even when out of bounds
→ **Answer:** A and C.

**Q4.** Time complexity of Max Heap Validation?
`A` `O(log n)` · `B` `O(n)` · `C` `O(n log n)` · `D` `O(1)`
→ **Answer:** B.

**Q5.** Space complexity of Max Heap Validation?
`A` `O(n)` · `B` `O(log n)` · `C` `O(1)` · `D` `O(n log n)`
→ **Answer:** C.

**Q6.** In `[60, 40, 20, 10, 45, 35, 15, 5, 25]`, which index pair caused the `false` result?
`A` `arr[0]` and `arr[1]` · `B` `arr[1]` and `arr[4]` · `C` `arr[2]` and `arr[5]` · `D` `arr[3]` and `arr[7]`
→ **Answer:** B.

**Q7.** True or False: leaves need checking too, since they might have "grandchildren" violations.
`A` True · `B` False
→ **Answer:** B — leaves have no children by definition, so there is nothing below them to violate.

**Running it** — poll tool, ~35–40 s/question. Total 6 min including reads.

---

## Hook (6–9 min)

Say: *"Yesterday you checked whether an array was a valid max heap. Today's twist: someone hands you an array that's already a perfectly valid heap — just the wrong kind. It's a min heap, and you need a max heap, same elements, no rebuilding from scratch. What do you actually have to change?"*

Take a guess or two, then: *"Almost nothing about the *shape* — it's still a complete binary tree. What has to change is which value ends up on top at every parent-child pair. That's the entire problem."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 4–12: Problem Statement, Examples 1–2, Recursive Approach overview -->
Covers: problem statement (convert a min-heap array of `n` integers into a max-heap) → Example 1: `[5, 7, 8, 14, 9, 10]` → `[14, 9, 10, 7, 5, 8]` → Example 2: `[6, 10, 9, 18, 12, 15, 20]` → `[20, 18, 15, 10, 12, 6, 9]` → recursive approach overview (start at the last non-leaf node, move upward toward the root; at each node, find the largest of the node and its children; if it isn't the current node, swap and recurse into the affected subtree).

**Beats to emphasise**

- **This is the exact same `downHeapify` procedure you saw building a heap in Heapsort (Session 23) — except the input here is already a valid heap of the *other* kind, not a raw unsorted array.** Say this connection out loud immediately; it's the fastest way into the algorithm.
- The **"find the largest" step compares the node against *both* children, not just one** — this generalizes the max-heap `down_heapify` idea from Session 22, just applied uniformly across the whole array from the bottom up.

**Checkpoint (at 17 min)** — cold-call two students:
> *"This procedure — start at the last non-leaf node, downHeapify, move toward the root — sounds identical to something from two sessions ago. What's actually different here?"*
> **Answer:** Nothing about the mechanism is different — the algorithm doesn't know or care whether the input started as a min-heap, a max-heap, or a random array. The only difference is what you're told about the starting array; the same bottom-up `downHeapify` sweep restores max-heap order regardless.

---

## ⚡ Activity 1 — Predict-the-Output: First Swap (19–24 min)

**Format:** Predict-the-Output · **Exposes:** whether students can correctly identify the starting index and the *larger* of two children before the deck confirms it — a common failure point is defaulting to "the left child" out of habit.

**Setup line (say this):**
> *"Min-heap array: 6, 10, 9, 18, 12, 15, 20 — that's Example 2. Before I show the deck's answer: which index does the conversion start at, and what swaps with what, first?"*

**What students do:** Compute the last non-leaf index (`⌊7/2⌋ - 1 = 2`, value `9`), identify its children at indices 5 and 6 (values `15` and `20`), and correctly predict that `20` (index 6) is the larger — so `arr[2]` and `arr[6]` swap.

**How it surfaces:** If someone defaults to comparing only the left child (`15`) and swaps `arr[2]` with `arr[5]` instead, stop and ask: *"You have two children — did you check which one is actually bigger?"*

**Debrief line:**
> *"This step always compares both children and picks the larger one before doing anything. Skip that comparison and you can swap with the wrong child — the array stops looking wrong immediately, but the heap property downstream is still broken."*

**Cut rule:** If running late, just have students name the starting index and skip the child-comparison prediction — state it yourself.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–40 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 13–44: full recursive dry run, pseudocode, complexity (recursive), then the iterative approach's pseudocode and complexity -->
Covers: full recursive dry run on `[6, 10, 9, 18, 12, 15, 20]` — index 2 (swap with index 6, value 20) → index 1 (swap with index 3, value 18) → index 0 / root (swap with index 2, value 20) → recurse back into index 2 (swap with index 5, value 15) → final array `[20, 18, 15, 10, 12, 6, 9]` → recursive pseudocode (`downHeapify` + `minToMaxHeap`) → recursive complexity: `O(n)` time overall (each `downHeapify` call is `O(log n)`, but summed across all non-leaf nodes the total is `O(n)`, same amortized argument as Heapsort's construction phase), `O(log n)` space (recursion stack) → iterative approach (same logic, expressed as a `while` loop instead of recursive calls) → iterative complexity: `O(n)` time, `O(1)` space.

**Beats to emphasise**

- **Narrate the dry run as one continuous sweep, not four separate examples:** index 2, then index 1, then index 0 (root) — and *then*, because the root's swap disturbed the subtree at index 2 again, the algorithm recurses back down into index 2 a second time. That re-entry into index 2 is the step students most often miss — it's not a bug, it's the point of recursing into "the affected subtree."
- **On complexity:** the recursive version's `O(log n)` space comes purely from the call stack — one stack frame per level of recursive depth. The iterative version reaches the *identical* final array with the *identical* time complexity, but replaces the call stack with loop variables, dropping space to `O(1)`.
- Say explicitly: **the two approaches are not two different algorithms** — they produce the exact same sequence of swaps on the exact same input. The only thing that changes is how the "keep going deeper" step is implemented.

**Checkpoint (at 40 min)** — show hands:
> *"Recursive version: O(log n) space. Iterative version: O(1) space. Why does dropping recursion save space here?"*
> **Answer:** The recursion stack holds one frame per level of the `downHeapify` call chain; the iterative version reuses the same loop variables instead of stacking a new frame for every recursive call.

---

## ⚡ Activity 2 — Dry-Run Relay: Finish the Conversion (42–47 min)

**Format:** Live Dry-Run Relay · **Exposes:** whether students correctly recurse into the *swapped-into* subtree instead of stopping after one swap or moving on to an unrelated index.

**Setup line (say this):**
> *"We just swapped index 2 and index 6 — the array is now 6, 10, 20, 18, 12, 15, 9. I will not move another element until you tell me exactly which index to process next, and why."*

**What students do:** Call out, in order: *"Move to index 1 — parent 10, children at index 3 (value 18) and index 4 (value 12) — 18 is larger, swap index 1 and index 3."* Then: *"Move to index 0, the root — parent 6, children at index 1 (value 18) and index 2 (value 20) — 20 is larger, swap index 0 and index 2."* Then, critically: *"That swap just placed 6 at index 2 — does index 2 need to be checked again?"* (Yes — recurse: parent 6 at index 2, children at index 5 (value 15) and index 6 (value 9) — 15 is larger, swap index 2 and index 5.)

**How it surfaces:** If a student stops after the root swap and declares the array done, stop and ask: *"The value you just moved down to index 2 — does it still satisfy the heap property against *its* children?"* Force the re-check.

**Debrief line:**
> *"A swap doesn't end the work at that index — it just relocates the problem one level down. `downHeapify` isn't finished until the node it swapped into either wins against both its children, or has none left to compare against."*

**Cut rule:** If running short, relay only the index-1 and root-level swaps; state the final recursive re-check into index 2 yourself.

---

## Exit Ticket (47–50 min)

> On paper or in chat: *"Min-heap array: 4, 8, 6 (n = 3). Convert it to a max-heap. Which index do you start at, what swap happens, and what's the final array?"*
> **Answer:** Last non-leaf index = `⌊3/2⌋ - 1 = 0` (the root, value `4`). Its children are `arr[1] = 8` and `arr[2] = 6`; the larger is `8`. Swap `arr[0]` and `arr[1]`. Final array: `[8, 4, 6]`. Index 1 is now a leaf, so no further recursion is needed. <!-- placement: inferred exit-ticket array, built to exercise the same start-index and larger-child logic with a minimal 3-element case -->

**Homework:** re-run the full recursive dry run on `[6, 10, 9, 18, 12, 15, 20]` from memory, then re-derive it using the iterative pseudocode instead. <!-- placement: inferred; no homework/practice-unit table exists for this course -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Converting min-heap to max-heap just means reversing or re-sorting the array | Heaps "feel" ordered, so flipping the order seems intuitive | Point out the final array isn't a simple reversal of the input — walk the dry run and show the actual swaps come from comparing each parent against its real children, not from sorting |
| The algorithm needs to know the input was a min-heap to work correctly | It's introduced as "converting a min-heap," which sounds special-cased | State plainly: the exact same `downHeapify`-from-last-non-leaf-node sweep works on *any* array — min-heap, max-heap, or fully random. The starting condition is flavor, not a precondition the algorithm checks |
| The larger child is always the left child by default | Left-first habit from reading arrays left to right | Activity 1 — force students to compare both children explicitly before naming the swap target |
| A swap ends the work at that index | Earlier problems (e.g., Max Heap Validation) had single-step, non-recursive comparisons | Activity 2's relay — force students to re-check the node that was just swapped *into* against its own children before declaring the conversion done |
| The recursive and iterative approaches can produce different results | Different code structure (function calls vs. a loop) looks like a different algorithm | State explicitly, with the dry run as evidence: both approaches perform the identical sequence of swaps on the identical input — only the space complexity differs |

---

## Instructor Notes

- **The recursive re-entry into an already-processed index (index 2, twice, in the main dry run) is the single hardest idea in this session.** Slow down specifically at that transition in Slide Block B and again in Activity 2 — it's the point students are most likely to silently misunderstand.
- **Don't let "recursive vs. iterative" become a code-syntax lecture.** The complexity contrast (`O(log n)` vs `O(1)` space) is the entire pedagogical point — keep returning to that, not to line-by-line code differences.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- This session's algorithm — sweep from the last non-leaf node to the root, `downHeapify` at each — is the third time students have seen this exact pattern (Heapsort's construction phase, and now heap-type conversion). If a student is still lost here, the gap is likely in Session 23, not this session — consider a 2-minute callback to the Heapsort construction dry run before pressing on.
