# Session 14 — Introduction to Binary Search Tree

**Duration** 45 min · **Topic** Binary Search Tree · **Prerequisite** Lowest Common Ancestor of a Binary Tree (last Binary Tree session)
**Session type** Concept lecture

<!-- placement: inferred — this is a single-concept intro session, lighter than a full topic-launch session, so 45 min rather than the reference's 60 -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Introduction to Binary Search Tree | https://docs.google.com/presentation/d/1-xHa4vmKdp0UcWgn4zs3tMmCn7i-LSu4i9gpzEV-JAk/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a Binary Search Tree by its ordering property — left subtree < node < right subtree — and state that both subtrees must themselves be BSTs. *(REMEMBERING)*
2. Verify whether a given binary tree satisfies the BST property by checking every node against its subtree, not just its immediate children. *(APPLYING)*
3. Explain why an in-order traversal of a BST always returns values in strictly increasing order. *(UNDERSTANDING)*
4. Trace the iterative and recursive search algorithms on a BST to determine whether a target value is present. *(APPLYING)*
5. State the time/space complexity of BST search in terms of height `h`, and distinguish the balanced case (`h ≈ log n`) from the skewed case (`h ≈ n`). *(ANALYZING)*

<!-- placement: inferred — phrased from the deck's Key Takeaways slides (42–45, 59) -->

---

## Warm-Up Poll — Retrieval Practice on LCA of a Binary Tree (0–6 min)

> Retrieval practice on the session immediately before this one. No new content — this is recall.

Say: *"Six quick ones on yesterday's Lowest Common Ancestor problem before we move to a new topic."*

**Q1.** What is the Lowest Common Ancestor (LCA) of two nodes `p` and `q`?
`A` The root of the tree · `B` The deepest node that is an ancestor of both `p` and `q` · `C` The parent of `p` · `D` The node halfway between `p` and `q`
→ *Read:* B. If this is shaky, the rest of the poll will be too — recap the definition before Q2.

**Q2.** In the LCA recursive approach, what do you return the moment `root == null` or `root == p` or `root == q`?
`A` `null` always · `B` `root` · `C` The parent of root · `D` An error
→ *Read:* B — that's the base case that either bottoms out the search or reports "found."

**Q3.** During the recursive calls, if the left subtree call returns `null` and the right subtree call returns a node, what do you return?
`A` `null` · `B` The right subtree's result · `C` The current node · `D` Both results
→ *Read:* B — one side being empty means both targets must be in the non-empty side.

**Q4.** If BOTH the left and right subtree calls return non-null, what does that tell you about the current node?
`A` It's an error · `B` The current node is the LCA · `C` One of `p`/`q` is missing · `D` Nothing, keep searching
→ *Read:* B — `p` and `q` are on different sides, so the current node is where their paths split.

**Q5.** What is the time complexity of the LCA algorithm?
`A` O(log N) · `B` O(N) · `C` O(N²) · `D` O(H)
→ *Read:* B — every node is visited exactly once in the recursive DFS.

**Q6. (MSQ)** Which of these are true about the LCA algorithm's space complexity? *(pick all that apply)*
`A` It is O(H) due to the recursion call stack · `B` In the worst case (a skewed tree) it becomes O(N) · `C` It is always O(1) · `D` It depends on the height of the tree
→ *Read:* A, B, D are correct. C is the trap — space is never O(1) here because of the call stack.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"In LCA, when you were looking for `p` and `q`, the tree gave you zero hints about which side to search — you had to recurse into both the left AND the right subtree every single time, at every node. That's why it cost you O(N): you couldn't skip anything."*

Pause, then: *"Today's structure is built specifically to kill that problem. What if, at every node, the tree itself told you exactly one side to look at — and you could ignore the other side completely?"*

Write on the board: `left < node < right`. *"That one rule is the entire session. Everything else — search, insertion, deletion, all the sessions coming after this one — is a consequence of that inequality."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: BST definition and ordering property → walked example building a 11-node tree node-by-node → BST properties (no duplicates by default, in-order traversal is strictly increasing, ordering invariant holds at *every* node, not just the root).

**Beats to emphasise**

- **"Both subtrees must also be BSTs" is not decoration — it's the whole point.** The deck's own worked example (root 9, checking node 4's subtree, then node 3's, then node 18's, then node 12's, then node 19's) exists specifically to show this is a recursive property, checked all the way down, not just root-vs-children.
- **In-order traversal → strictly increasing order** is the single most useful consequence of the BST property. Flag it explicitly — sessions 17–19 (Kth Smallest, Validate BST, Predecessor/Successor) are all built on this one fact.
- **No duplicates by default.** State it plainly and move on — it will matter the moment a student tries to insert an equal value in a later session.

**Checkpoint (at 18 min)** — cold-call two students:
> *"In one sentence: what has to be true about EVERY node in a BST, not just the root?"*
> **Answer:** Every node's value must be greater than everything in its left subtree and less than everything in its right subtree — and both of its subtrees must themselves obey that same rule.

---

## ⚡ Activity 1 — Spot the Bug: "Is This Still a BST?" (18–24 min)

**Format:** Spot the Bug · **Exposes:** the near-universal beginner shortcut of checking only "is my child on the correct side of me" instead of checking every node against the *full range* set by its ancestors. This is exactly the deck's own Property-2 example pair (slides on the "satisfied" vs. "not satisfied" trees).

**Setup line (say this):**
> *"I'm going to put up two trees that look almost identical. One is a valid BST. One isn't. Same shape, same rule you just learned — find the one node that breaks it, and tell me exactly why the local left/right comparisons at that node aren't enough to save it."*

Reconstruct both trees from the deck on the board:
- **Tree 1 (valid):** root 9; left subtree values `{4, 3, 1, 7}` all `< 9`; right subtree values `{18, 12, 11, 15, 19, 24}` all `> 9`. Every node checks out all the way down. → **Property satisfied.**
- **Tree 2 (invalid):** same shape, but one value has been swapped so that a node sitting in the **right** subtree of the root holds a value **smaller** than the root. Locally, that node might still look fine next to its immediate parent — but it's on the wrong side of an ancestor further up.

**What students do:** 90 seconds in pairs, then hands up. Take the answer from a pair that names the *specific* violating node, not just "something's wrong."

**How it surfaces:** If a pair only checks parent-vs-immediate-child pairs and declares Tree 2 valid, push: *"Check that node against the ROOT, not just against its parent. Is it still on the correct side?"* This is the exact mistake the optimal range-check approach (coming up in Session 18, Validate a Binary Search Tree) is built to prevent — plant that seed now.

**Debrief line:**
> *"A tree can pass every single local left-right comparison and still not be a BST. The rule isn't 'smaller than your parent' — it's 'smaller than every ancestor whose right side you're NOT on.' Hold onto that sentence; it comes back word-for-word later in this topic."*

**Cut rule:** If running late, skip reconstructing Tree 1 from scratch — state it's valid, put up only Tree 2, and ask students to find the violation.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–38 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Searching in a BST — Example 1 (search for 7, found) and Example 2 (search for 12, not found) → iterative pseudocode/code → recursive pseudocode/code → complexity analysis → Applications (searching, sorting, data storage, databases).

**Beats to emphasise**

- **Search is just "walk the rule."** At each node: equal → found; smaller → go left; larger → go right; hit `null` → not found. Nothing more.
- Run **both** worked examples live, node by node, exactly as the deck lays them out: search 7 in the tree `{9, 2, 11, 1, 5, 10, 14, 4, 7}` succeeds at depth 3 (9 → 2 → 5 → 7); search 12 in the same tree fails when the left child of 14 turns out to be `null`.
- On complexity: iterative search is **O(h) time, O(1) space** — no call stack. Recursive search is **O(h) time, O(h) space** — the call stack IS the extra cost. Say this pairing out loud; it is the exact contrast the checkpoint below tests.
- Applications slide (searching, sorting, data storage, databases) — 30 seconds, it's motivation not exam content.

**Checkpoint (at 38 min)** — show hands:
> *"Recursive BST search and iterative BST search — who says they have the SAME space complexity? Who says different?"*
> **Answer:** Different. Both are O(h) time, but iterative is O(1) space (a couple of pointers) and recursive is O(h) space (the call stack). Same time, different space — that's the trade you're always making between iterative and recursive on a tree.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: Search 7 vs. Search 12 (38–43 min)

**Format:** Dry-Run Relay · **Exposes:** whether students can independently execute the three-way branch (equal / smaller / larger) without you narrating each step.

**Setup line (say this):**
> *"Same tree as the slides — root 9, and I'm covering the answer. One relay for target 7, one relay for target 12. Each of you gets one comparison. State the comparison, state the direction, pass it on."*

Draw the tree: root `9`; left child `2` (children `1` and `5`, where `5`'s children are `4` and `7`); right child `11` (children `10` and `14`).

**Relay 1 — target 7 (should succeed):** `9`→(7<9)→left to `2` →(7>2)→right to `5` →(7>5)→right to `7` →(7==7)→**found**.

**Relay 2 — target 12 (should fail):** `9`→(12>9)→right to `11` →(12>11)→right to `14` →(12<14)→left child of `14` is `null` → **not found**.

**How it surfaces:** If a student says "not found" for 12 but stops one level too early (e.g., at node `11`), push: *"You haven't hit a `null` yet — where does the rule send you next?"*

**Debrief line:**
> *"Notice Relay 2 took the exact same number of steps as Relay 1 before it gave up. Search doesn't get slower to fail — it just walks until it either matches or falls off the tree. That's O(h), win or lose."*

**Cut rule:** If running short, run Relay 2 only (the "not found" case) — it's the one students get wrong more often, because they want to declare failure too early.

---

## Exit Ticket (43–45 min)

> On paper or in chat: *"Draw a 4-node BST of your own (any values). Then write, in one sentence, what would have to be true for a search for your smallest value to take the maximum possible number of steps."*
> **Answer shape:** The tree would have to be skewed (each node has only one child, forming a straight line) so that height `h` equals `n` — the smallest value sits at the end of a chain, not near the root.

Scan responses on the way out. If most students draw a *balanced* tree and can't articulate the skewed case, open Session 15 with a 60-second recap of `h ≈ n` vs. `h ≈ log n`.

Homework: re-attempt today's two search dry runs from memory, on paper, without the slide. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A node just needs to be less/greater than its *immediate* parent | The definition slide shows single parent-child comparisons first | Activity 1 — a tree that passes every local check yet isn't a BST |
| BST search and "just check every node" (linear scan) have the same complexity | Both feel like "walking the tree" | Contrasting O(h) search against O(n) full-tree LCA traversal from the warm-up poll |
| Recursive and iterative search have the same space cost because they "do the same thing" | Both produce the identical control flow on paper | The checkpoint after Slide Block B — O(1) vs O(h) side by side |
| BSTs allow duplicate values, "just insert them wherever" | Real-world data often has repeats | State the "no duplicates by default" property explicitly and leave it on the board |
| In-order traversal on any binary tree gives sorted order | The property is specific to BSTs but sounds like a generic traversal fact | Tie it explicitly to the ordering invariant — sorted order is a *consequence* of the BST property, not of in-order traversal alone |

---

## Instructor Notes

- **Pacing risk:** Activity 1 (Spot the Bug) is the emotional core of this session — the "looks valid but isn't" trap is what makes Validate-a-BST (Session 18) land later. Don't let it get rushed to save time elsewhere; if anything overruns, cut from Slide Block A's node-by-node example walk instead, since students already saw the general rule in the Hook.
- **The deck's "Property not satisfied" example (used in Activity 1) is visually dense** — the slide-to-slide extraction includes a repeated/ambiguous value in the raw text. Reconstruct the tree from the *values and relationships stated in the deck's own commentary* ("node in the right subtree holding a value smaller than the root"), not from trying to pixel-match the diagram. <!-- placement: inferred — source slide text for this specific tree is garbled in extraction -->
- **Have both trees for Activity 1 pre-drawn on the board or a slide before class starts** — reconstructing them live burns time you don't have in a 45-minute session.
- **This is the seed session for the whole BST topic.** The "left < node < right, recursively" sentence from Slide Block A and the "local check isn't enough" line from Activity 1 will be referenced verbatim in Sessions 15 (insertion), 16 (deletion), and especially 18 (Validate a BST). Say them exactly the same way each time if you want students to recognize the callback.
