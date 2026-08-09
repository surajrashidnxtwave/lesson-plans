# Session 21 — Introduction to Heaps

**Duration** 60 min <!-- placement: inferred — heavier than the 45-min default; deck covers definition, both heap variants, three structural properties, three worked operations (insert/extractMax/getMax), a heap-family survey, and applications, which doesn't fit 45 without cutting slide content --> · **Topic** Heaps · **Prerequisite** Binary Search Trees (previous session: Merge Two BSTs)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Introduction to Heaps | https://docs.google.com/presentation/d/17X8ri-v3OXVq0DdZrz0oPcu3hWfNY5EsdYNf1uMqhVU/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a heap as a complete binary tree that satisfies the heap property. *(REMEMBERING)*
2. Distinguish a Min Heap from a Max Heap by the parent–child ordering rule and by what sits at the root. *(UNDERSTANDING)*
3. Explain why a heap's height is `log(n)` and why that bounds every heap operation. *(UNDERSTANDING)* <!-- placement: inferred from Key Takeaways slide 53 -->
4. Trace the insertion ("bubble up") procedure on a max heap for a given sequence of inserted values. *(APPLYING)*
5. Trace the `extractMax()` ("bubble down") procedure and state why `getMax()` is `O(1)` while `extractMax()` is `O(log n)`. *(APPLYING)*
6. List at least two real-world applications of heaps (priority queues, heap sort, graph algorithms, kth largest/smallest). *(REMEMBERING)*

---

## Warm-Up Poll — Retrieval Practice on Session 20: Merge Two BSTs (0–7 min)

Say: *"Seven questions on yesterday's problem — merging two BSTs. No names, no grades, just tell me what stuck."*

**Q1.** What must the final output of "Merge Two BSTs" look like?
`A` Two separate sorted arrays · `B` One sorted array containing every element from both trees · `C` The two trees merged into one BST · `D` Doesn't matter, any order works
→ *Read:* B is the whole problem statement. If this misses, the rest of the poll is noise — restate the problem before Q2.

**Q2.** What is the first concrete step of the taught approach?
`A` Do a full inorder traversal of each tree and concatenate the two lists · `B` Push every left-descendant node from each root onto two separate stacks · `C` Convert both trees to arrays and merge-sort them · `D` Convert both trees into heaps
→ *Read:* If most of the class picks A, they're recalling *what inorder traversal produces*, not the *stack-based mechanism* that was actually taught. That gap is fine for today but flag it — it'll matter when Activity 2 asks them to trace steps, not just outcomes.

**Q3.** *(MSQ — pick 2)* Which are true of the merge step once both stacks are primed?
`A` Compare the tops of both stacks and pop the smaller one · `B` Always pop from stack 1 first if there's a tie · `C` If the popped node has a right child, push that child's left-descendant chain onto the *same* stack it came from · `D` Recursively call the merge function on each subtree
→ **Answer:** A and C.

**Q4.** Time complexity of Merge Two BSTs, with `N1`, `N2` nodes in the two trees?
`A` `O(N1 · N2)` · `B` `O(N1 + N2)` · `C` `O(log N1 + log N2)` · `D` `O(N1 log N2)`
→ **Answer:** B — every node in both trees is visited exactly once.

**Q5.** Space complexity, with `H1`, `H2` the heights of the two trees?
`A` `O(1)` · `B` `O(N1 + N2)` · `C` `O(H1 + H2)` · `D` `O(H1 · H2)`
→ *Read:* C. If the class answers B here, they're substituting time complexity for space complexity — a mix-up worth naming out loud before moving on: the stacks only ever hold one root-to-leaf path per tree, not every node.

**Q6.** In the dry run, once one stack goes empty but the other still has elements, what happens?
`A` The algorithm throws an error · `B` Keep popping from whichever stack still has elements · `C` The algorithm stops early · `D` Swap the two stacks
→ **Answer:** B — this is exactly the `s1.empty() || ...` branch in the pseudocode.

**Q7.** True or False: the algorithm never sorts the combined result directly — the sorted order falls out of the stack mechanics alone.
`A` True · `B` False
→ **Answer:** A True. *Read:* Worth 20 seconds — this is the same "the order comes from the algorithm's structure, not a separate sort step" idea that will reappear today in how a heap keeps its max accessible without ever sorting itself.

**Running it** — poll tool, ~40 s/question, project the distribution after each. Total 7 min including reads.

---

## Hook (7–11 min)

Say: *"Yesterday's BST problem got you a fully sorted array. That took visiting every node, and every stack push and pop. But most of the time, you don't need the full sorted order — you need one thing, fast, over and over: the maximum. Or the minimum. Right now."*

Ask: *"Emergency room, right now — who gets seen next? Not the person who arrived first. The person who's most critical. Every time someone new walks in, the answer to 'who's next' can change instantly. Would you re-sort the entire waiting room every time someone new arrives?"*

Let a few answers land — someone will say "no, that's wasteful." Then: *"Exactly. You want a structure that always knows who's most urgent, without needing everyone else in order. That's a heap."*

---

## Slide Block A (11–22 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 4–9: Definition, Types of Heaps, Heap Properties -->
Covers: Heap definition → Min Heap vs Max Heap → Heap Properties (Complete Binary Tree shape, unsorted siblings, height = `log(n)`).

**Beats to emphasise**

- **Heap property ≠ full sort.** Slide 8 is the single most important slide in this block: left and right children are *not* ordered relative to each other, only relative to their parent. Say this out loud even though the slide already says it — it directly contradicts the BST instinct they walked in with from yesterday.
- **Root tells you everything you need.** Min heap → smallest at root. Max heap → largest at root. That's the whole payoff of the structure.
- **Height = `log(n)` because the heap is always a complete binary tree** — no skew is possible, unlike the BSTs from the last several sessions. This is *why* every heap operation you'll see today is bounded by `log(n)`.

**Checkpoint (at 22 min)** — cold-call two students:
> *"Give me the two things every heap must satisfy, in one sentence."*
> **Answer:** It must be a complete binary tree (all levels full except possibly the last, filled left to right), and every parent must be ≥ (max heap) or ≤ (min heap) its children.

---

## ⚡ Activity 1 — Real-World Callout (22–27 min)

**Format:** Real-World Callout · **Exposes:** the sense that "heap" is an abstract data-structure exercise disconnected from anything students already reason about daily.

**Setup line (say this):**
> *"Thirty seconds. Name one real system where you always need 'the most urgent one' or 'the biggest one' right now, and you genuinely don't care what order everything else is in."*

**What students do:** Shout out systems. Write up to 8 on the board — expect things like hospital triage, an OS process scheduler, a flight standby list, a food-delivery app's next-driver assignment, a leaderboard's "current top score."

**How it surfaces:** For 2–3 of the callouts, push once: *"In that system, what's the 'parent' and what decides who's on top?"* Accept plain English — e.g., triage → "the most critical patient is always the one at the front, and a new critical patient can jump straight to the front without re-sorting the whole line."

**Debrief line:**
> *"Every one of those systems needs the extreme value, fast, again and again, and can't afford to re-sort everything each time someone new shows up. That's exactly the problem a heap solves — and that's why it's not a sorted array underneath."*

**Cut rule:** If running late, take 3 callouts instead of 8 and skip the push-for-mechanism step. Do not cut the debrief line.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (37–50 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 10–50: Insertion (bubble up), extractMax (bubble down), getMax -->
Covers: Insertion → worked example (insert 35, then 30, into a 7-node max heap) → insertion complexity → `extractMax()` → worked example (removing root 35, replacing with last element, bubbling down) → `extractMax()` complexity → `getMax()` → `getMax()` complexity.

**Beats to emphasise**

- **Insert always lands in the same place first:** the first vacant slot in the last level — *never* wherever it "should" sort to. Only after landing does it bubble up by comparing against its parent, one level at a time.
- **Walk the two worked inserts (35, then 30) at the board's pace, not the slide's.** There are ~20 slides across the two inserts because each swap gets its own slide — narrate the *pattern* ("compare, swap if bigger, move up, repeat until parent wins or you hit the root") rather than re-deriving arithmetic on every slide.
- **`extractMax()` is a two-part move, not one:** (1) delete the root and pull the *last* element into its place, (2) bubble that element *down*, swapping with whichever child is larger, until it's no longer smaller than either child. Both parts matter — the second part is where students under-deliver if you let this go by fast.
- **`getMax()` costs nothing.** It's `O(1)` — just read `root`. Contrast this hard against `extractMax()`'s `O(log n)`: reading the max is free, *removing* it isn't.

**Checkpoint (at 50 min)** — show hands:
> *"Insertion is O(what), and why?"*
> **Answer:** `O(log n)` — because the heap's height is `log n`, and bubble-up does at most one comparison-and-swap per level on the way up.

---

## ⚡ Activity 2 — Dry-Run Relay: extractMax() (52–57 min)

**Format:** Live Dry-Run Relay · **Exposes:** the "just delete the root, done" misconception — students track step 1 of `extractMax()` (remove root) but drop step 2 (bubble the replacement down through comparisons).

**Setup line (say this):**
> *"We just built this max heap by inserting 35 and 30: root 35, then 30, 20, 25, 8, 15, 13, 10, 17. I'm going to call extractMax() on it. I will not move a single node unless one of you tells me exactly which comparison to make and which swap to do. If you skip a step, I stop."*

**What students do:** Walking through the deck's own worked extractMax example — root 35 removed, last element 17 moved to the root — call out, one step at a time: *"Compare 17 with its children, 30 and 20 — which is bigger?"* (30) *"Swap 17 and 30."* Continue: *"Compare 17 with its new children, 25 and 8 — which is bigger?"* (25) *"Swap."* Continue: *"Compare 17 with its new child, 13 — is a swap needed?"* (No — 17 > 13, done.)

**How it surfaces:** If a student says "just remove 35 and we're done" or tries to skip straight to declaring the new root, stop and ask: *"What's sitting at the root right now, and does it belong there?"* Make them state the comparison before you'll move a node.

**Debrief line:**
> *"`extractMax()` is never a one-step delete. It's delete-and-replace, then bubble the replacement down until it earns its position by being bigger than both children. Skip the bubble-down and your 'heap' is just a tree that used to be one."*

**Cut rule:** If running short, do only the first two bubble-down comparisons (17 vs. 30/20, then 17 vs. 25/8) and state the final step ("17 vs. 13, no swap, done") yourself rather than relaying it. Do not cut the debrief line.

---

## Slide Block C (57–60 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — slides 51–54: other heap families, applications, Key Takeaways -->
Covers: other heap families that exist (Binomial, Fibonacci, Leftist, K-ary — names only, no mechanics) → applications (priority queue implementation, heap sort, graph algorithms, kth largest/smallest) → Key Takeaways.

**Beats to emphasise**

- Deliver the other-heap-family slide as a *map*, not a lecture: "these exist, you'll never implement them by hand in this course, know the names." Do not open a tangent on Fibonacci heaps.
- Land hard on **Applications** — heap sort and kth-largest/smallest are literally the next several sessions in this course. Say that explicitly: *"Sessions 23, 26, and 27 are three of these four bullet points."*

---

## Exit Ticket (60 min)

> On paper or in chat: *"This is a max heap: root 25, children 20 and 18, and 20's children are 10 and 8. I insert 30. Which nodes get compared against 30, in order, and which get swapped?"*
> **Answer:** 30 lands as the next left-to-right vacant slot (as 20's left child, replacing where 10 was — i.e., final shape has 30 where 10 was). Compare 30 vs. parent 20 → 30 > 20, swap. Compare 30 vs. parent 25 → 30 > 25, swap. 30 is now root; stop, no parent left to compare. <!-- placement: inferred exit-ticket scenario, built from the same insertion mechanics as the deck's worked example -->

**Homework:** re-attempt today's two dry runs (insertion of 35 & 30, extraction of 35) from memory, without looking at the slides. <!-- placement: inferred; no homework/practice-unit table exists for this course -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A heap keeps everything in sorted order, like a BST's inorder traversal | Yesterday's session (Merge Two BSTs) trained "traverse it and you get sorted order" | Slide 8 — point at a valid max heap where the left child is smaller than the right child, or vice versa; only the parent–child relationship is guaranteed |
| A new element gets inserted wherever it "should" sort to | BST insertion habit — search down to find the correct spot | Insertion always lands in the last level's first vacant slot; the sorting work happens *after*, via bubble-up |
| `extractMax()` is just "delete the root" | Attention naturally lands on the dramatic step (removing the max) and drops the quieter one | Activity 2's dry-run relay — force the bubble-down comparisons to be stated aloud, step by step |
| Heap height can be `O(n)`, like a skewed BST | Same BST habit from the immediately preceding topic, where skewed/unbalanced trees are common | Slide 9 — a heap is *always* a complete binary tree, so height is always `log(n)`, no exceptions |
| Min heap means "less important" and max heap means "more important" | The words "min" and "max" get read as value judgments instead of ordering direction | State plainly: min heap = smallest value at root, max heap = largest value at root — nothing about importance, only about ordering |

---

## Instructor Notes

- **Pacing risk:** the insert-35-then-30 walkthrough spans roughly 20 slides for what is really two repeated steps ("compare and swap while bigger than parent"). Narrate the pattern once explicitly, then move through the remaining slides at a brisk, confirming pace — don't re-derive the comparison logic from scratch on every slide.
- **BST carryover is the dominant risk in this session.** Because this is the first Heaps session immediately after the BST unit, expect the class to keep reaching for "sorted order" and "search down to insert" instincts. Name the contrast explicitly at least twice (Slide Block A checkpoint, and again before Activity 2).
- **This is a foundation session.** Sessions 22–28 all build on the operations taught here — the array representation of a heap (Session 22) and every problem after it assumes fluency with bubble-up/bubble-down. If the extractMax dry run in Activity 2 goes badly, flag it — Session 22 opens by rebuilding these same operations on an array, and a shaky foundation here compounds.
- Have the two worked examples (insert 35 & 30; extractMax on the resulting heap) sketched on the board or a handout before class — the numbers repeat across many slides and are easy for students to lose track of if they're copying from a fast-moving screen.
- The Classroom Quiz line above is a placeholder only — there is no MCQ bank in the source material for this course. Pull 5–6 questions from the platform's question bank live; do not build your own.
