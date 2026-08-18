# Session 5 — Post-Order Traversal

**Duration** 45 min · **Topic** Binary Tree — Post-Order Traversal · **Prerequisite** In-Order Traversal (Session 04)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Post-Order Traversal | https://docs.google.com/presentation/d/1H1xaYYsNyEzE5sHJ03TPB-VkDnYUx0W0amazJ4uK_Vk/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define post-order traversal as the Left → Right → Root visiting order. *(REMEMBERING)*
2. Explain why the current node is processed only after both of its subtrees are fully traversed. *(UNDERSTANDING)* <!-- placement: inferred -->
3. Trace a post-order dry run on a given binary tree, correctly sequencing visits and backtracks, to produce the output list. *(APPLYING)*
4. Write the post-order recursive function (pseudocode, C++, or Python) from the Node template. *(APPLYING)*
5. Analyze the time and space complexity of post-order traversal — O(n) time; O(h) space, with best case O(log n) and worst case O(n). *(ANALYZING)*
6. Contrast post-order's Left → Right → Root pattern against pre-order (Root → Left → Right, Session 03) and in-order (Left → Root → Right, Session 04), recognising all three share one recursive skeleton. *(ANALYZING)* <!-- placement: inferred -->

**Duration note:** 45 min (default), matching Sessions 03–04 — same single-traversal shape, and this is the closing session of the pre/in/post-order arc rather than a heavier one. <!-- placement: inferred -->

---

## Warm-Up Poll — Retrieval Practice on Session 04 (0–7 min)

Say: *"Last set of eight before we close out this three-session run. Today's traversal is the last piece of the same recursive skeleton — get in-order solid now, or today will feel like a new algorithm instead of one more small move."*

Reference tree for tree-specific questions below (Session 04's 9-node example): root = 1; children 2, 3; 2's children 4, 5; 4's left child = 8; 3's children 6, 7; 6's right child = 9.

**Q1.** What is the visiting order for in-order traversal?
`A` Root, Left, Right · `B` **Left, Root, Right ✅** · `C` Left, Right, Root · `D` Right, Left, Root

**Q2.** In the in-order pseudocode, where does `print(root->data)` sit relative to the two recursive calls?
`A` Before both · `B` **Between them ✅** · `C` After both · `D` There is no print statement

**Q3.** For the reference tree, what is the in-order output?
`A` 1,2,4,8,5,3,6,9,7 · `B` **8,4,2,5,1,6,9,3,7 ✅** · `C` 8,4,5,2,9,6,7,3,1 · `D` 4,8,2,5,1,9,6,7,3
→ *Read:* Option C is today's post-order answer for this exact tree. If that gets picked, it's a live preview of exactly how easily the two blur together — which is the whole reason today matters.

**Q4.** Right after visiting Node 8 (the deepest left node) in the dry run, what happens next?
`A` Backtrack straight to Node 1 · `B` **Backtrack to Node 4, visit Node 4, then backtrack to Node 2 ✅** · `C` Visit Node 5 · `D` Visit Node 9
→ *Read:* Recursion always returns to its immediate caller, one level at a time. If "straight to Node 1" gets picked, re-show the backtrack arrows before today's dry run.

**Q5.** What is the base case that stops in-order's recursion?
`A` When node.data == 0 · `B` **When root is null ✅** · `C` When the node has no left child · `D` After exactly 9 calls

**Q6.** What is in-order's time complexity for n nodes?
`A` O(log n) · `B` **O(n) ✅** · `C` O(n²) · `D` O(n log n)

**Q7.** In the worst case (a fully skewed tree), what is in-order's space complexity?
`A` O(1) · `B` O(log n) · `C` **O(n) ✅** · `D` O(h), and h is always small
→ *Read:* D is a trap, not necessarily wrong — O(h) is the general answer, and in a skewed tree `h = n`, so the two agree. Make sure students see that both descriptions are consistent, not contradictory.

**Q8 (MSQ — pick all that apply).** Which describe in-order traversal correctly?
`A` **The left subtree is fully processed before the root is visited ✅** · `B` The root is visited before either subtree · `C` The right subtree is processed before the root is visited · `D` **The recursive structure shares the same base case as pre-order ✅**

**Running it** — poll tool, ~50 s per question. Project the distribution after each question. Total 7 min including reads.

---

## Hook (7–10 min)

Put in-order's pseudocode on the board with the `print` line highlighted in the middle:

```
if (root == null) return
inorder(root->left)
print(root->data)      <-- here, last session
inorder(root->right)
```

Ask: *"Two sessions ago, print sat first. Last session, it sat in the middle. Where do you think it sits today — for a traversal literally called 'post'-order — and why does the name basically hand you the answer?"*

Let guesses land on "last" / "after both recursive calls."

Then: *"You just derived the entire rule from one word. Today's dry run is just proof."*

---

## Slide Block A (10–19 min) — DELIVER SLIDES AS-IS

Covers: Problem Statement → Approach → Dry Run (the same 9-node example tree as Session 04: root 1; children 2, 3; 2's children 4, 5; 4's left child 8; 3's children 6, 7; 6's right child 9).

**Beats to emphasise**

- **The Approach puts the current node last, after both recursive calls.** Recurse left fully, recurse right fully, *then* visit. This is the mirror image of pre-order's "root always first" — call that mirror out explicitly.
- **The base case is identical again:** `if (root == null) return`. Three sessions running the same stopping mechanism — worth naming outright.
- **The dry run's own language marks the difference from the last two sessions:** it says "Visit Node X → Done with Node X → Go back to Node Y." "Done with" only happens once a node has been fully visited — contrast this with pre-order/in-order's backtracks, which could happen before or after a visit depending on the node.
- Walk the dry run: 1 → 2 → 4 → 8 (leaf, visit, done, back to 4) → visit 4 (no right, done, back to 2) → right to 5 (leaf, visit, done, back to 2) → visit 2 (both subtrees done, back to 1) → right to 3 → left to 6 → right to 9 (leaf, visit, done, back to 6) → visit 6 (no left, right done, back to 3) → right to 7 (leaf, visit, done, back to 3) → visit 3 (both done, back to 1) → visit 1 (both done). Final sequence: **8, 4, 5, 2, 9, 6, 7, 3, 1.**

**Checkpoint (at 19 min)** — cold-call one student:
> *"Why must Node 1 — the root — always be the LAST value in post-order's result, no matter what shape the tree is?"*
> **Answer:** Because a node is only visited once BOTH its subtrees are fully done, and the root's two subtrees are, by definition, everything else in the tree.

---

## ⚡ Activity 1 — Dry-Run Relay (19–24 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students apply the "both sides must be done before you can visit" rule, versus defaulting to pre-order's or in-order's visit timing out of habit.

**Setup line (say this):**
> *"Same relay, new rule: you may only say 'visit' if BOTH the left and right side of that node have already been fully handled. Point, relay — check both sides before you speak."*

**What students do:** Round-robin through the deck's own dry-run sequence on the 9-node tree, one move at a time — "go left," "go right," "visit (done)," or "backtrack to node X" — same mechanic as Sessions 03 and 04.

**How to handle wrong answers:** If a student calls "visit" on Node 2 before Node 5 has been handled, stop and ask: *"Is the right side of this node done yet?"* Force the check before allowing the visit call.

**Debrief line:**
> *"Pre-order: root goes first, no waiting. In-order: root waits for the left. Post-order: root waits for BOTH sides. Same three rules you've now seen in three sessions, three different points where the root gets its turn."*

**Cut rule:** If running short, skip the right subtree of Node 3 (nodes 6, 9, 7) and jump from "backtrack to Node 1" to the completed sequence. Do not cut the debrief line — it's the capstone statement for the whole 3-session arc.

---

## Classroom Quiz (24–29 min)

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–37 min) — DELIVER SLIDES AS-IS

Covers: Pseudocode → Complexity Analysis → C++ Code → Python Code.

**Beats to emphasise**

- **Pseudocode:** `postorder(root){ if (root == null) return; postorder(root->left); postorder(root->right); print(root->data); }` — print sits after both recursive calls, exactly where the Hook predicted.
- **Time complexity O(n):** each of the n nodes is visited exactly once; recursive calls process each node exactly one time; no redundant operations — same reasoning across all three sessions.
- **Space complexity O(h):** same call-stack reasoning as pre-order and in-order. Best case (balanced) O(log n), worst case (skewed) O(n).
- Run the C++/Python code as a *template* of the recursive shape — see Instructor Notes for the same code/tree caveat flagged in Session 04.

**Checkpoint (at 37 min)** — show hands:
> *"Pre-order, in-order, post-order — same tree, same n=9. Do the time and space complexities actually differ across the three traversals?"*
> **Answer:** No — O(n) time and O(h) space are identical in form across all three. Only the traversal order, and therefore the output sequence, differs — never the cost.

---

## ⚡ Activity 2 — Predict-the-Output (37–41 min)

**Format:** Predict-the-Output · **Exposes:** whether students understand post-order's Left → Right → Root *rule* specifically, versus having memorized this tree's one sequence.

**Setup line (say this):**
> *"One more swap, same move as Session 3. `postorder(root->left)` and `postorder(root->right)` trade places. Root is still visited last — that part never changes in post-order. Predict the new sequence before I reveal it."*

**What students do:** Individually or in pairs, write a predicted sequence for the swapped pseudocode on the same 9-node tree, then compare against the reveal.

**How to handle wrong answers:** The common wrong guess is a straight reversal of the original list. If that comes up, walk the swapped version live, node by node, showing that only the left/right pairing at each node inverts — root still finishes last regardless, because print still sits after both recursive calls.

**Debrief line:**
> *"Root was last both times — that never changes in post-order. What changed is which subtree finishes first. This is the exact mirror of what you predicted in Session 3 with pre-order, just anchored at the other end of the list."*

**Answer key:** Swapped (Right → Left → Root) output for this tree: **7, 9, 6, 3, 5, 8, 4, 2, 1.**

**Cut rule:** If running short, skip the individual-write step and go straight to a show-of-hands guess before revealing. Keep the reveal and the debrief line.

---

## Exit Ticket (41–45 min)

On paper or in chat before anyone leaves, give the same small transfer tree used in Sessions 03 and 04 (root 1; left child 2; right child 3; 2's left child 4): <!-- placement: inferred — same small tree reused across all three sessions so the only variable across the arc is the traversal rule, not the tree -->

> Write the post-order traversal of this tree: root = 1, 1.left = 2, 1.right = 3, 2.left = 4.
> **Answer:** `4, 2, 3, 1`.

Scan responses on the way out. Across the three exit tickets for this tree, the answers are `1,2,4,3` (pre-order), `4,2,1,3` (in-order), `4,2,3,1` (post-order) — if a student's answer matches either of the other two instead of today's, that's a precise signal of which traversal rule is still not distinct in their head.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The root is visited last just as "leftover bookkeeping" rather than for a structural reason | Post-order is taught after the other two, so "last" can feel arbitrary | Pointing at the Approach's own wording — "visit the current node last" — and the checkpoint question on why the root can't go earlier |
| Left-Right-Root (post-order) blurs with Left-Root-Right (in-order) | Both start with "Left," and both were just taught back-to-back | Activity 2's three-way pseudocode contrast, and Warm-Up Poll Q3's deliberate distractor using this session's own answer |
| Backtracking "discards" a node's value once you move past it | The word "backtrack" sounds like undoing something | The dry run's own phrasing — "Visit Node X → Add to result" happens before "Done with Node X → Go back" — visiting and backtracking are separate, sequential steps |
| Post-order is assumed to be more expensive than pre-order/in-order because it's taught last | Teaching order gets mistaken for a complexity ranking | Slide Block B's checkpoint, showing identical O(n)/O(h) across all three sessions |

---

## Instructor Notes

- **Pacing risk:** as in Sessions 03–04, the dry run is the longest block — protect it, use Activity 1's cut rule if behind schedule.
- **Known deck inconsistency — do not surface to students.** As in Session 04, this session's C++/Python code slides build their example tree with `root->left->right->right = new Node(8)` (Session 03's 8-node tree, where node 8 hangs off Node 5), which does not match this session's own dry-run tree (9 nodes, node 8 as Node 4's left child, node 9 off Node 6). Use the dry-run tree — confirmed by the Problem Statement's own Example 1 output (`8 4 5 2 9 6 7 3 1`) — as ground truth throughout the live class, and treat the code slides as a generic template only. <!-- flagged per assignment instructions: genuinely ambiguous/inconsistent in source material, not routine inferred-placement -->
- **This closes the 3-session arc.** Consider a 60-second explicit recap at the very end (outside the 45-minute block, or trimmed from Slide Block B's beats if time allows): same recursive skeleton, same base case, only `print`'s position moved three times — top, middle, bottom. Session 03's and 04's exit tickets, plus today's, form a matched set on the same small tree specifically so this recap can point at three concrete answers side by side.
- **Warm-Up Poll** is self-sourced from this deck's own Session 04 plan — verify the 9-node tree and its in-order answer against the live Session 04 slides before class.
