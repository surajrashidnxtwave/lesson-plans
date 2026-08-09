# Session 17 — Kth Smallest Element in BST

**Duration** 45 min · **Topic** Binary Search Tree · **Prerequisite** Deletion in Binary Search Tree
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Kth Smallest Element in BST | https://docs.google.com/presentation/d/11Bmg4ukAe00sBSolJB-Jng7noLAz5rWorJAMS_alUVw/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Explain why an in-order traversal of a BST visits nodes in ascending sorted order, and why that makes "find the kth smallest" tractable. *(UNDERSTANDING)*
2. Trace the brute-force approach: full in-order traversal into an array, then index `arr[k-1]`. *(APPLYING)*
3. Trace the optimal approach: in-order traversal with a running counter that stops *updating* once `count == k`, without stopping the traversal itself. *(APPLYING)*
4. Compare the complexity of brute force (O(N) time, O(N+H) space) against optimal (O(N) time worst case, O(H) space), and explain that the optimal approach saves space, not time. *(ANALYZING)*
5. Predict the counter/answer state at each step of an in-order dry run, including after the answer has already been found. *(APPLYING)*

---

## Warm-Up Poll — Retrieval Practice on Deletion in BST (0–6 min)

Say: *"Six on yesterday's deletion before today's new problem."*

**Q1.** What are the three deletion cases in a BST?
`A` Root, leaf, internal · `B` Leaf, one child, two children · `C` Left, right, balanced · `D` Small, medium, large
→ *Read:* B.

**Q2. (True/False)** When deleting a node with two children, it's replaced by either its in-order predecessor or its in-order successor.
→ *Read:* True.

**Q3.** What is the overall time complexity of BST deletion, in all three cases?
`A` O(1) · `B` O(log n) always · `C` O(h) · `D` O(n²)
→ *Read:* C.

**Q4.** For the two-children case specifically, what extra step does deletion require beyond the initial search?
`A` Nothing extra · `B` A second search, for the successor or predecessor · `C` Re-inserting the whole subtree · `D` Rebuilding the entire tree
→ *Read:* B — and that second search is still bounded by O(h), which is why the total stays O(h) rather than becoming O(h²).

**Q5. (MSQ)** In the recursive deletion pseudocode, what happens if `root->left == null`? *(pick all that apply)*
`A` `temp = root->right` is saved · `B` The current node is deleted · `C` `temp` is returned to take the current node's place · `D` The whole tree is rebuilt
→ *Read:* A, B, C.

**Q6.** Deletion's space complexity depends on recursion depth, which is…
`A` O(1) · `B` O(h) · `C` O(n²) · `D` Always O(log n)
→ *Read:* B — O(log n) if balanced, O(n) if skewed.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"You've now built and deleted from BSTs by leaning on one fact over and over: in-order traversal gives you sorted order for free. Today's question tests how well you actually believe that fact: if I want just the 5th smallest value in the tree — not the whole sorted list, just one value from it — do you have to build the whole list anyway?"*

Let a few students guess yes/no before moving on. Don't resolve it yet — Slide Block A answers it directly.

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Problem statement + two worked examples → Brute-force approach → dry run (in-order traversal into an array, then `arr[k-1]`) → pseudocode/code → complexity.

**Beats to emphasise**

- State the problem exactly as the deck does: given a BST and a 1-indexed `k`, find the kth smallest value.
- Walk the dry run on the deck's example 2 tree (`{15, 10, 6, 4, 8, 13, 17, 22, 20}`, k=5): full in-order traversal gives `[4, 6, 8, 10, 13, 15, 17, 20, 22]`; the answer sits at index `k - 1 = 4`, which is `13`.
- Hammer the **1-indexed k, 0-indexed array** gap explicitly — say it as its own sentence, not folded into the walk-through. This is the exact bug Activity 1 is built to surface.
- Complexity: O(N) time (visits every node once), O(N) space for the array plus O(H) for the recursion stack — usually just written as O(N).

**Checkpoint (at 18 min)** — cold-call:
> *"Full sorted array is `[4, 6, 8, 10, 13, 15, 17, 20, 22]`. What's the 3rd smallest, and what index is that in the array?"*
> **Answer:** 3rd smallest is `8`, at index `2` (k - 1 = 3 - 1 = 2).

---

## ⚡ Activity 1 — Predict the Output: Off-by-One on k (18–24 min)

**Format:** Predict-the-Output · **Exposes:** the k-vs-(k-1) indexing gap — the single most common bug in this problem.

**Setup line (say this):**
> *"Same sorted array as the slides. I'll call out a k value. Before I confirm, write down BOTH the array index you'd check and the value you expect there. Most of you will get the value right and the index wrong, or vice versa — I want to see which."*

Use `[4, 6, 8, 10, 13, 15, 17, 20, 22]`. Call out, in order: `k=1`, `k=5`, `k=9`, `k=2`.

**Answers:** k=1 → index 0 → `4`. k=5 → index 4 → `13`. k=9 → index 8 → `22` (the last element — a good moment to ask "what if k were 10?" and let the class conclude "out of bounds, k can't exceed n"). k=2 → index 1 → `6`.

**How it surfaces:** If a student writes `arr[k]` instead of `arr[k-1]`, don't just correct it — ask them to state out loud which convention is "wrong": *"Is k wrong for being 1-indexed, or is the array wrong for being 0-indexed?"* (Neither — they're just different conventions that have to be reconciled by subtracting 1.)

**Debrief line:**
> *"k is 1-indexed because that's how humans count — 'the first, the fifth.' Arrays are 0-indexed because that's how computers count. Every kth-smallest bug you will ever write in this session is that one-character gap."*

**Cut rule:** Run only `k=1` and `k=5` if short on time — they cover both the trivial and the deck's own worked case.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–37 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Optimal approach — in-order traversal with a `count`/`ans` pair instead of storing the whole array → full dry run on the tree `{15, 10, 6, 4, 8, 13, 17, 22, 20}`, k=5, ending with `ans = 13` found at `count == 5` → pseudocode/code → complexity (O(N) time worst case, O(H) space).

**Beats to emphasise**

- Walk the dry run node by node exactly as the deck does, narrating `count` and `ans` at each step: descend the left spine (`15→10→6→4`), visit `4` (count 1→2), back to `6` (count 2→3), right to `8` (count 3→4), back up through `10` (count 4→5) — **now `count == k`**, so move right to `13`, visit it, and set `ans = 13`.
- Say the order of operations out loud, precisely: **check `count == k` FIRST, then increment.** This is the exact sequencing the next activity tests.
- Explicitly flag what happens **after** `ans` is set: the deck's own dry run states traversal continues, visiting the rest of the tree, but `ans` no longer changes because the check only fires once (`count` has moved past `k`).

**Checkpoint (at 37 min)** — show hands:
> *"After `ans` is set to 13, does the traversal stop, or does it keep visiting the remaining nodes?"*
> **Answer:** It keeps going. Nothing in the pseudocode returns early — recursion just runs to completion. `ans` simply never gets overwritten again because `count` has already passed `k`. This is exactly why the worst-case time complexity is still O(N), not O(H): the algorithm doesn't know to stop.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: Count-Then-Check vs. Check-Then-Count (37–43 min)

**Format:** Dry-Run Relay · **Exposes:** whether students internalise the exact ordering — check `count == k` BEFORE incrementing — or default to the more "natural"-feeling order of incrementing first.

**Setup line (say this):**
> *"Same tree, same k=5. I'll draw it, and each of you gets one node visit in in-order sequence. Before you move on: state count BEFORE this node, whether count equals k, whether ans changes, and count AFTER."*

Tree: root `15`; left child `10` (children `6` and `13`, where `6`'s children are `4` and `8`); right child `17` (children none-left / right child `22`, which has left child `20`). <!-- placement: inferred tree shape reconstructed from the dry-run narration in slides 23–41; diagram itself not fully recoverable from extracted text -->

**Expected relay (in-order sequence with count starting at 1):** `4`(count=1, not k, →2) → `6`(count=2, not k, →3) → `8`(count=3, not k, →4) → `10`(count=4, not k, →5) → `13`(count=5, **== k, ans=13**, →6) → `15`(count=6, not k, →7) → `17`(count=7, not k, →8) → `20`(count=8, not k, →9) → `22`(count=9, not k, →10).

**How it surfaces:** If a student increments count and THEN checks (making the trigger fire one node too late, e.g. at 10 instead of 13), stop: *"Read the pseudocode line order again — which comes first on the page, the comparison or the increment?"*

**Debrief line:**
> *"The off-by-one in this half of the problem isn't about arrays anymore — it's about whether you compare-then-bump or bump-then-compare. Get the order backwards and your answer shifts by exactly one node, silently."*

**Cut rule:** If running short, walk only the left spine plus the triggering node (`4, 6, 8, 10, 13`) and state the remaining traversal verbally rather than relaying it node by node.

---

## Exit Ticket (43–45 min)

> On paper or in chat: *"In your own words: why does the optimal approach still have O(N) worst-case time, even though it can determine the answer partway through the traversal?"*
> **Answer:** Because the algorithm never actually stops recursing once `ans` is set — it has no early-return — so in the worst case (e.g., a large `k`, or the answer sitting deep in the left side of the tree) most or all of the remaining nodes still get visited, even though they no longer change the result.

Homework: re-derive, on paper, what a version of this pseudocode WOULD look like if it *did* return early the moment `count == k` — no need to code it, just describe the one change. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| `arr[k]` gives the kth smallest | k feels like a direct index because it's a small, familiar number | Activity 1 — predicting both index and value side by side |
| The optimal approach is faster (lower time complexity) than brute force | "Optimal" in the name implies better time complexity across the board | The checkpoint after Slide Block B — both are O(N) worst case; the win is space, not time |
| The traversal stops the instant `ans` is found | It feels wasteful to keep going once you have the answer, so students assume the code must stop | Walking the deck's own dry-run note that traversal continues after `ans = 13` |
| Incrementing `count` before checking `count == k` gives the same result as checking first | Both feel like "the same two operations, just reordered" | Activity 2 — showing the off-by-one that results from swapping the order |

---

## Instructor Notes

- **Pacing risk:** Slide Block B's dry run has many small steps (nine node visits, each with a count/ans update) — narrate briskly and lean on Activity 2 to do the slow, careful pass rather than doing both at full detail.
- **Optional aside, time permitting only:** an alert student may ask "couldn't we make this O(h) by storing subtree sizes and skipping whole subtrees?" That's a real technique (augmented BSTs / order-statistics trees) but it is **not** covered anywhere in this deck — acknowledge it's a real idea, credit it as "beyond this problem's optimal solution as taught," and move on. Do not build it out live. <!-- placement: inferred — flagging so the instructor has a ready, honest answer if asked, without inventing deck content -->
- **The tree diagram for the optimal-approach dry run (Slide Block B / Activity 2) is reconstructed from the narration text**, not from a directly legible diagram in the extraction — cross-check the shape against the live deck slides before drawing it on the board. <!-- placement: inferred -->
- Both worked examples (Example 1: k=1 on a small tree, answer 2; Example 2: k=5, answer 13) recur across brute-force and optimal sections in the deck — reusing the SAME tree for both approaches, as this plan does, reinforces that it's the same problem solved two ways, not two different problems.
