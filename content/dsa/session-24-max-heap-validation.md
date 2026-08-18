# Session 24 — Max Heap Validation

**Duration** 45 min · **Topic** Heaps · **Prerequisite** Heapsort Algorithm (Session 23)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Max Heap Validation | https://docs.google.com/presentation/d/1xVH2Yp1h2XQSPjVOld9awEcY5FyjNEXi250uOaFxKpc/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the Max Heap Validation problem: given a level-order array, decide whether it satisfies the max-heap property. *(REMEMBERING)*
2. Explain why only non-leaf nodes (indices `0` to `n/2 - 1`) need to be checked, and why one violation anywhere is enough to return false. *(UNDERSTANDING)*
3. Trace the validation loop on a given array, checking each non-leaf node's children against it in order. *(APPLYING)*
4. Identify the exact index and comparison where a given array violates the max-heap property. *(ANALYZING)*
5. State the time and space complexity of the validation approach — `O(n)` time, `O(1)` space. *(REMEMBERING)*

---

## Warm-Up Poll — Retrieval Practice on Session 23: Heapsort Algorithm (0–6 min)

Say: *"Seven on Heapsort before we move to today's problem — validating a heap, rather than building or sorting with one."*

**Q1.** Heapsort has how many main phases, and what are they?
`A` One — just sorting · `B` Two — heap construction, then sorting · `C` Three — build, merge, sort · `D` Four
→ **Answer:** B.

**Q2.** Which heap type do you build for ascending-order output?
`A` Min heap · `B` Max heap · `C` Either works identically · `D` A binary search tree
→ **Answer:** B.

**Q3.** Where does heap construction start?
`A` The root · `B` The last non-leaf node, working backward toward the root · `C` A random leaf · `D` The last element in the array
→ **Answer:** B.

**Q4.** *(MSQ — pick 2)* Which are true about construction's `O(n)` complexity?
`A` Most nodes are near the leaves and require little work · `B` Every node bubbles the full height of the tree · `C` Only nodes near the root can move far · `D` Construction is `O(n log n)` regardless of where you start
→ **Answer:** A and C.

**Q5.** In the sorting phase, what happens to the current maximum?
`A` It's deleted permanently · `B` It's swapped to the last live slot of the shrinking heap · `C` It's moved to a separate output array · `D` It's left at the root
→ **Answer:** B.

**Q6.** Overall time complexity of Heapsort?
`A` `O(n)` · `B` `O(n log n)` · `C` `O(log n)` · `D` `O(n²)`
→ **Answer:** B.

**Q7.** Auxiliary space complexity of Heapsort?
`A` `O(n)` · `B` `O(log n)` · `C` `O(1)` · `D` `O(n log n)`
→ **Answer:** C.

**Running it** — poll tool, ~35–40 s/question. Total 6 min including reads.

---

## Hook (6–8 min)

Say: *"Different kind of question today. Someone hands you a raw array and says, 'trust me, this is a valid max heap.' No tree drawn, just numbers in a row. How do you check they're telling the truth — without rebuilding the whole thing from scratch?"*

Take one or two guesses, then: *"That's today's entire problem. You already know exactly what a max heap requires — every parent at least as big as its children. Today you just check it, index by index."*

---

## Slide Block A (8–16 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 4–8: Problem Statement, Example 1 (valid), Example 2 (invalid), Approach overview -->
Covers: problem statement (given array `arr` of size `n`, does it represent a valid level-order max heap?) → Example 1: `[50, 30, 40, 10, 5, 20, 35, 2, 1]` → `true` → Example 2: `[60, 40, 20, 10, 45, 35, 15, 5, 25]` → `false`, because `arr[1] = 40` has child `arr[4] = 45`, which is greater than its parent → Approach overview (iterate non-leaf nodes; check both children; any violation → false).

**Beats to emphasise**

- **This is a validation problem, not a construction problem.** Nothing gets built or fixed — the array either already satisfies the property or it doesn't.
- **Walk Example 2 slowly.** It's the whole lesson in one slide: one bad pair, anywhere in the array, is enough to make the entire array invalid. Point at the exact indices — `arr[1]` and `arr[4]` — not just "40 and 45."

**Checkpoint (at 16 min)** — cold-call two students:
> *"In Example 2, which exact index pair breaks the max-heap property?"*
> **Answer:** `arr[1] = 40` (parent) and `arr[4] = 45` (its child) — the child is greater than the parent.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (23–32 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 8–24: full approach, dry run on Example 1's array across 4 iterations, pseudocode, complexity analysis, code implementation -->
Covers: approach detail (loop `i` from `0` to `n/2 - 1`; for each, compute `leftChildIndex = 2i+1`, `rightChildIndex = 2i+2`; if either child exists and is greater than `arr[i]`, return `false`; otherwise return `true` after the loop) → full dry run on `[50, 30, 40, 10, 5, 20, 35, 2, 1]` across 4 iterations (`i = 0`: parent 50, children 30/40, valid; `i = 1`: parent 30, children 10/5, valid; `i = 2`: parent 40, children 20/35, valid; `i = 3`: parent 10, children 2/1, valid → all pass, returns `true`) → pseudocode → complexity: `O(n)` time (loop runs over ~`n/2` non-leaf nodes, constant work each), `O(1)` space (no extra structures or recursion) → C++ and Python code.

**Beats to emphasise**

- **Only non-leaf nodes get checked** — the loop bound `n/2 - 1` is not arbitrary. Leaves have no children, so there's nothing to violate; checking them would be wasted work.
- **The loop stops the instant it finds a violation.** It does not count violations or keep checking after the first `false` — this is a short-circuit check, not an audit.
- On the code slides: point out the explicit bounds checks (`leftChildIndex < n`, `rightChildIndex < n`) — they exist because the last non-leaf node in an even-sized array might have only a left child, no right child.

**Checkpoint (at 32 min)** — show hands:
> *"Why do we only need to check indices `0` through `n/2 - 1`, not every index in the array?"*
> **Answer:** Every index past `n/2 - 1` is a leaf — it has no children, so there's nothing left to check against.

---

## ⚡ Activity 1 — Spot the Bug (34–39 min)

**Format:** Spot the Bug · **Exposes:** whether students can find the *exact* failing index and comparison, versus vaguely sensing "something's off," and whether they stop checking the instant a violation is found rather than continuing past it.

**Setup line (say this):**
> *"Run the validation loop yourself on this array, iteration by iteration: 60, 40, 20, 10, 45, 35, 15, 5, 25. Tell me the exact iteration index and the exact comparison where it fails — not just 'it's invalid.'"*

**What students do:** Trace `i = 0` (parent `60`, children `40`/`20` — both smaller, valid), then `i = 1` (parent `40`, children `10`/`45` — `45 > 40`, violation). They should stop there and declare `false` at `i = 1`.

**How it surfaces:** If a student declares the array invalid without naming the index and comparison, push: *"Which parent, which child, what are the actual numbers?"* If a student keeps checking `i = 2` and `i = 3` after finding the violation at `i = 1`, stop them: *"The function already returned. What's the point of checking further?"*

**Debrief line:**
> *"One bad pair, anywhere below the root, is enough to invalidate the whole array. You don't fix it, you don't count how many violations exist — you find the first one and you're done."*

**Cut rule:** If running late, give the array already split by iteration on the board and just ask students to point to the failing one.

---

## ⚡ Activity 2 — Predict-the-Output: Trace the Code (39–43 min)

**Format:** Predict-the-Output (code reading) · **Exposes:** whether students can map the tree-diagram understanding from Slide Block A onto the actual index arithmetic in real code, rather than only reasoning visually.

**Setup line (say this):**
> *"Here's the real Python function from the deck. No tree diagram this time — just the code. Trace it line by line for i = 0 through i = 3 on the array 50, 30, 40, 10, 5, 20, 35, 2, 1, and tell me what it returns before I run it."*

```python
def maxHeapValidation(arr):
    n = len(arr)
    for i in range(n // 2):
        leftChildIndex = 2 * i + 1
        if leftChildIndex < n and arr[leftChildIndex] > arr[i]:
            return False
        rightChildIndex = 2 * i + 2
        if rightChildIndex < n and arr[rightChildIndex] > arr[i]:
            return False
    return True
```

**What students do:** Trace each loop iteration against the array, computing `leftChildIndex` and `rightChildIndex` and comparing values, arriving at `True` after all 4 iterations pass.

**How it surfaces:** If students compute `n // 2` as `3` instead of `4` (for `n = 9`), stop and recompute together — `9 // 2 = 4`, so `i` runs `0, 1, 2, 3`, matching the 4 iterations from the earlier dry run exactly.

**Debrief line:**
> *"Same answer as the tree-diagram dry run — because it's the same algorithm. Reading code and tracing a diagram should always agree; if they don't, one of your two mental models is wrong."*

**Cut rule:** If running short, trace only `i = 0` and `i = 1` aloud and state the remaining two iterations pass without full tracing.

---

## Exit Ticket (43–45 min)

> On paper or in chat: *"Array: 30, 20, 25, 10, 5. Is this a valid max heap? Name the exact index and comparison that decides it."*
> **Answer:** `n = 5`, so check `i = 0` and `i = 1`. `i = 0`: parent `30`, children `arr[1]=20` and `arr[2]=25` — both smaller, valid. `i = 1`: parent `20`, children `arr[3]=10` and `arr[4]=5` — both smaller, valid. No violations found → `True`, it is a valid max heap. <!-- placement: inferred exit-ticket array, built to require the same iteration/bounds logic taught in the session -->

**Homework:** re-attempt Activity 1's dry run (the invalid array) from memory, then invent one array of your own that fails at `i = 0` instead of `i = 1`. <!-- placement: inferred; no homework/practice-unit table exists for this course -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Validating a heap means comparing adjacent array elements (`arr[i]` vs `arr[i+1]`) | Most "check the array" problems students have seen scan adjacent pairs | Point at the index formulas explicitly — the comparison is always parent (`i`) vs. children (`2i+1`, `2i+2`), never adjacent positions |
| Finding one violation means you should "fix" it and keep checking | The very next session (Convert Min Heap to Max Heap) is about fixing a heap, and it's easy to blur the two problems | This problem only answers true/false — say explicitly, "we are not repairing anything today," and return `false` immediately on the first violation |
| You must check the right child even when the left child index is already out of bounds | Habit of always checking "both children" without considering array length | Point at the explicit `< n` bounds checks in the pseudocode/code — a node can have zero, one, or two children depending on array size |
| Every index from `0` to `n-1` needs to be checked | Generalizing "scan the whole array" from other array problems | Leaves (indices `n/2` and above) have no children — nothing to check against — so the loop only runs to `n/2 - 1` |
| Validation should be `O(log n)`, like a single heap operation | Insert/extractMax from Sessions 21–22 were both `O(log n)`, so students assume everything heap-related is that cheap | Validation touches *every* non-leaf node once — it's a full-array scan, `O(n)`, not a single root-to-leaf path |

---

## Instructor Notes

- **This is a short, tight session** — the deck itself is under 30 slides and the algorithm is a single loop. Resist the urge to pad; let the two activities carry the engagement instead of over-narrating slides.
- **Sequencing note:** this session validates a heap; the next session (Convert Min Heap to Max Heap) repairs one. Say this connection explicitly at the end of class — it previews why "no fixing today" mattered.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- Keep the bounds-check detail (`leftChildIndex < n`) visible on the board through Activity 2 — it's a one-line detail that's easy to skip past on a fast read of the code but is exactly what breaks on odd-vs-even-sized arrays if students copy the logic carelessly later.
