# Session 4 — In-Order Traversal

**Duration** 45 min · **Topic** Binary Tree — In-Order Traversal · **Prerequisite** Pre-Order Traversal (Session 03)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — In-Order Traversal | https://docs.google.com/presentation/d/1xTVYlWV0ebpsEtbE6vmr8LydVqxXV533gYEYaKiq0X0/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define in-order traversal as the Left → Root → Right visiting order. *(REMEMBERING)*
2. Explain why the current node isn't processed until its entire left subtree has been visited (the recursive base case). *(UNDERSTANDING)* <!-- placement: inferred -->
3. Trace an in-order dry run on a given binary tree, correctly sequencing visits and backtracks, to produce the output list. *(APPLYING)*
4. Write the in-order recursive function (pseudocode, C++, or Python) from the Node template. *(APPLYING)*
5. Analyze the time and space complexity of in-order traversal — O(n) time; O(h) space, with best case O(log n) and worst case O(n). *(ANALYZING)*
6. Contrast in-order's Left → Root → Right pattern against pre-order's Root → Left → Right pattern (Session 03) using the shared recursive skeleton. *(ANALYZING)* <!-- placement: inferred -->

**Duration note:** 45 min (default), matching Session 03 — same single-traversal shape (problem → dry run → pseudocode → complexity → code), no additional topics. <!-- placement: inferred -->

---

## Warm-Up Poll — Retrieval Practice on Session 03 (0–7 min)

Say: *"Eight questions on pre-order before we touch anything new. Today's traversal reuses almost everything from last session — just one thing moves — so this recap is not optional."*

Reference tree for tree-specific questions below (Session 03's 8-node example): root = 1; children 2, 3; 2's children 4, 5; 5's right child = 8; 3's children 6, 7.

**Q1.** What is the visiting order for pre-order traversal?
`A` Left, Root, Right · `B` **Root, Left, Right ✅** · `C` Left, Right, Root · `D` Right, Root, Left

**Q2.** In pre-order's pseudocode, what happens when `root == null`?
`A` It prints "null" · `B` The program crashes · `C` **The function returns immediately — this is the base case ✅** · `D` It skips straight to the right child without returning
→ *Read:* If many pick "crashes," walk them back to the pseudocode. The null check isn't a failure path — it's what lets every recursive branch end cleanly. Today's in-order pseudocode uses the exact same base case.

**Q3.** For the reference tree, what is the pre-order output?
`A` 1,2,4,8,5,3,6,7 · `B` **1,2,4,5,8,3,6,7 ✅** · `C` 4,2,8,5,1,6,3,7 · `D` 1,3,6,7,2,5,8,4

**Q4.** In the dry run, right after visiting Node 4 (a leaf), where does the traversal backtrack to?
`A` Node 1 · `B` Node 5 · `C` **Node 2 ✅** · `D` Node 8
→ *Read:* Backtracking always returns to the immediate parent, one level at a time — never straight to the root. Watch for this exact pattern again in today's dry run.

**Q5.** What is pre-order's time complexity for a tree with n nodes?
`A` O(log n) · `B` **O(n) ✅** · `C` O(n log n) · `D` O(n²)

**Q6.** What is pre-order's space complexity, and what does `h` represent?
`A` O(n); the number of nodes · `B` **O(h); the height of the tree ✅** · `C` O(1); constant extra space · `D` O(n²); pairs of nodes
→ *Read:* If many pick O(1), they're forgetting recursion itself consumes call-stack space — there's no such thing as "free" recursion.

**Q7.** In the worst case — a fully skewed tree — what does pre-order's space complexity become?
`A` O(log n) · `B` **O(n) ✅** · `C` O(1) · `D` It stays O(h), and h is always small

**Q8 (MSQ — pick all that apply).** Which of these correctly describe pre-order traversal?
`A` **The root is visited before either subtree ✅** · `B` **The left subtree is fully traversed before the right subtree begins ✅** · `C` It requires an explicit stack — recursion isn't allowed · `D` **The base case triggers on hitting a null node ✅**

**Running it** — poll tool, ~50 s per question. Project the distribution after each question. Total 7 min including reads.

---

## Hook (7–10 min)

Put pre-order's pseudocode and in-order's pseudocode on the board side by side, unlabelled:

```
A                                    B
if (root == null) return             if (root == null) return
print(root->data)                    inorder(root->left)
preorder(root->left)                 print(root->data)
preorder(root->right)                inorder(root->right)
```

Ask: *"Compare A and B. Exactly one line moved. Find it before I tell you."*

Let them spot it: `print(root->data)` moved from right after the null check to between the two recursive calls.

Then: *"That's it. That one line's position is the entire difference between the traversal you learned last session and the one you're learning today. The base case, the two recursive calls — identical."*

---

## Slide Block A (10–19 min) — DELIVER SLIDES AS-IS

Covers: Problem Statement → Approach → Dry Run (the 9-node example tree: root 1; children 2, 3; 2's children 4, 5; 4's left child 8; 3's children 6, 7; 6's right child 9).

**Beats to emphasise**

- **The Approach flips pre-order's first rule.** Recurse left *completely* first — the current node is only processed once its entire left subtree is exhausted. Root does not go first anymore; root *waits*.
- **The base case is identical to pre-order's:** `if (root == null) return`. Point this out explicitly — students should recognise it as the same stopping mechanism, just triggering at different moments in the traversal.
- **Use the deck's own contrast pair:** Node 4 has only a left child (8) and no right child; Node 5 is a pure leaf. Both hit the base case on their missing side — the null check fires at every missing child, not only at leaves.
- Walk the dry run at the deck's own pace: 1 → 2 → 4 → 8 (leaf, visit, backtrack to 4) → visit 4 (no right, backtrack to 2) → right to 5 (leaf, visit, backtrack to 2) → visit 2 → backtrack to 1 → visit 1 → right to 3 → left to 6 → right to 9 (leaf, visit, backtrack to 6) → visit 6 (no left, right done, backtrack to 3) → right to 7 (leaf, visit, backtrack to 3) → visit 3 → backtrack to 1, done. Final sequence: **8, 4, 2, 5, 1, 6, 9, 3, 7.**

**Checkpoint (at 19 min)** — cold-call one student:
> *"What's the FIRST value added to the result in in-order traversal, and why is it almost never the root?"*
> **Answer:** The deepest node down the leftmost path (Node 8 here) — because in-order fully exhausts the left subtree, however deep it goes, before the root is ever processed.

---

## ⚡ Activity 1 — Dry-Run Relay (19–24 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students track the "go left as far as possible before you're allowed to visit" rule, versus applying pre-order's "visit immediately" habit out of muscle memory.

**Setup line (say this):**
> *"Same relay format as last session, new rule: you may only say 'visit' if there is nothing left to explore to that node's left. Point, relay — check the rule before you speak."*

**What students do:** Round-robin through the deck's own dry-run sequence on the 9-node tree, one node/move at a time, same relay mechanic as Session 03's activity.

**How to handle wrong answers:** If a student calls "visit" on Node 1 or Node 2 before its entire left subtree is exhausted, stop and ask: *"Has everything to the left of this node been visited yet?"* Force the check before allowing the visit call — this is exactly the habit pre-order doesn't require and in-order does.

**Debrief line:**
> *"In pre-order, root went first, no waiting. In in-order, root has to wait — every node in its left subtree gets a turn first. Same recursion, one rule reordered, completely different rhythm."*

**Cut rule:** If running short, skip the right subtree of Node 3 (nodes 6, 9, 7) and jump from "backtrack to Node 1, visit Node 1, go right to Node 3" to the completed sequence. Do not cut the debrief line.

---

## Classroom Quiz (24–29 min)

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–37 min) — DELIVER SLIDES AS-IS

Covers: Pseudocode → Time Complexity → Space Complexity → C++ Code → Python Code.

**Beats to emphasise**

- **Pseudocode:** `inorder(root){ if (root == null) return; inorder(root->left); print(root->data); inorder(root->right); }` — point at the print line's position exactly where the Hook predicted it would be.
- **Time complexity O(n):** same reasoning as pre-order — every node visited exactly once, no repeated work.
- **Space complexity O(h):** same call-stack reasoning as pre-order. Best case (balanced) O(log n), worst case (skewed) O(n). Today's tree has 9 nodes and height 3 (`1 → 2 → 4 → 8`), close to `log2(9) ≈ 3.17` — near the best case, not the worst.
- Run the C++/Python code as a *template* for the recursive shape, not as "this exact tree" — see Instructor Notes for a caveat on the code slide's example tree.

**Checkpoint (at 37 min)** — show hands:
> *"Same n, same tree family as last session. Are today's time and space complexities any different in form from pre-order's?"*
> **Answer:** No — O(n) time and O(h) space are identical in form to pre-order. Only the traversal *order*, and therefore the output sequence, has changed — not the cost.

---

## ⚡ Activity 2 — Predict-the-Output (37–41 min)

**Format:** Predict-the-Output · **Exposes:** whether students see the recursive skeleton as one fixed shape with `print`'s position as the only variable, versus treating each traversal as an unrelated algorithm to memorize separately.

**Setup line (say this):**
> *"If I move `print(root->data)` from the middle of today's pseudocode to the very top — before both recursive calls — what traversal have I just rebuilt, and what's the new output for THIS tree?"*

**What students do:** Predict individually or in pairs, then compare against the reveal.

**How to handle wrong answers:** If predictions reuse Session 03's 8-node answer instead of recomputing for today's 9-node tree, stop and re-walk Root → Left → Right on today's tree specifically, node by node.

**Debrief line:**
> *"You just rebuilt pre-order from in-order by moving one line. That's the whole family of DFS traversals — one skeleton, and one line's position decides which member of the family you get."*

**Answer key:** Pre-order (Root → Left → Right) output for this tree: **1, 2, 4, 8, 5, 3, 6, 9, 7.**

**Cut rule:** If running short, skip the individual-write step and go straight to a show-of-hands guess before revealing. Keep the reveal and the debrief line.

---

## Exit Ticket (41–45 min)

On paper or in chat before anyone leaves, give the same small transfer tree used in Session 03 (root 1; left child 2; right child 3; 2's left child 4): <!-- placement: inferred — same small tree as Session 03's exit ticket, reused deliberately so the only variable is the traversal rule, not the tree -->

> Write the in-order traversal of this tree: root = 1, 1.left = 2, 1.right = 3, 2.left = 4.
> **Answer:** `4, 2, 1, 3`.

Scan responses on the way out. If students write `1, 2, 4, 3` (pre-order's answer for this same tree), that's a direct signal the Left-before-Root rule didn't override yesterday's habit — open Session 5 with a 60-second recap contrasting the two.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| "Left → Root → Right" is read as "do the left subtree at some point before the root, right subtree whenever" rather than a strict order | The words look like a loose checklist rather than a strict sequence | The dry run's explicit backtrack narration — Activity 1's rule check ("has everything to the left been visited yet?") |
| Today's Left-Root-Right blurs with yesterday's Root-Left-Right | Only one word's position differs between the two mnemonics | The Hook's side-by-side pseudocode comparison and Warm-Up Poll Q1 |
| The base case (null check) only matters at true leaf nodes | Most textbook examples only show it firing at leaves | Node 4 in the dry run — it has a left child but a null right child, and the base case still fires on that missing side |
| Space complexity is O(1) because recursion "doesn't use extra memory" | Same misconception carried over from Session 03 — the call stack is invisible | Slide Block B's checkpoint, computing `h` for the actual on-screen tree |

---

## Instructor Notes

- **Pacing risk:** the dry run is the longest single block again this session — protect it, use Activity 1's cut rule if you're behind, not the dry run itself.
- **Known deck inconsistency — do not surface to students.** The C++/Python code slides in this session build their example tree with `root->left->right->right = new Node(8)` (an 8-node tree matching Session 03's tree, where node 8 hangs off Node 5). This does **not** match this session's own dry-run tree, where node 8 is Node 4's left child and a 9th node exists (Node 9, off Node 6). The dry-run tree is the one stated in the Problem Statement's Example 1 output (`8 4 2 5 1 6 9 3 7`) and is the one used throughout this lesson plan. Treat the code slides as a generic template of the recursive *shape* only — don't narrate "this exact tree" while showing the code's construction, or the numbers won't match what students just dry-ran. <!-- flagged per assignment instructions: genuinely ambiguous in source material, not routine inferred-placement -->
- **Arc continuity:** this is session 2 of the 3-session pre/in/post-order arc. The Hook and Activity 2 both lean on that continuity — don't skip them even under time pressure, they're doing double duty as retrieval practice on Session 03.
- **Warm-Up Poll** is now self-sourced from this deck's own Session 03 plan rather than a separate source file — verify the 8-node tree and its pre-order answer against the live Session 03 slides before class.
