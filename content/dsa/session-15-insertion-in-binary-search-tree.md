# Session 15 — Insertion in Binary Search Tree

**Duration** 50 min · **Topic** Binary Search Tree · **Prerequisite** Introduction to Binary Search Tree
**Session type** Concept lecture

<!-- placement: inferred — 50 min instead of the 45-min default because the deck itself packs in a second sub-topic: a full conceptual preview of Deletion's three cases (leaf / one child / two children) on top of insertion. Both need real board time. -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Insertion in Binary Search Tree | https://docs.google.com/presentation/d/18K-En87Al628DLlClc7jwzOqCFOhA7mSaxpbETki944/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the BST insertion rule: compare with the current node, go left if the new value is smaller, right if it's greater, and insert at the first empty spot found. *(REMEMBERING)*
2. Trace both the iterative and recursive insertion algorithms to insert a new value into a given BST. *(APPLYING)*
3. Explain why inserting a value at an arbitrary position (rather than at the position the comparison rule dictates) breaks the BST property. *(ANALYZING)*
4. Compare the space complexity of iterative insertion (O(1)) against recursive insertion (O(h)), and justify the difference in terms of the call stack. *(ANALYZING)*
5. Describe, at a conceptual level, the three deletion cases previewed in this session — leaf, one child, two children — ahead of next session's full treatment. *(UNDERSTANDING)* <!-- placement: inferred from the deck's own deletion-preview section, slides 38–57 -->

---

## Warm-Up Poll — Retrieval Practice on Introduction to BST (0–6 min)

Say: *"Six on yesterday's BST basics before we touch insertion."*

**Q1.** In a BST, the right subtree of a node contains values that are…
`A` smaller than the node · `B` greater than the node · `C` equal to the node · `D` unrelated to the node
→ *Read:* B. If this misses, the rest of today collapses — insertion IS this rule, applied once.

**Q2. (True/False)** Both the left and right subtrees of a BST node must themselves be BSTs.
→ *Read:* True. This is the recursive part of the definition, not just the top-level comparison.

**Q3.** What does an in-order traversal of a BST return?
`A` Reverse sorted order · `B` Random order · `C` Strictly increasing order · `D` Level order
→ *Read:* C.

**Q4. (MSQ)** Which of these are BST properties, per yesterday's session? *(pick all that apply)*
`A` Ordering invariant holds at every node · `B` No duplicate values, by default · `C` In-order traversal gives increasing order · `D` Search is always O(1)
→ *Read:* A, B, C are correct. D is the trap — search is O(h), never O(1).

**Q5.** What is the time complexity of iterative BST search?
`A` O(1) · `B` O(log n) always · `C` O(h) · `D` O(n²)
→ *Read:* C — and only equals O(log n) when the tree happens to be balanced.

**Q6.** What is the SPACE complexity of iterative search, specifically?
`A` O(h) · `B` O(1) · `C` O(n) · `D` O(log h)
→ *Read:* B — no recursion, no call stack, just a couple of pointers.

**Q7.** For a skewed BST with `n` nodes, the height `h` is approximately…
`A` log n · `B` n · `C` 1 · `D` n/2
→ *Read:* B — every node has exactly one child, so height degenerates to the node count.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Insertion is going to feel suspiciously familiar. Here's why: inserting a value into a BST is a search for that value that's allowed to fail — except when it fails, instead of shrugging and returning 'not found,' you plant a new node exactly where the search fell off the tree."*

Draw the comparison rule again on the board: `smaller → left, larger → right`. *"Same rule as yesterday. Today it just gets one more line: 'and if there's nothing there, put it there.'"*

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Insertion introduction and approach → Example 1 (insert 13) full dry run → Example 2 (insert 8) full dry run.

**Beats to emphasise**

- Walk **both** dry runs live, matching the deck's own comparisons exactly.
  - **Insert 13** into `{9, 4, 11, 1, 10, 14, 2, 5, 7}`: `9`→(13>9)→right to `11`→(13>11)→right to `14`→(13<14)→left child of `14` is empty→**insert 13 as left child of 14.**
  - **Insert 8** into the same tree: `9`→(8<9)→left to `2`→(8>2)→right to `5`→(8>5)→right to `7`→(8>7)→right child of `7` is empty→**insert 8 as right child of 7.**
- Say explicitly: *"Notice neither dry run ever needed to look at more than one side per step. That's the entire payoff of yesterday's property."*
- The **"return the root"** line in the approach (both iterative and recursive insertion return the tree's root at the end) is easy to skate past — flag it, because it's the detail students forget when they write insertion code and end up returning the wrong node.

**Checkpoint (at 19 min)** — cold-call:
> *"If I insert the value 6 into this same tree, which existing node does it end up under, and on which side?"*
> **Answer:** `9`→(6<9)→left to `2`→(6>2)→right to `5`→(6>5)→right to `7`→(6<7)→left child of `7` is empty → 6 becomes the **left child of 7**.

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: Insert 8 (19–25 min)

**Format:** Dry-Run Relay · **Exposes:** whether students can run the comparison rule end-to-end without narration, and whether they remember to stop the moment they hit an empty spot rather than continuing to compare.

**Setup line (say this):**
> *"Same tree, same value the deck just used — 8. I'm covering the answer this time. Each of you gets one comparison. Say the comparison, say the direction, pass it to the next person, and tell me when to stop."*

Draw the tree: root `9`; left child `2` (children `1` and `5`, where `5`'s children are `4` and `7`); right child `11` (children `10` and `14`).

**Expected relay:** `9`→(8<9)→left to `2` →(8>2)→right to `5` →(8>5)→right to `7` →(8>7)→right child of `7` is empty → **insert 8 here, as the right child of 7.**

**How it surfaces:** If a student keeps comparing after reaching the empty spot (e.g., tries to compare 8 against "nothing"), stop: *"There's no node there. What do you do when the rule points you at empty space?"*

**Debrief line:**
> *"You just ran the identical rule from yesterday's search, with one new ending: when the walk runs out of tree, that's not failure — that's your insertion point."*

**Cut rule:** If running short, skip re-drawing the tree and reuse the one already on the board from Slide Block A.

---

## ⚡ Activity 2 — Spot the Bug: The Deck's Own Broken Insertion (25–30 min)

**Format:** Spot the Bug · **Exposes:** the belief that insertion just needs "a spot," not "the correct spot the rule dictates" — this activity is lifted directly from the deck's own worked counter-example.

**Setup line (say this):**
> *"The deck tried inserting the value 13 as the LEFT child of node 1. Before I tell you whether that's allowed — is it still a BST afterward? Defend your answer with the rule, not a gut feeling."*

**What students do:** 45 seconds, then hands up.

**Answer:** No. `13 < 1` is false — 13 is **greater** than 1, but it was placed as 1's **left** child, where the rule requires every value to be **less than** 1. The left child of a node must be smaller than that node; putting a larger value there breaks the ordering invariant on the spot, regardless of where in the tree it happens.

**Debrief line:**
> *"'Find an empty spot' was never the rule. 'Find the ONE empty spot the comparison walk leads you to' is the rule. Every other empty spot in the tree is the wrong answer, even if it's unoccupied."*

**Cut rule:** This is a 3-sentence activity if you're tight on time — state the broken insertion, take one hand, give the answer, move on. Do not cut it entirely; it is the only place this session names the failure mode directly.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (35–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Iterative and recursive insertion pseudocode/code → complexity analysis (both O(h) time; O(1) space iterative vs. O(h) space recursive) → a conceptual **preview** of Deletion's three cases: deleting a leaf (delete 7), deleting a node with one child (delete 5, replaced by its child 4), deleting a node with two children (delete 11, replaced by successor 14).

**Beats to emphasise**

- Complexity pairing: **iterative insertion is O(h) time, O(1) space; recursive insertion is O(h) time, O(h) space.** Identical time, different space — same contrast as search from last session. Say it the same way both times so students hear the pattern repeating.
- **Frame the deletion section explicitly as a preview:** *"We are not building this algorithm today. I'm showing you the three shapes a deletion can take, so next session's pseudocode isn't the first time you've seen them."* Walk the three cases at a conceptual level only — no pseudocode, no code, just "what changes in the tree":
  - **Leaf (delete 7):** node has no children → just remove it, nothing to reattach.
  - **One child (delete 5, whose only remaining child is 4):** node is removed and its single child takes its place.
  - **Two children (delete 11, whose children are 10 and 14):** node is removed and replaced by a value that preserves order — the deck uses `14`, the in-order successor (smallest value in 11's right subtree).
- Do **not** derive the successor/predecessor rule in depth here — that reasoning belongs to Session 16. Today's job is just: "two children means you can't simply delete or promote a child — you need a value that keeps everything sorted."

**Checkpoint (at 42 min)** — show hands:
> *"Node 11 has two children — 10 and 14. The deck replaces it with 14. Who thinks 10 would also have worked?"*
> **Answer:** Yes — either the in-order predecessor (largest value in the left subtree, here 10) or the in-order successor (smallest value in the right subtree, here 14) preserves the ordering. The deck picked the successor; both are valid strategies, and Session 16 covers exactly how to pick and splice one in.

---

## Exit Ticket (42–45 min)

> On paper or in chat: *"(1) Using the tree from today (root 9, with 2/11 as its children), where does the value 6 get inserted? (2) Name the three deletion cases previewed today."*
> **Answers:** (1) `9`→left(6<9)→`2`→right(6>2)→`5`→right(6>5)→`7`→left(6<7)→**left child of 7.** (2) Leaf, one child, two children.

Homework: re-run today's insert-8 dry run from memory, then predict where 6, 3, and 20 would each land in the same tree. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Insertion just needs "an" empty spot | "Empty" feels like the only requirement once they stop thinking about ordering | Activity 2 — the deck's own 13-under-1 counter-example |
| Recursive insertion uses O(1) space, "like iterative does the same job" | Both produce identical trees, so students assume identical cost | The complexity pairing in Slide Block B — same time, different space, tied to the call stack |
| Deleting a two-children node just removes it and reattaches its children arbitrarily | Leaf and one-child deletion are simple removals, so two-children "should be" too | The deletion preview — naming successor/predecessor explicitly as the fix, even before the algorithm is built |
| Insertion can change an existing node's value instead of adding a new node | "Insert" sounds similar to "update" in everyday language | Point at the pseudocode's `new Node(target)` line — insertion always creates a node, never mutates an existing one |

---

## Instructor Notes

- **Known deck artifact — do not teach from it:** the recursive insertion C++/Python code slides in this deck are visually preceded by a leftover LCA code block (`Node* lca(Node* root, Node* p, Node* q) {...}`) that appears to be a copy-paste remnant from an earlier session's slide template, sitting above the actual `insertBST` code on the same slide. It is not part of this session's content — skip past it silently rather than explaining it. <!-- placement: inferred from raw slide-text extraction; flagging so the instructor isn't caught off guard live -->
- **Pacing risk:** the deletion preview (Slide Block B) is a preview, not a lesson — cap it at 5 minutes of talk time. If you find yourself explaining *why* the successor works, you've drifted into Session 16's territory; stop and say "more on that next time."
- **This session runs 5 minutes over the usual 45** because it's doing double duty (insertion in full, deletion in outline). If your cohort needs the full 50, protect Activity 2 (Spot the Bug) over Activity 1 (Dry-Run Relay) if you must cut one — Activity 2 carries the harder misconception.
- **Have the tree `{9, 4, 11, 1, 10, 14, 2, 5, 7}` already drawn** before class starts; it's reused across the Hook, both slide blocks, both activities, and the exit ticket. Redrawing it from scratch each time is where 45-minute sessions quietly become 55-minute sessions.
