# Session 18 — Validate a Binary Search Tree

**Duration** 45 min · **Topic** Binary Search Tree · **Prerequisite** Kth Smallest Element in BST
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Validate a Binary Search Tree | https://docs.google.com/presentation/d/1fq_020a_hbPtK8xmjJ_bLJUJziUaaB4G6wwncPjhSSQ/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the BST validation rules: left subtree values are all less than the root, right subtree values are all greater, and both subtrees must themselves be valid BSTs. *(REMEMBERING)*
2. Trace the brute-force approach: in-order traversal into an array, then a strictly-increasing check across the array. *(APPLYING)*
3. Trace the optimal range-check approach: propagate a `(min, max)` bound down each recursive call, narrowing it at every left or right step. *(APPLYING)*
4. Explain why checking only "is this node less/greater than its immediate parent" is insufficient, and why a range inherited from every ancestor is required instead. *(ANALYZING)*
5. Compare the time/space complexity of both approaches — both O(n) time; O(n) space (brute force) vs. O(h) space (optimal). *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Kth Smallest Element in BST (0–6 min)

Say: *"Six on yesterday's Kth Smallest before today's problem, which is really the same traversal wearing a different hat."*

**Q1.** What traversal order does the Kth Smallest problem rely on?
`A` Pre-order · `B` Post-order · `C` In-order · `D` Level-order
→ *Read:* C.

**Q2. (True/False)** The brute-force and optimal approaches to Kth Smallest have the same worst-case TIME complexity.
→ *Read:* True — both are O(N). The optimal approach saves space, not time.

**Q3.** In the optimal approach's pseudocode, which happens first at each node: checking `count == k`, or incrementing `count`?
`A` Checking first · `B` Incrementing first · `C` They happen simultaneously · `D` Order doesn't matter
→ *Read:* A — get this backwards and the answer shifts by one node.

**Q4.** Given the sorted array `[4, 6, 8, 10, 13]` and `k = 3`, what's the answer, and at what array index?
`A` 10, index 3 · `B` 8, index 2 · `C` 8, index 3 · `D` 13, index 4
→ *Read:* B — k is 1-indexed, arrays are 0-indexed, so check `arr[k-1]`.

**Q5.** Does the optimal in-order approach stop traversing once `ans` is found?
`A` Yes, it returns immediately · `B` No, it keeps visiting the rest of the tree, ans just stops changing
→ *Read:* B.

**Q6.** What is the space complexity of the optimal (counter-based) approach?
`A` O(1) · `B` O(N) · `C` O(H) · `D` O(N log N)
→ *Read:* C — the recursion stack, bounded by tree height.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Weeks ago, in the Intro to BST session, I showed you a tree that looked almost right — every parent-child pair passed its local comparison, but the whole tree still wasn't a valid BST, because one value strayed outside the range set by an ancestor further up. I told you then to hold onto that idea. Today we build the algorithm that actually catches it."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Validation rules restated → problem statement → brute-force approach (in-order traversal + strictly-increasing check) → both worked examples (Example 1: valid tree; Example 2: invalid tree where the sequence breaks at 17→12).

**Beats to emphasise**

- State the rule cleanly: left subtree strictly less than root, right subtree strictly greater, and — same recursive clause as Session 14 — **both subtrees must themselves be valid BSTs.**
- Walk **Example 2's** dry run in full, since it's the one that teaches the technique: in-order traversal produces `[4, 5, 10, 17, 12, 15, 16, 18, 20, 21, 22]`. Read the sequence aloud and let the class catch where it stops climbing: `10 < 17`, then `17 > 12` — **break.** Result: `false`.
- Name the check precisely: "strictly increasing," using `>=` as the failure condition (`arr[i-1] >= arr[i]` fails validation) — this quietly also rules out duplicate values, tying back to Session 14's "no duplicates by default" property.

**Checkpoint (at 18 min)** — cold-call:
> *"Example 1's in-order array is `[1, 3, 4, 5, 7, 8, 9, 10, 11, 12]`. Valid or not, and why?"*
> **Answer:** Valid — every consecutive pair is strictly increasing, `1 < 3 < 4 < ... < 12`, with no break anywhere in the sequence.

---

## ⚡ Activity 1 — Spot the Bug: Find the Break in the Sequence (18–24 min)

**Format:** Spot the Bug · **Exposes:** whether students can apply the strictly-increasing check by eye, and whether they stop at the FIRST break rather than trying to evaluate the whole array holistically.

**Setup line (say this):**
> *"Here's the deck's own in-order array from Example 2. Somewhere in this sequence, strictly-increasing breaks. Find the exact pair where it happens — not 'somewhere in the middle,' the exact two adjacent numbers."*

Put on the board: `[4, 5, 10, 17, 12, 15, 16, 18, 20, 21, 22]`

**What students do:** 30 seconds silent, then hands up. Take the first correct answer, then ask them to name BOTH values in the breaking pair.

**Answer:** The break is `17 → 12` (index 3 to index 4): `17 > 12`, so the sequence stops being strictly increasing there. Per the deck's own explanation, this corresponds to node `17` sitting in the left subtree of node `12` in the original tree — but `17 > 12`, violating the rule that a left subtree must hold only smaller values.

**How it surfaces:** If a student stops at a DIFFERENT, later-looking irregularity (there isn't one after the fix point, but some may hesitate on `18, 20, 21, 22` as "too evenly spaced to be right"), redirect: *"Compare every adjacent pair, in order, left to right. Where's the FIRST one that fails?"*

**Debrief line:**
> *"One broken pair is enough to invalidate the whole tree — you don't need to find every violation, just the first one. That's why the algorithm can return `false` the moment it sees one bad pair, instead of finishing the whole array."*

**Cut rule:** If running short, skip the "first vs. all violations" follow-up question and just confirm the single break point.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–38 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Optimal approach — recursive min-max range check → full dry run on Example 1's tree (root 7; building outward through nodes 5, 3, 1, 4, 9, 8, 11, 10, 12), tracking each node's valid `(min, max)` range → pseudocode/code → complexity (O(N) time, O(H) space).

**Beats to emphasise**

- State the propagation rule precisely, twice, in different words: *"Going left tightens the upper bound to the parent's value. Going right tightens the lower bound to the parent's value. Everything else about the range — the bound you DIDN'T just tighten — gets inherited unchanged from the parent."*
- Walk the range build-up exactly as the deck does: root `7` → `(-∞, ∞)`. Left to `5` → `(-∞, 7)`. Left to `3` → `(-∞, 5)`. Left to `1` → `(-∞, 3)`. Back up, right child of `3` is `4` → `(3, 5)`. Right of root to `9` → `(7, ∞)`. Left child of `9` is `8` → `(7, 9)`. Right child of `9` is `11` → `(9, ∞)`. Left child of `11` is `10` → `(9, 11)`. Right child of `11` is `12` → `(11, ∞)`.
- Explicitly connect this back to Session 14's Spot-the-Bug tree: *"That tree passed every local parent-child check and still wasn't a BST. A range check would have caught it immediately, because the offending node's value would have fallen outside the range inherited from an ancestor two or three levels up — not just its direct parent."*

**Checkpoint (at 38 min)** — cold-call:
> *"Node 9 is the root's right child. Its valid range is `(7, ∞)`. Why is the lower bound 7 instead of `-∞`?"*
> **Answer:** Because node 9 sits in the root's RIGHT subtree, and the rule says every value in a right subtree must be greater than the root — so the root's value (7) becomes the new lower bound passed down to that entire subtree.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: Building the Range Tree (38–43 min)

**Format:** Dry-Run Relay · **Exposes:** whether students can propagate the `(min, max)` pair correctly, in particular whether they narrow the CORRECT bound (max on a left move, min on a right move) and correctly carry the other bound forward unchanged.

**Setup line (say this):**
> *"Same tree, same ranges as the slides. I'll walk down it exactly like the deck did. Before each node, you tell me its `(min, max)` range, and whether it passes."*

Tree: root `7`; left child `5` (children `3` and `9`... <!-- placement: inferred correction: per the dry-run narration, 5's children are 3 and (no right child stated); 9 is the ROOT's right child, not 5's --> — reconstruct on the board as: root `7`; left child `5`; `5`'s left child `3`, whose children are `1` (left) and `4` (right); root's right child `9`; `9`'s left child `8`, right child `11`; `11`'s left child `10`, right child `12`.

**Expected relay:** `7`→`(-∞,∞)` → left to `5`→`(-∞,7)` → left to `3`→`(-∞,5)` → left to `1`→`(-∞,3)`, passes → back to `3`, right to `4`→`(3,5)`, passes → back to `7`, right to `9`→`(7,∞)` → left to `8`→`(7,9)`, passes → back to `9`, right to `11`→`(9,∞)` → left to `10`→`(9,11)`, passes → back to `11`, right to `12`→`(11,∞)`, passes.

**How it surfaces:** If a student propagates the SAME range unchanged to both children (forgetting to narrow either bound), stop: *"You just went left/right — which bound does that move tighten? What replaces it?"*

**Debrief line:**
> *"A local check only ever asks 'is my child on the correct side of ME.' A range check asks 'is this node consistent with EVERY ancestor above it.' That's the difference between a tree that fools the local check and one that can't fool this one."*

**Cut rule:** Walk only the left spine (`7→5→3→1`, then `3`'s right child `4`) and state the right-subtree ranges verbally rather than relaying them node by node.

---

## Exit Ticket (43–45 min)

> On paper or in chat: *"Node X is the left child of node Y, and Y is itself the right child of the root R. Write X's valid range in terms of R and Y."*
> **Answer:** `(R, Y)` — Y's own range (as R's right child) is `(R, ∞)`, and moving left from Y tightens the upper bound to Y's value while keeping the inherited lower bound R, giving X the range `(R, Y)`.

Homework: re-attempt today's range-propagation dry run from memory, without looking at the slide. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Checking a node against only its immediate parent is enough | The definition is usually stated as a parent-child comparison first | Session 14's original Spot-the-Bug callback + this session's range-propagation dry run |
| The optimal approach is faster (better time complexity) than brute force | "Optimal" implies a full win, not a partial one | The complexity comparison — both O(n) time, the win is O(h) vs O(n) space |
| A left child just needs to be smaller than its direct parent, full stop | Matches the very first (incomplete) version of the BST definition | Activity 2 — showing that a left child's upper bound can be much smaller than its parent's value once it's several levels down |
| BSTs can have duplicate values, so `>=` should pass in the increasing-order check | Real-world data often has repeats | Point back to Session 14's "no duplicates by default" property — a repeated value fails validation by design |

---

## Instructor Notes

- **Pacing risk:** the range-propagation dry run (Slide Block B) has ten nodes' worth of range updates — narrate it briskly and let Activity 2 be the slow, careful re-run rather than doing both at full detail.
- **Direct callback session:** this is the payoff session for Session 14's "looks valid but isn't" Spot-the-Bug tree. If your cohort remembers that activity clearly, you can shorten the Hook to a single sentence and spend the saved time on Activity 2 instead.
- **Tree reconstruction note:** the Example 1 tree's exact left/right child layout for nodes below `5` and `9` had to be reconstructed from the deck's own node-by-node range narration (which states each node's parent and range explicitly) rather than from a directly legible diagram in the raw slide-text extraction. Cross-check the shape against the live slides before drawing it on the board. <!-- placement: inferred -->
- Example 2's tree has **more than one** violating node according to the deck's own explanation (it separately calls out `6` vs `5`, `17` vs `15`, and `14` vs `20` as violations in different framings across the two examples in this deck) — Activity 1 only needs the first break in the specific array used; mention briefly that real invalid trees often fail in more than one place, but don't hunt for all of them live.
