# Session 28 — Top K Frequent Elements

**Duration** 45 min <!-- placement: inferred — standard length, closes out the Heaps block with a problem that directly reuses Session 26's eviction pattern on a new key (frequency instead of value) --> · **Topic** Heaps · **Prerequisite** Merge K Sorted Arrays (Session 27)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Top K Frequent Elements | https://docs.google.com/presentation/d/1hovPC3jNwqfVBpccCOTNiUTWoaq6wPi1MPN-kxUkiIE/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given an array and integer `k`, return the `k` most frequently occurring elements. *(REMEMBERING)*
2. Explain the brute-force approach — count frequencies, build `(frequency, element)` pairs, sort descending, take the first `k`. *(UNDERSTANDING)*
3. Trace the optimal min-heap approach: build the frequency map first, then push `(frequency, element)` pairs into a min-heap, evicting the smallest-frequency pair whenever size exceeds `k`. *(APPLYING)*
4. Explain why this problem's heap orders by **frequency**, not by the element's own value — directly contrasting with Session 26 where the heap ordered by value. *(ANALYZING)*
5. Contrast `O(n log n)` (brute force, dominated by sorting unique elements) against `O(n log k)` (heap), and state why counting frequencies first is unavoidable in both approaches. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 27 (Merge K Sorted Arrays) (0–6 min)

Say: *"Six on yesterday's k-way merge, then we swap out what the heap is keyed on."*

**Q1.** In the optimal Merge K Sorted Arrays solution, what does each heap entry store?
`A` Just the value · `B` The value plus its row and column · `C` Just the row number · `D` A copy of the entire row
→ **Answer:** B.

**Q2.** When a value is popped from the heap, what determines what gets pushed back in?
`A` Nothing is pushed back · `B` The next element from that same row, if one exists · `C` A random element from any row · `D` The largest remaining value overall
→ **Answer:** B.

**Q3.** What happens when a row runs out of elements to contribute?
`A` The whole algorithm errors out · `B` The heap simply doesn't get refilled from that row anymore · `C` The row restarts from its first element · `D` All other rows are discarded
→ **Answer:** B.

**Q4.** Time complexity of the min-heap merge approach, where `k` rows each have up to `k` elements?
`A` `O(k log k)` · `B` `O(k² log k)` · `C` `O(k² log k²)` · `D` `O(k²)`
→ **Answer:** B.

**Q5.** Does the min-heap merge approach require all `k` rows to be the same length?
`A` Yes, always · `B` No — it only checks whether each row has a next element · `C` Only for even `k` · `D` Only if `k > 10`
→ **Answer:** B.

**Q6 (MSQ — pick all correct).** Which are true of the k-way merge approach?
`A` Every row must arrive already sorted · `B` The heap holds at most one live candidate per unvisited-to-exhaustion row · `C` It re-sorts each row internally as it goes · `D` It never re-sorts anything — it only ever decides relative order across rows

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"You have a huge list of website visits by user ID, and you want the top 3 most frequent visitors. Is 'frequent' the same kind of thing as 'large,' the way it was two sessions ago when we wanted the largest *values*?"*

Let students react — most will sense it's different but not immediately say why. Then:

> *"Completely different axis. Two sessions ago, the heap cared about how big a number *was*. Today, the heap doesn't care about the element's value at all — it cares only about how *often* that element showed up. Same eviction trick as Session 26, just pointed at a different quantity entirely."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–22: Problem Statement, Examples, Brute Force Approach, Dry Run, Pseudocode, Complexity, Code -->
Covers: problem statement (return the `k` most frequent elements in `arr`) → Example 1 (`arr=[1,1,1,2,2,3]`, `k=1` → `[1]`, since `1` appears 3 times) → Example 2 (`arr=[8,10,3,7,10,7,1,0,7]`, `k=2` → `[7,10]`) → brute force: count frequency of every element into a map, convert to `(frequency, element)` pairs, sort descending by frequency, take the first `k` → dry run building `(1,8),(2,10),(1,3),(3,7),(1,1),(1,0)`, sorting to `(3,7),(2,10),(1,8),(1,3),(1,1),(1,0)`, taking the first `k=2` → `[7,10]` → pseudocode → complexity (`O(n)` to build the frequency map, `O(u log u)` to sort `u` unique elements, overall `O(n + u log u)`, worst case `u=n` giving `O(n log n)`; space `O(u+k)`, worst case `O(n+k)`) → C++/Python code.

**Beats to emphasise**

- State the brute force in one line: *"count how often everything appears, sort by that count descending, take the top `k` — structurally identical to Session 26's brute force, just sorting by frequency instead of by value."*
- **Say the counting step is unavoidable, in either approach:** *"you cannot know an element's frequency without first scanning the whole array — every solution to this problem starts with an `O(n)` counting pass, no way around it."*
- Note the `u` vs `n` distinction precisely: sorting cost depends on the number of *unique* elements, not the array's raw length — if most elements repeat heavily, `u` can be much smaller than `n`.

**Checkpoint (at 17 min)** — cold-call:
> *"Why does every approach to this problem — brute force or optimal — have to start with an `O(n)` frequency count, no matter what happens after?"*
> **Answer:** Frequency is a property that only emerges from seeing every occurrence of an element across the whole array — there's no way to know how often something appeared without a full pass counting it first.

---

## Slide Block B (17–25 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 23–41: Optimal Approach, full Dry Run, Pseudocode, Complexity, Code -->
Covers: optimal approach — build the frequency map first (`O(n)`, unavoidable), then push `(frequency, element)` pairs into a min-heap, evicting the smallest-frequency pair whenever heap size exceeds `k` → full dry run on `arr=[8,10,3,7,10,7,1,0,7]`, `k=2`: frequencies `{8:1, 10:2, 3:1, 7:3, 1:1, 0:1}`; insert `(1,8)`; insert `(2,10)`; insert `(1,3)` → size 3 > 2 → evict smallest `(1,3)`; insert `(3,7)` → size 3 > 2 → evict smallest `(1,8)`; insert `(1,1)` → size 3 > 2 → evict smallest `(1,1)`; insert `(1,0)` → size 3 > 2 → evict smallest `(1,0)`; final heap `{(2,10),(3,7)}` → result `[7,10]` → pseudocode → complexity (`O(n)` to count, `O(u log k)` to maintain the heap over `u` unique elements, overall `O(n + u log k)`, worst case `O(n log k)`; space `O(u)` for the map plus `O(k)` for the heap, overall `O(n+k)` worst case) → C++/Python code.

**Beats to emphasise**

- **Say the mechanism as one sentence, this is the whole session:** *"once frequencies are known, push every `(frequency, element)` pair into a min-heap, and whenever it grows past size `k`, evict the pair with the smallest frequency — same eviction rule as Session 26's Kth-Largest, just keyed on frequency instead of raw value."*
- **Name the direct parallel to Session 26 explicitly:** the min-heap's job is identical — "keep the top `k` survivors, discard the weakest whenever a new one has to compete for a spot." Only the comparison key changed.
- Walk the dry run's repeated evictions carefully — three of the four insertions after the heap fills immediately trigger an eviction, because most candidates here have frequency `1`, the lowest possible value, so they rarely survive once stronger candidates exist.

**Checkpoint (at 25 min)** — cold-call:
> *"This heap and Session 26's Kth-Largest heap use the exact same eviction rule. What's the one thing that's actually different between them?"*
> **Answer:** What the heap compares elements *by*. Session 26 compared raw values directly. Today, the heap compares `(frequency, element)` pairs by their frequency — the element's own numeric value plays no role in ordering, only how often it occurred.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Evict by Frequency, Not Value" (30–36 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students correctly compare by frequency rather than defaulting to comparing by element value — a natural slip given Session 26 trained the opposite instinct on the same-shaped heap.

**Setup line (say this):**
> *"Frequencies already counted: element `9` appears 5 times, element `2` appears 1 time, element `50` appears 3 times, element `4` appears 1 time. `k = 2`. Insert them into the min-heap in that order — `9`, `2`, `50`, `4` — and tell me each eviction, by frequency, before I confirm."*

Run **one insert at a time**:

```
insert (5,9)  → heap {(5,9)}                                  size 1 ≤ 2, no eviction
insert (1,2)  → heap {(1,2),(5,9)}                             size 2 ≤ 2, no eviction
insert (3,50) → heap {(1,2),(5,9),(3,50)} → size 3 > 2 → evict smallest freq (1,2) → heap {(3,50),(5,9)}
insert (1,4)  → heap {(1,4),(3,50),(5,9)} → size 3 > 2 → evict smallest freq (1,4) → heap {(3,50),(5,9)}
```

**How it surfaces:** At `insert (3,50)`, ask before revealing: *"Which pair gets evicted — the one with the smallest element value, or the smallest frequency?"* If someone reaches for element value (evicting based on `2` being numerically small is correct here by coincidence — the real test is the next step), immediately push: *"`50` is a much bigger number than `4` — if we were comparing by value, would `4` survive over `50`? We're not comparing by value at all."*

**Debrief line:**
> *"Final heap holds `(3,50)` and `(5,9)` — the two most *frequent* elements, `50` and `9`, regardless of the fact that `50` is numerically much larger than the element that got evicted. Value never enters the comparison — only frequency does."*

**Cut rule:** If running short, do just the first two inserts plus one eviction — one correctly-identified eviction is enough to confirm the frequency-only comparison; the second eviction is confirmation, not new information.

---

## ⚡ Activity 2 — Predict & Discuss: "Three Sessions, One Heap Trick" (36–41 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students can articulate the shared structural pattern across Sessions 26, 27, and 28 — the actual payoff of ending the Heaps block on this problem.

**Setup line (say this):**
> *"Kth Largest Element, Merge K Sorted Arrays, and Top K Frequent Elements — three different problems, three different-looking heap contents. What's actually the same about all three?"*

**What students do:** Discuss for a minute, then share out.

**Answer:** All three maintain a heap that never grows past a bounded size (`k` in two of the three cases), and all three use the heap's root to cheaply find "the current weakest survivor" so it can be evicted or read. What changes each time is only the comparison key: raw value (Session 26), `(value, origin)` for merge ordering (Session 27), and frequency (today). The heap operations themselves — push, peek, pop — never change shape.

**How it surfaces:** Ask a follow-up: *"If tomorrow's problem asked for the `k` elements closest to some target number, what would the heap compare by?"* Push toward: distance from the target — same pattern again, a third possible comparison key.

**Debrief line:**
> *"Once you can spot 'maintain the top/bottom `k` of something,' the heap is almost always the tool — the only real design decision left is what the comparison key should be."*

**Cut rule:** If running short, state the shared pattern directly and skip the open discussion.

---

## Exit Ticket (41–45 min)

> Frequencies already known: `A` appears 2 times, `B` appears 6 times, `C` appears 1 time, `D` appears 4 times. `k = 2`. Trace every insertion and eviction, and state the final answer.
> **Answer:** insert `(2,A)` → `{(2,A)}`; insert `(6,B)` → `{(2,A),(6,B)}`; insert `(1,C)` → size 3 > 2 → evict smallest freq `(1,C)` → `{(2,A),(6,B)}`; insert `(4,D)` → size 3 > 2 → evict smallest freq `(2,A)` → `{(4,D),(6,B)}`. Final answer: `B` and `D` (frequencies 6 and 4). <!-- placement: inferred exit-ticket frequencies, built to exercise mid-sequence eviction on both the very first and a later candidate -->

**Homework:** given `arr = [4,4,4,9,9,1,2,2,2,2]`, `k = 2`, compute frequencies by hand and trace the min-heap approach to the final answer. <!-- placement: inferred — no homework/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The heap compares elements by their own value, like in Session 26 | Same heap shape, same eviction rule, trained instinct from two sessions ago | Activity 1 — the `50` vs `4` case, where the numerically larger element is the one evicted, since frequency (not value) drives every comparison |
| Frequency can be determined without a full pass over the array | Sorting-based and heap-based approaches both "feel" like they skip the counting step since it's mentioned briefly | Slide Block A's checkpoint — explicitly establishing the `O(n)` counting pass as mandatory in every approach |
| This is a completely new algorithm, unrelated to Sessions 26–27 | Different-sounding problem statement ("frequent" vs "largest" vs "merge") | Activity 2 — explicitly naming the shared bounded-heap-with-eviction pattern across all three sessions |
| The brute force and optimal approaches differ in whether they count frequencies | Both are heap/sort-adjacent, easy to conflate which step is shared | State plainly: both approaches share the identical `O(n)` frequency-counting step; they only differ in *how* the top `k` are selected afterward — sorting all unique pairs vs. maintaining a bounded heap |
| A higher-frequency element evicted earlier in the trace means it was wrongly discarded | Eviction still reads as "a mistake" for some students, carried over from Session 26 | Reaffirm from Session 26: eviction is deliberate — an element evicted early can still return later in a real streaming scenario, but within this single static pass, once evicted it's correctly judged as not in the current top-`k` |

---

## Instructor Notes

- **This session is deliberately the block's capstone — treat Activity 2 as the most important five minutes, not an optional extra.** Students who can articulate the shared "bounded heap + comparison key" pattern across Sessions 26–28 have understood the actual point of teaching three heap-application problems back to back.
- **Watch specifically for the value-vs-frequency comparison slip in Activity 1** — it's the single most predictable error this session, precisely because Session 26 trained the opposite habit on a heap that looks identical.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- **This closes the Heaps block.** If time allows, briefly preview that the next topic (Bit Manipulation) is a hard context switch — no heaps, no comparisons of "top-k" anything — so students aren't expecting the pattern to continue.
