# Session 26 — Kth Largest Element in an Array

**Duration** 45 min <!-- placement: inferred — standard length, two approaches (sort + min-heap) each with a full dry run --> · **Topic** Heaps · **Prerequisite** Convert Min Heap to Max Heap (Session 25)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Kth Largest Element in an Array | https://docs.google.com/presentation/d/18-3vA-8s-hdRptCJ4J117eZWSj_u4un1lBDOZqgOhdw/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given an array and integer `k`, return the `k`-th largest element. *(REMEMBERING)*
2. Explain the brute-force approach — sort descending, read index `k-1` — and its `O(n log n)` cost. *(UNDERSTANDING)*
3. Trace the optimal min-heap approach: push every element, and whenever heap size exceeds `k`, pop the smallest — so the heap always holds exactly the `k` largest elements seen so far. *(APPLYING)*
4. Explain why the heap must be a **min**-heap (not a max-heap) for this specific problem, and why its top is the answer once all elements are processed. *(ANALYZING)*
5. Contrast `O(n log n)` / `O(log n)` (sort) against `O(n log k)` / `O(k)` (heap), and identify when the heap approach wins — namely when `k` is small relative to `n`. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 25 (Convert Min Heap to Max Heap) (0–6 min)

Say: *"Six on yesterday's conversion problem, then we put the min-heap to direct use instead of just fixing one."*

**Q1.** The min-to-max-heap conversion starts at:
`A` The root · `B` The last non-leaf node · `C` The last leaf · `D` Index 1
→ **Answer:** B.

**Q2.** At each node during the conversion, the algorithm compares the node against:
`A` Only its left child · `B` Only its right child · `C` Both children, picking the larger · `D` Every node in the array

**Q3.** When a swap happens during the conversion, what must happen next?
`A` Nothing, move to the next index · `B` Recurse into the subtree that was just swapped into, since it may now violate the property · `C` Restart the whole array from index 0 · `D` Swap back immediately

**Q4.** Time complexity of the min-to-max-heap conversion (both recursive and iterative)?
`A` `O(log n)` · `B` `O(n)` · `C` `O(n log n)` · `D` `O(n²)`
→ **Answer:** B.

**Q5.** The iterative version beats the recursive version specifically on:
`A` Time complexity · `B` Space complexity — `O(1)` vs `O(log n)` · `C` Correctness · `D` It doesn't — they're identical in every way
→ **Answer:** B.

**Q6 (MSQ — pick all correct).** Which are true of a min-heap?
`A` The smallest element is always at the root · `B` Every parent is smaller than or equal to both its children · `C` It is always sorted left to right · `D` Removing the root always costs `O(log n)`

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"You have an array of a million numbers, and someone asks for just the 5th largest. Do you really need to sort all one million of them to answer that?"*

Let students react (most will say "no, that seems wasteful"). Then:

> *"Sorting the whole array works, but it does more work than the question actually needs — it fully orders every element when you only care about one position from the top. Today: a brute-force sort-based answer, and then a min-heap trick that only tracks the `k` largest elements it has seen, throwing everything else away as it goes."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–20: Problem Statement, Examples, Brute Force Approach, Dry Run, Pseudocode, Complexity, Code -->
Covers: problem statement (return the `k`-th largest element in `arr`) → Example 1 (`arr = [3,2,1,5,6,4]`, `k=2` → `5`) → Example 2 (`arr = [3,2,3,1,2,4,5,5,6]`, `k=4` → `4`) → brute force: sort descending, return `arr[k-1]` → dry run sorting `[3,2,3,1,2,4,5,5,6]` to `[6,5,5,4,3,3,2,2,1]`, reading index `k-1=3` → `4` → pseudocode → complexity (`O(n log n)` time from the sort, `O(log n)` space for the sort's recursion stack) → C++/Python code.

**Beats to emphasise**

- State the brute force in one line: *"sort descending, and the answer is sitting at index `k-1` — no searching needed once it's sorted."*
- **Say explicitly why this is wasteful:** *"sorting orders every single element, but the question only ever asks about one specific position. All that ordering work below and above position `k-1` is thrown away unused."*
- Complexity is entirely the sort's cost — reading the index afterward is `O(1)`, so the whole approach's cost is exactly the cost of sorting.

**Checkpoint (at 17 min)** — cold-call:
> *"After sorting `[3,2,3,1,2,4,5,5,6]` descending, why is index `k-1` — not index `k` — the right one to read for the `k`-th largest?"*
> **Answer:** 0-based indexing: the largest element sits at index `0`, so the `k`-th largest sits at index `k-1`. For `k=4`, that's index `3`, value `4`.

---

## Slide Block B (17–26 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 21–38: Optimal Solution (min-heap), full Dry Run, Pseudocode, Complexity, Code -->
Covers: optimal approach — use a min-heap; push each element; if heap size exceeds `k`, pop the smallest (the root) → full dry run on `[3,2,3,1,2,4,5,5,6]`, `k=4`, inserting one element at a time and popping whenever size exceeds 4, ending with heap `{4,5,5,6}` and top `4` → pseudocode → complexity (`O(n log k)` time, `O(k)` space) → C++/Python code.

**Beats to emphasise**

- **Say the mechanism as one sentence, this is the whole session:** *"push everything in, but the instant the heap grows past size `k`, throw away the smallest thing in it — so the heap only ever holds the `k` largest survivors seen so far."*
- **Narrate why it's a min-heap, not a max-heap** — this is the detail students most often get backwards: *"we need fast access to the smallest of the `k` largest, because that's exactly the thing we discard the moment a bigger candidate shows up. A max-heap gives you fast access to the biggest — which is the wrong end for this eviction rule."*
- Walk the dry run exactly in the deck's own insertion order — `3, 2, 3, 1, 2, 4, 5, 5, 6` — narrating each eviction as it happens once the heap passes size 4, and confirm the final four survivors are `4, 5, 5, 6`.
- Complexity: `n` insertions each costing `O(log k)` since the heap never exceeds size `k` — that's the whole saving versus sorting all `n` elements.

**Checkpoint (at 26 min)** — cold-call:
> *"Why is the heap in this approach a min-heap, when we're looking for the *largest* elements?"*
> **Answer:** The heap holds the `k` largest elements found so far, and the operation we need repeatedly is "find and remove the smallest of those `k`" — that's exactly what a min-heap's root gives in `O(log k)`, every time a bigger candidate needs to evict the current weakest survivor.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Evict the Smallest" (31–37 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can correctly identify when the heap exceeds size `k` and correctly name the element that gets evicted, rather than only having watched the deck's trace.

**Setup line (say this):**
> *"Fresh min-heap, `k = 3`. Sequence: insert `7, 2, 9, 4, 1`. After each insert, tell me the heap's contents and whether anything gets evicted — before I confirm."*

Run **one insert at a time**:

```
insert 7 → heap {7}                              size 1 ≤ 3, no eviction
insert 2 → heap {2,7}                             size 2 ≤ 3, no eviction
insert 9 → heap {2,7,9}                           size 3 ≤ 3, no eviction
insert 4 → heap {2,7,9,4} → size 4 > 3 → evict smallest (2) → heap {4,7,9}
insert 1 → heap {1,4,7,9} → size 4 > 3 → evict smallest (1) → heap {4,7,9}
```

**How it surfaces:** At `insert 1`, ask before revealing: *"Does `1` even survive long enough to matter?"* Correct: it's inserted, immediately becomes the smallest, and is evicted right back out on the very next size check — the heap ends unchanged at `{4,7,9}`.

**Debrief line:**
> *"Final heap `{4,7,9}`, top (smallest of these) is `4` — that's the 3rd largest of everything we inserted. Every element that couldn't beat the current weakest survivor gets discarded immediately, which is exactly why this never needs to look at more than `k` elements at once."*

**Cut rule:** If running short, stop after `insert 4` — one eviction is enough to demonstrate the mechanism; `insert 1`'s immediate eviction is confirmation, not new information.

---

## ⚡ Activity 2 — Predict & Discuss: "When Does the Heap Actually Win?" (37–42 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students understand the trade-off is about the *size of `k`*, not a blanket "heap is always better."

**Setup line (say this):**
> *"Sorting costs `O(n log n)` no matter what `k` is. The heap costs `O(n log k)`. For an array of a million elements, when does that difference actually matter — and when doesn't it?"*

**What students do:** Discuss for a minute, then share out.

**Answer:** When `k` is small (say, `k=5` out of a million), `log k` is tiny compared to `log n` — the heap approach does dramatically less work. When `k` is close to `n` (e.g., `k = n/2`), `log k` and `log n` are nearly the same, so the heap's advantage shrinks toward nothing — at that point, sorting is just as reasonable and far simpler to write.

**How it surfaces:** Ask a follow-up: *"So is the heap approach unconditionally 'the better solution'?"* Push toward: no — it's the better solution specifically when `k` is small relative to `n`; complexity trade-offs are about the actual input shape, not a fixed ranking of approaches.

**Debrief line:**
> *"'Optimal' always means optimal *for a stated shape of input*. Say what `k` and `n` actually look like before declaring a winner."*

**Cut rule:** If running short, state the `k`-small-vs-`k`-large trade-off directly and skip the open discussion.

---

## Exit Ticket (42–45 min)

> `arr = [9, 3, 7, 1, 8]`, `k = 2`. Using the min-heap approach, name every eviction that happens and the final answer.
> **Answer:** insert `9` → `{9}`; insert `3` → `{3,9}`; insert `7` → `{3,9,7}` size 3 > 2 → evict smallest `3` → `{9,7}`; insert `1` → `{9,7,1}` size 3 > 2 → evict smallest `1` → `{9,7}`; insert `8` → `{9,7,8}` size 3 > 2 → evict smallest `7` → `{9,8}`. Final heap `{9,8}`, top (smallest of these) = `8` = the 2nd largest element. <!-- placement: inferred exit-ticket array, built to exercise the eviction rule across a full sequence -->

**Homework:** trace the min-heap approach on `arr = [5, 12, 8, 3, 15, 9]`, `k = 3`, listing every eviction. <!-- placement: inferred — no homework/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A max-heap should be used to find the `k` largest elements | "Largest" intuitively suggests max-heap | Slide Block B — explicitly reasoning that the needed operation is "find the smallest of the current top-`k`," which is a min-heap's specialty |
| The heap must hold all `n` elements before an answer can be read | Sorting-based intuition, where the whole array is touched before the answer is available | Activity 1 — the heap never grows past size `k`, evicting immediately whenever it would |
| The heap approach is unconditionally faster than sorting | `O(n log k)` looks better than `O(n log n)` on paper without considering that `k` could be close to `n` | Activity 2 — explicit discussion of when `k` is small vs. when it approaches `n` |
| Once an element is evicted, it means the algorithm made a mistake | Eviction feels like "undoing work" rather than a deliberate part of the algorithm | Activity 1 — frame every eviction as the algorithm correctly discarding an element that can no longer be among the top `k` |
| The final answer is the heap's *largest* remaining element | Reasonable-sounding confusion given the heap holds "the largest elements" | State plainly: the answer is the heap's *smallest* remaining element (its root) — that's the weakest member of the surviving top-`k`, which is exactly the `k`-th largest overall |

---

## Instructor Notes

- **The min-heap-not-max-heap detail is the single most important thing to nail down this session.** Don't let it pass as a throwaway line — Activity 1's checkpoint exists specifically to force students to justify it themselves.
- **This is the first session in the block that reuses the heap purely as a tool for another problem**, rather than teaching heap mechanics directly — frame it that way explicitly: "we're not learning anything new about heaps today, we're applying what you already know."
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- **Bridge to Session 27 at the close:** "Today we tracked the top `k` of a single array with a heap. Next session, we merge `k` *entire arrays* together — same tool, a different job for it."
