# Session 19 — Predecessor and Successor in BST

**Duration** 45 min · **Topic** Binary Search Tree · **Prerequisite** Validate a Binary Search Tree
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Predecessor and Successor in BST | https://docs.google.com/presentation/d/184LAB4HY_aSrCoRvwwXiLXs_luXaqv92oPZmYAHv-HI/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define the predecessor and successor of a node in terms of its position in an in-order traversal. *(REMEMBERING)*
2. Trace the brute-force approach: in-order traversal into a sorted array, binary search for the target's index, then read its neighbours. *(APPLYING)*
3. Trace the optimal single-pass approach for predecessor (move right and record a candidate whenever `current < target`, otherwise move left) and for successor (move left and record a candidate whenever `current > target`, otherwise move right). *(APPLYING)*
4. Explain why the optimal approach needs no array at all — a single pointer walk that updates a "best candidate so far" as it descends. *(UNDERSTANDING)*
5. State the time/space complexity of both approaches: brute force O(N) time / O(N) space; optimal O(H) time / O(1) space. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Validate a Binary Search Tree (0–6 min)

Say: *"Six on yesterday's BST validation before today's related problem."*

**Q1.** The optimal Validate-BST approach checks each node against…
`A` Only its immediate parent · `B` A `(min, max)` range inherited from all its ancestors · `C` The root only · `D` Its siblings
→ *Read:* B.

**Q2.** Moving LEFT in the range-check approach tightens which bound?
`A` The minimum · `B` The maximum · `C` Both · `D` Neither
→ *Read:* B — the maximum becomes the parent's value; the minimum is inherited unchanged.

**Q3. (True/False)** A tree can pass every local parent-child comparison and still not be a valid BST.
→ *Read:* True — that was the entire point of the Session 14 callback.

**Q4.** In the brute-force Validate-BST approach, what check is run on the in-order array?
`A` Is it sorted descending? · `B` Is it strictly increasing? · `C` Does it contain duplicates? · `D` Is its length even?
→ *Read:* B.

**Q5.** What's the time complexity shared by BOTH the brute-force and optimal Validate-BST approaches?
`A` O(1) · `B` O(log n) · `C` O(n) · `D` O(n²)
→ *Read:* C — they differ in space (O(n) vs O(h)), not time.

**Q6.** What's the space complexity of the optimal (range-check) approach specifically?
`A` O(1) · `B` O(n) · `C` O(h) · `D` O(n log n)
→ *Read:* C — the recursion stack.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"You've already met today's two ideas without their names. In Kth Smallest, they were the array neighbours on either side of the answer. In Validate BST, they were the two directions a range check narrows in. Today they get names — predecessor and successor — and their own dedicated algorithm."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Definitions of predecessor and successor (in terms of in-order position, with NULL as the edge case) → problem statement → both worked examples → brute-force approach (in-order traversal into a sorted array, binary search for the target, read neighbours) → dry run.

**Beats to emphasise**

- State both definitions precisely: **predecessor** = the node immediately BEFORE the target in an in-order traversal (NULL if the target is first). **successor** = the node immediately AFTER (NULL if the target is last).
- Walk **Example 1** in full: tree `{11, 5, 15, 3, 7, 19, 6, 9, 17}`, target `7`. In-order traversal: `3, 5, 6, 7, 9, 11, 15, 17, 19`. Predecessor of 7 is `6`; successor is `9`.
- Name **Example 2** as the edge case: target `3`, which is the FIRST value in its tree's in-order sequence → predecessor is `NULL`, successor is `5`. Say explicitly: *"First in the sequence means no predecessor exists — not zero, not the smallest possible value, NULL."*
- Brute force: build the sorted array via in-order traversal, binary search for the target's index `idx`, then predecessor = `nodes[idx-1]` if it exists, successor = `nodes[idx+1]` if it exists.

**Checkpoint (at 18 min)** — cold-call:
> *"Sorted array is `[3, 5, 6, 7, 9, 11, 15, 17, 19]`, target is 7 at index 3. What are the predecessor and successor, by index?"*
> **Answer:** Predecessor = `nodes[2]` = `6`. Successor = `nodes[4]` = `9`.

---

## ⚡ Activity 1 — Predict the Output: Array Neighbours and the NULL Edge Case (18–24 min)

**Format:** Predict-the-Output · **Exposes:** whether students handle the boundary cases (first/last element) correctly instead of assuming every element has both a predecessor and a successor.

**Setup line (say this):**
> *"Same sorted array as the slides: `3, 5, 6, 7, 9, 11, 15, 17, 19`. I'll call out a target value. Predict its predecessor AND successor before I confirm — including if one of them doesn't exist."*

Call out, in order: target `9`, target `19`, target `3`.

**Answers:** target `9` (index 4) → predecessor `7`, successor `11`. target `19` (index 8, LAST element) → predecessor `17`, successor `NULL`. target `3` (index 0, FIRST element) → predecessor `NULL`, successor `5` — matching the deck's own Example 2.

**How it surfaces:** If a student guesses a *value* instead of `NULL` for the missing neighbour (e.g., "predecessor of 3 is the smallest thing left, so... nothing smaller, maybe 0?"), stop: *"There's no value to invent. If the index doesn't exist in the array, the answer is NULL — not a guess at what it might have been."*

**Debrief line:**
> *"Predecessor and successor are just 'my neighbours in the sorted line.' Everyone has at most two — except the two people at the ends of the line, who are each missing one. That missing neighbour is NULL, not a placeholder value."*

**Cut rule:** Run only target `19` and target `3` — the two edge cases carry the whole point; the middle case is already covered by the checkpoint.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–38 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Optimal single-pass approach — separate pointer walks for predecessor and successor, each starting at the root, on the same Example 1 tree and target (`7`) → full dry runs for both → pseudocode/code → complexity (O(H) time, O(1) space).

**Beats to emphasise**

- State both rules side by side, deliberately mirrored: **Predecessor** — if `current < target`, record `current` as a candidate and move RIGHT (looking for something closer to target, still smaller); otherwise move LEFT. **Successor** — if `current > target`, record `current` as a candidate and move LEFT (looking for something closer to target, still bigger); otherwise move RIGHT.
- Walk the **predecessor** dry run for target 7: root `11` (11 not < 7, move left) → `5` (5 < 7, candidate = 5, move right) → `7` (7 not < 7, move left) → `6` (6 < 7, candidate = 6, move right to NULL) → **final predecessor = 6.**
- Walk the **successor** dry run for target 7: root `11` (11 > 7, candidate = 11, move left) → `5` (5 not > 7, move right) → `7` (7 not > 7, move right) → `9` (9 > 7, candidate = 9, move left to NULL) → **final successor = 9.**
- Land the "why move that direction" reasoning explicitly: *"Predecessor wants the LARGEST value still less than target — so once you find a candidate, you push RIGHT, back toward target, hoping for something even closer but still under it. Successor is the mirror: push LEFT, back toward target, hoping for something closer but still over it."*

**Checkpoint (at 38 min)** — show hands:
> *"Why does the predecessor search move RIGHT after finding a candidate, while the successor search moves LEFT after finding a candidate?"*
> **Answer:** They're chasing the target from opposite sides. Predecessor already has something smaller than target and wants to get closer without going equal-or-over, so it moves right (toward larger values, still hoping to stay under target). Successor already has something larger and wants to get closer without going equal-or-under, so it moves left (toward smaller values, still hoping to stay over target).

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: Team Predecessor vs. Team Successor (38–43 min)

**Format:** Dry-Run Relay (split class) · **Exposes:** whether students confuse the two mirrored rules under pressure — the single most common error in this session.

**Setup line (say this):**
> *"Half the room is Team Predecessor, half is Team Successor. Same tree, same target — 7 — same starting point, the root. Different rule. Go, and don't look at the other team's board."*

Tree: root `11`; left child `5` (children `3` and `7`, where `7`'s children are `6` and `9`); right child `15` (children `null` and `19`, where `19`'s left child is `17`). Both teams walk simultaneously on identical copies of this tree.

**Expected outcome:** Team Predecessor arrives at `6`; Team Successor arrives at `9` — matching the deck's stated output `{6, 9}` for target 7.

**How it surfaces:** If Team Predecessor moves LEFT after finding a candidate (mirroring Team Successor's rule by mistake), stop: *"You just found something smaller than target. Going further left makes it MORE smaller — further from target, not closer. Which direction gets you closer while staying under target?"*

**Debrief line:**
> *"Same walk, mirrored rule. Predecessor hugs the target from below by drifting right; successor hugs it from above by drifting left. Whichever value updates your 'best so far,' you keep pushing toward the target on that exact rule — never away from it."*

**Cut rule:** Run Team Successor live only; describe Team Predecessor's walk verbally as the mirror image rather than running both teams simultaneously.

---

## Exit Ticket (43–45 min)

> On paper or in chat: *"Target = 3 in Example 2's tree, and the deck states the predecessor is NULL. Using the optimal rule, explain in one sentence why the predecessor pointer walk never finds a candidate."*
> **Answer:** Because 3 is the smallest value in the entire tree, every node visited during the walk has a value greater than or equal to 3 — the condition `current < target` never becomes true, so the predecessor candidate is never updated away from its initial `NULL`.

Homework: re-run both the predecessor and successor dry runs for target 7 from memory, then predict both for a target of your own choosing from the same tree. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Predecessor and successor's movement rules are interchangeable | Both are "walk down recording a candidate," which sounds identical at a glance | Activity 2 — running both rules live, side by side, on the same target |
| A missing predecessor/successor means "the smallest/largest possible value" instead of NULL | Feels safer to guess a number than to say "nothing" | Activity 1 — explicitly testing the first/last element edge cases |
| The optimal approach still needs the full sorted array, just walked more cleverly | Hard to believe you can skip building the array entirely | Naming the O(1) space complexity explicitly and tying it to "no list, just a few pointers" |
| Predecessor/successor must be a direct parent or child of the target | Intuition from "neighbour" suggests physical adjacency in the tree | Example 1 — 6 and 9 are neither parent nor child of 7, only its in-order neighbours |

---

## Instructor Notes

- **Pacing risk:** Activity 2's split-class format takes longer to set up than a single relay — have the tree pre-drawn TWICE on the board (or two slides) before class starts so both teams can start immediately without waiting on each other.
- **This session doesn't introduce a genuinely new mechanic** — it recombines in-order traversal (Kth Smallest) and directional pointer walks (Validate BST's range propagation) into a new problem shape. If a cohort is strong, you can shorten Slide Block A and spend the saved time letting Activity 2 run longer, including a second target value.
- **Successor-not-found edge case:** the deck states the general rule (successor is NULL if target is last in-order) but does not walk a full dry run for it — Activity 1's `target = 19` case is this plan's own minimal worked example of that edge case, built directly from the stated rule rather than a new algorithm. <!-- placement: inferred -->
- Callback opportunity: Session 16's deletion-of-a-two-children-node relied on finding "the smallest value in the right subtree" — that IS a successor computation, just under a different name. Worth a 10-second verbal callback after Slide Block B if time allows.
