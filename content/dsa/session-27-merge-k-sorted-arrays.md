# Session 27 — Merge K Sorted Arrays

**Duration** 50 min <!-- placement: inferred — above default because the optimal approach tracks (element, row, column) triples in the heap, a genuinely fiddlier bookkeeping step than prior heap sessions --> · **Topic** Heaps · **Prerequisite** Kth Largest Element in an Array (Session 26)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Merge K Sorted Arrays | https://docs.google.com/presentation/d/1Bu6HdP47N4RTkNWi_ud30zeqyyCN79sKaEAAeMY7ACM/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given `k` sorted arrays (a `k × k` matrix), merge them into one fully sorted array. *(REMEMBERING)*
2. Explain the brute-force approach — flatten every array into one list, then sort — and its `O(k² log k²)` cost. *(UNDERSTANDING)*
3. Trace the optimal min-heap approach: seed the heap with the first element of every row, then repeatedly pop the smallest and push the next element from that same row. *(APPLYING)*
4. Explain why each heap entry must carry its row and column indices, not just its value — this is what lets the algorithm find "the next element from the same row" after a pop. *(ANALYZING)*
5. Contrast `O(k² log k²)` / `O(k²)` (brute force) against `O(k² log k)` / `O(k + k²)` (heap), and explain where the `log k` vs `log k²` saving actually comes from. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 26 (Kth Largest Element in an Array) (0–6 min)

Say: *"Six on yesterday's top-k heap trick, then we scale the same idea up to merging entire arrays instead of tracking single elements."*

**Q1.** In the optimal Kth-Largest solution, the heap used is a:
`A` Max-heap · `B` Min-heap · `C` Either works identically · `D` Neither — a plain array is used
→ **Answer:** B.

**Q2.** The eviction rule in that heap is: whenever the heap's size exceeds `k`, remove:
`A` The most recently added element · `B` The largest element · `C` The smallest element · `D` A random element
→ **Answer:** C.

**Q3.** After processing every element, the answer (`k`-th largest) is found at:
`A` The heap's smallest remaining element (its root) · `B` The heap's largest remaining element · `C` The first element inserted · `D` The last element inserted

**Q4.** Time complexity of the min-heap approach to Kth Largest Element?
`A` `O(n log n)` · `B` `O(n log k)` · `C` `O(n)` · `D` `O(k log k)`
→ **Answer:** B.

**Q5.** The heap-based approach beats sorting the whole array specifically when:
`A` `k` is close to `n` · `B` `k` is small relative to `n` · `C` The array is already sorted · `D` It never beats sorting

**Q6 (MSQ — pick all correct).** Which are true of the min-heap in that session?
`A` It never holds more than `k` elements after the first `k` insertions · `B` Its root is always the current smallest of the surviving top-`k` · `C` It must be rebuilt from scratch for every insertion · `D` Push and pop each cost `O(log k)`

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"You have `k` different sorted playlists, and you want one single sorted playlist out of all of them combined. Do you have to dump every song into one giant list and re-sort everything from scratch, when each individual playlist was already in order?"*

Let students react. Then:

> *"No — and today's heap trick is exactly about not throwing away work that's already done. Each array arrives pre-sorted; the only real question at every step is 'which array currently has the smallest next candidate?' A min-heap answers that question in `O(log k)`, repeatedly, without ever re-sorting anything that was already sorted."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–23: Problem Statement, Examples, Brute Force Approach, Dry Run, Pseudocode, Complexity, Code -->
Covers: problem statement (`k` sorted arrays, each length `k`, arranged as a `k × k` matrix; merge into one sorted array) → Example 1 (`[[1,2,3],[10,15,17],[5,9,11]]` → `[1,2,3,5,9,10,11,15,17]`) → Example 2 (empty input → empty output) → brute force: flatten all arrays into one list, then sort → dry run: traverse each row in turn appending to `answer`, then sort the fully flattened result → pseudocode → complexity (`O(k² + k² log k²)` = `O(k² log k²)` time — flattening is `O(k²)`, dominated by the sort; `O(k²)` space for the flattened array) → C++/Python code.

**Beats to emphasise**

- State the brute force in one line: *"dump every row into one big list, then sort that list once — simple, but it throws away the fact that each row already arrived in order."*
- **Say explicitly what's wasted:** *"the sort has to re-discover ordering information that already existed inside each row — it treats the flattened list as if it were completely random."*
- Complexity: flattening costs `O(k²)` (total elements across all rows), and the sort of that flattened list costs `O(k² log k²)` — the sort dominates.

**Checkpoint (at 18 min)** — cold-call:
> *"What information does the brute-force sort throw away that the input already gave us for free?"*
> **Answer:** Each individual row was already sorted — the brute force ignores that entirely and re-sorts the fully flattened, unordered-looking list from scratch.

---

## Slide Block B (18–29 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 24–54: Optimal Approach, full Dry Run, Pseudocode, Complexity, Code -->
Covers: optimal approach — min-heap seeded with the first element of every row, each entry tagged `(value, (row, column))`; repeatedly pop the smallest, append it to the answer, and if that row has a next element, push it into the heap → full dry run on `[[1,2,3],[10,15,17],[5,9,11]]`: seed heap with `(1,(0,0))`, `(10,(1,0))`, `(5,(2,0))`; pop `1`, push `(2,(0,1))`; pop `2`, push `(3,(0,2))`; pop `3` (row 0 exhausted, nothing pushed); pop `5`, push `(9,(2,1))`; pop `9`, push `(11,(2,2))`; pop `10`, push `(15,(1,1))`; pop `11` (row 2 exhausted); pop `15`, push `(17,(1,2))`; pop `17` — final answer `[1,2,3,5,9,10,11,15,17]` → pseudocode → complexity (`O(k log k)` to seed the heap with the first `k` elements, `O(k² log k)` to process the remaining `k²` push/pop pairs, overall `O(k log k + k² log k)` = `O(k² log k)` time; `O(k)` for the heap plus `O(k²)` for the result array, overall `O(k + k²)` space) → C++/Python code.

**Beats to emphasise**

- **Say the mechanism as one sentence, this is the whole session:** *"seed the heap with one candidate from each row, then every time you pop the smallest, immediately refill from that same row — the heap always holds exactly one live candidate per row that still has elements left."*
- **Narrate why each heap entry needs `(row, column)`, not just the value** — this is the detail students most often skip: *"once you pop a value, you need to know exactly which row and which position it came from, so you can find that row's *next* element to push in its place. The value alone doesn't tell you that."*
- Walk the "row exhausted" case explicitly, at least twice in the dry run: after popping `3` from row 0, there's no next element in that row, so nothing gets pushed — the heap simply shrinks by one live candidate for the rest of the run.
- Complexity: contrast `k² log k` here against `k² log k²` for brute force — note `log k²` = `2 log k`, so the heap approach is a real, not just cosmetic, factor-of-2-in-the-exponent improvement, on top of avoiding a full independent sort.

**Checkpoint (at 29 min)** — cold-call:
> *"After popping the smallest element from the heap, how does the algorithm know what to push back in?"*
> **Answer:** Every heap entry carries the `(row, column)` it came from; after popping, the algorithm checks if `column + 1` is still within that row's bounds, and if so, pushes `(row, column + 1)`'s value next.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Refill the Row" (34–41 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can track which row a popped value came from and correctly identify what refills the heap — the one genuinely new mental model versus Session 26's single-array heap.

**Setup line (say this):**
> *"Two sorted rows: Row 0 = `[4, 8]`, Row 1 = `[2, 6]`. Seed the heap with the first element of each row. After each pop, tell me what gets pushed back in, and from where — before I confirm."*

Run **one pop at a time**:

```
Seed: heap = {(4,(0,0)), (2,(1,0))}
pop 2 (row 1, col 0) → answer=[2] → row 1 has col 1 → push (6,(1,1))    heap = {(4,(0,0)), (6,(1,1))}
pop 4 (row 0, col 0) → answer=[2,4] → row 0 has col 1 → push (8,(0,1))  heap = {(8,(0,1)), (6,(1,1))}
pop 6 (row 1, col 1) → answer=[2,4,6] → row 1 exhausted → push nothing  heap = {(8,(0,1))}
pop 8 (row 0, col 1) → answer=[2,4,6,8] → row 0 exhausted → heap empty
```

**How it surfaces:** After the third pop, ask before revealing: *"Row 1's `6` just got popped — does anything get pushed back in?"* Correct: no — row 1 only had two elements, columns 0 and 1, and column 1 was the last one; there's nothing left in that row to refill with.

**Debrief line:**
> *"Final answer `[2, 4, 6, 8]` — fully merged, fully sorted, and at every step the heap held exactly one live candidate per row that still had elements remaining. Rows don't all finish at the same time, and the algorithm has to handle that gracefully, not assume every row survives equally long."*

**Cut rule:** If running short, do just the first two pops — one live refill is enough to demonstrate the mechanism; the exhausted-row case can be stated rather than relayed.

---

## ⚡ Activity 2 — Predict & Discuss: "What If a Row Is Longer Than the Others?" (41–47 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students understand the algorithm generalizes beyond the "all rows length `k`" square-matrix framing the deck uses, since real inputs won't always be so tidy.

**Setup line (say this):**
> *"The deck's examples are always a `k × k` square — every row the same length. What actually breaks if one row has 10 elements and another has only 2?"*

**What students do:** Discuss for a minute, then share out.

**Answer:** Nothing breaks. The algorithm never assumes rows are equal length — the only check it ever performs is "does this row have a next column after the one I just popped?" A shorter row simply stops contributing candidates sooner and the heap naturally shrinks; a longer row keeps contributing until its own elements run out.

**How it surfaces:** Ask a follow-up: *"So why does the deck always use a square `k × k` matrix?"* Push toward: it's a simplifying assumption for teaching clarity — the real algorithm only cares about "sorted rows, some finite number of them," not equal lengths.

**Debrief line:**
> *"Whenever a problem statement adds a constraint like 'all rows the same length,' ask whether the algorithm actually *needs* that constraint, or whether it's just making the examples easier to draw. Here, the algorithm doesn't need it at all."*

**Cut rule:** If running short, state the generalization directly and skip the open discussion.

---

## Exit Ticket (47–50 min)

> Three sorted rows: `[1, 9]`, `[2, 3]`, `[5]`. Using the min-heap approach, trace every pop and the final merged array.
> **Answer:** Seed: `{(1,(0,0)), (2,(1,0)), (5,(2,0))}`. Pop `1` → push `(9,(0,1))` → `{(2,(1,0)),(5,(2,0)),(9,(0,1))}`. Pop `2` → push `(3,(1,1))` → `{(3,(1,1)),(5,(2,0)),(9,(0,1))}`. Pop `3` → row 1 exhausted → `{(5,(2,0)),(9,(0,1))}`. Pop `5` → row 2 exhausted → `{(9,(0,1))}`. Pop `9` → row 0 exhausted → heap empty. Final: `[1, 2, 3, 5, 9]`. <!-- placement: inferred exit-ticket rows, built with unequal lengths to exercise the exhausted-row case Activity 2 discusses -->

**Homework:** trace the min-heap merge on rows `[3, 7, 20]`, `[4]`, `[1, 5, 6, 8]`, listing every push and pop in order. <!-- placement: inferred — no homework/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The heap only needs to store values, not their row/column origin | Session 26's heap only ever tracked plain values | Slide Block B — explicitly showing that without `(row, column)`, there's no way to find "the next element from this same row" after a pop |
| All rows must be the same length for this algorithm to work | The deck's `k × k` square matrix framing | Activity 2 — reasoning through unequal row lengths and showing nothing in the algorithm depends on equal length |
| A row that runs out early is an error case that needs special handling | "Exhausted row" sounds like a failure state | Activity 1 — treating an exhausted row as an entirely normal, expected outcome: the heap simply shrinks by one candidate |
| This approach re-sorts anything | The word "merge" sounds adjacent to "sort" | State plainly: every row arrives already sorted, and the heap never reorders elements *within* a row — it only ever decides the relative order *across* rows, one pop at a time |
| `O(k² log k)` and `O(k² log k²)` are basically the same thing | Both have `k²` and `log` in them, so they look similar at a glance | Slide Block B — note `log k² = 2 log k`, a genuine (if modest) improvement layered on top of avoiding an entirely separate full sort of the flattened data |

---

## Instructor Notes

- **The `(row, column)` bookkeeping is this session's real hurdle, not the heap mechanics themselves.** Students already know min-heap eviction from Session 26 — spend the marginal time here on *why* each entry needs origin metadata, not on re-teaching heap operations from scratch.
- **Activity 1's exhausted-row moment is the load-bearing beat.** Real inputs won't always be tidy squares; don't let that case pass without an explicit pause.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- **Bridge to Session 28 at the close:** "Today's heap tracked one 'best candidate per row.' Next session, the heap tracks 'best candidate per unique element's frequency' — same tool, another shape of problem entirely."
