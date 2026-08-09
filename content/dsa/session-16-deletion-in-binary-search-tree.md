# Session 16 — Deletion in Binary Search Tree

**Duration** 50 min · **Topic** Binary Search Tree · **Prerequisite** Insertion in Binary Search Tree
**Session type** Concept lecture

<!-- placement: inferred — 50 min instead of the 45-min default because the deck covers three deletion cases in full, PLUS both a recursive and an iterative (parent-pointer) implementation, which is meaningfully denser than a single-approach session. -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Deletion in Binary Search Tree | https://docs.google.com/presentation/d/1eIV4xw5ICsy5DwJWymzGtJw-VSEk4bb6Rg400LDMBG0/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the three deletion cases in a BST — leaf, one child, two children — and the replacement rule for each. *(REMEMBERING)*
2. Trace deletion of a leaf node, a one-child node, and a two-children node on a given BST. *(APPLYING)*
3. Explain why a two-children deletion replaces the node's value with its in-order successor (or predecessor) rather than simply relinking its children. *(UNDERSTANDING)*
4. Justify why deletion's overall time complexity remains O(h) even in the two-children case, by decomposing it into a search phase and an adjustment phase. *(ANALYZING)*
5. Compare the recursive (call-stack-based) and iterative (parent-pointer-based) deletion implementations. *(UNDERSTANDING)* <!-- placement: inferred — phrasing built from the deck's two full implementations, slides 36–41 and 47–60 -->

---

## Warm-Up Poll — Retrieval Practice on Insertion in BST (0–6 min)

Say: *"Six on yesterday's insertion before we do the opposite operation."*

**Q1.** BST insertion rule: go left if the value is ___, go right if the value is ___.
`A` smaller / greater · `B` greater / smaller · `C` equal / unequal · `D` odd / even
→ *Read:* A.

**Q2. (True/False)** Iterative and recursive insertion use the same amount of auxiliary space.
→ *Read:* False. Iterative is O(1); recursive is O(h) because of the call stack. This was last session's #1 mix-up — if it's still shaky, restate it now before deletion adds a second layer of recursion reasoning.

**Q3.** In yesterday's "broken insertion" example, the deck placed value 13 as the left child of node 1. Why was that wrong?
`A` 13 is too large to exist in the tree · `B` 13 > 1, but a left child must be smaller than its parent · `C` Node 1 already had a left child · `D` Nothing was wrong
→ *Read:* B.

**Q4. (MSQ)** Which deletion cases were previewed at the end of yesterday's session? *(pick all that apply)*
`A` Leaf node · `B` Node with one child · `C` Node with two children · `D` Deleting the entire tree
→ *Read:* A, B, C.

**Q5.** In the two-children preview example, node 11 (children 10 and 14) was replaced by which value?
`A` 10 · `B` 14 · `C` 9 · `D` The node was removed with nothing put in its place
→ *Read:* B — the deck used the in-order successor.

**Q6.** What is the time complexity of BST insertion, iterative or recursive, in terms of height `h`?
`A` O(1) · `B` O(log n) always · `C` O(h) · `D` O(n)
→ *Read:* C.

**Q7.** For a skewed BST, `h` is approximately…
`A` log n · `B` n · `C` constant · `D` n/2
→ *Read:* B.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Yesterday's whole session was: find the empty spot, plant a node. Today is the opposite problem — you have to remove a node WITHOUT leaving a hole in the tree's shape. The entire session is really just one question, asked three different ways depending on how many children the node you're deleting has: what do you put in the hole?"*

---

## Slide Block A (9–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Deletion approach overview → Case 1: deleting a leaf (delete 7, full dry run) → Case 2: deleting a node with one child (delete 5, full dry run) → Case 3: deleting a node with two children (delete 11, full dry run).

**Beats to emphasise**

- Open with the three-case decision tree from the approach slide: *"Find the node the same way you'd search for it. Then look at how many children it has — that decides everything that happens next."*
- Walk **all three dry runs live**, on the shared tree `{9, 4, 11, 1, 10, 14, 2, 5, 7}` (root 9; left child 2 with children 1 and 5; 5's children 4 and 7; right child 11 with children 10 and 14):
  - **Delete 7 (leaf):** search `9→2→5→7` (comparing 7 against each), found, no children → **remove it outright, nothing to reattach.**
  - **Delete 5 (one child):** search `9→2→5`, found, only child remaining is `4` → **5 is removed and 4 takes its place** as the child of node 2.
  - **Delete 11 (two children):** search `9→11`, found, children are `10` and `14` → **11's value is replaced by 14** (its in-order successor — the smallest value in 11's right subtree), and the old node holding 14 is removed instead.
- Land the general rule explicitly after all three: *"Every deletion is a search, followed by exactly one of three cleanup moves. The first two moves are just pointer surgery. The third move is the only one that needs a NEW value from somewhere else in the tree."*

**Checkpoint (at 24 min)** — cold-call two students:
> *"Why can't you just delete node 11 and reattach both 10 and 14 as children of node 9 directly?"*
> **Answer:** Node 9 can only have one right child. You'd also lose the ordering between 10 and 14 relative to whatever else sits under them — replacing 11's *value* with a value that's already correctly ordered (the successor) is the only way to keep the rest of the subtree intact without re-inserting everything.

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: Three Deletions, Three Students (24–31 min)

**Format:** Dry-Run Relay · **Exposes:** whether students can identify which of the three cases applies BEFORE they try to fix anything, and whether they instinctively reach for "just remove it" without asking what has to be reattached.

**Setup line (say this):**
> *"Same tree as the slides. I'm assigning each of you one of the three deletions — leaf, one child, two children. Walk me through it exactly like a search: compare, move, and when you find the target, tell me what happens to the pointers around it."*

Reuse the tree from Slide Block A. Assign:
- **Student A:** delete 7 (leaf)
- **Student B:** delete 5 (one child, only child remaining is 4)
- **Student C:** delete 11 (two children, 10 and 14)

**How it surfaces:** If a student says "just remove it" without stating what replaces it, push: *"Remove it, and then what's sitting in the hole? Nothing? A child? A borrowed value?"* For Student C specifically, if they try to promote both 10 and 14 at once, stop: *"A node can't have two right children. Pick ONE value that keeps everything sorted — where does it come from?"*

**Debrief line:**
> *"Every deletion is secretly still a search, then one of exactly three cleanup moves. Leaf: nothing to reattach. One child: promote it. Two children: borrow the successor's value, then go delete THAT node instead — which is now guaranteed to be a leaf or one-child case."*

**Cut rule:** If running short, run Student A and Student C only — the leaf case and the two-children case bracket the whole idea; the one-child case is the easiest to describe verbally without a full relay.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (36–45 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Recursive pseudocode/code for deletion → complexity analysis (search phase O(h) + adjustment phase O(1) for 0/1-child or O(h) for 2-children, overall O(h)) → the iterative approach with explicit parent-pointer tracking.

**Beats to emphasise**

- Walk the recursive pseudocode's two-children branch specifically: `cur = root->right; while (cur->left) cur = cur->left;` — this loop IS "find the in-order successor," expressed as "go right once, then keep going left." Connect it back to Slide Block A's delete-11 example: 11's right child is 14, which has no left child, so the loop stops immediately and returns 14.
- On complexity, walk the decomposition explicitly: *"Total time = search to find the node (O(h)) + cleanup. For 0 or 1 child, cleanup is O(1) pointer surgery, so total is O(h) + O(1) = O(h). For 2 children, cleanup means ANOTHER search — for the successor — which costs O(h) in the worst case, so total is O(h) + O(h), which is still just O(h)."* The "two O(h)'s add up to O(h), not O(h²)" point is the one that trips students.
- The iterative approach (parent-pointer tracking) is denser — it maintains `parent` and `current` explicitly because, without recursion, there's no call stack remembering "how did I get here." Frame it as: *"Recursion gets the parent reference for free, on the stack. Iteration has to track it by hand."*

**Checkpoint (at 45 min)** — show hands:
> *"Two O(h) searches happen during a two-children deletion — finding the node, then finding its successor. Who thinks that makes the total O(h²)? Who thinks it's still O(h)?"*
> **Answer:** Still O(h). The successor search only ever walks down 11's *right subtree*, which is itself bounded by the tree's height — the two searches are sequential, not nested, so their costs **add**, not multiply: O(h) + O(h) = O(h).

---

## ⚡ Activity 2 — Predict the Output: The Successor-Finding Loop (45–49 min)

**Format:** Predict-the-Output · **Exposes:** whether students actually trust the `while (cur->left) cur = cur->left` loop to find the successor, or are still trying to compute it by inspecting the whole subtree.

**Setup line (say this):**
> *"One loop: start at the deleted node's right child, keep going left until you can't. Whatever you land on is the successor. I'll give you two versions of the same tree — predict what the loop returns before I confirm."*

- **Version 1 (from the deck):** node 11's right child is `14`, and `14` has no left child. → **Predicted successor: 14** (loop stops immediately).
- **Version 2 (a one-step extension of the same rule):** suppose `14` *did* have a left child, `12`. → **Predicted successor: 12** (loop now takes one more step left before stopping). <!-- placement: inferred — a minimal hypothetical extension of the deck's own tree, used only to test the loop's general behaviour, not new algorithm content -->

**How it surfaces:** If students predict `14` for Version 2 as well, push: *"The loop says keep going left WHILE there's a left child. Does 14 have one now?"*

**Debrief line:**
> *"The rule was never 'the right child.' It's 'go right once, then hug the left wall until you can't anymore.' That's the smallest value in the right subtree, which is exactly the next value up from the one you deleted."*

**Cut rule:** Run Version 1 only, verbally, without drawing Version 2 — state the extension as a rhetorical question instead of a full dry run.

---

## Exit Ticket (49–50 min)

> On paper or in chat: *"In one sentence, why is deletion's overall time complexity O(h) even in the two-children case, when it requires two separate searches?"*
> **Answer:** Both searches are bounded by the tree's height and happen one after the other (not nested), so their costs add rather than multiply: O(h) + O(h) = O(h).

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Deleting a two-children node just removes it and reattaches both children arbitrarily | Leaf and one-child deletion feel like "just disconnect it" | Activity 1, Student C — forcing the "a node can only have one right child" contradiction |
| Two O(h) searches (node + successor) make deletion O(h²) | "Two loops" reads as "multiply the costs" by analogy with nested loops | The checkpoint after Slide Block B — sequential vs. nested cost |
| The successor-finding loop looks at the whole right subtree to pick the smallest value | Without tracing the loop, "smallest in a subtree" sounds like it needs a full scan | Activity 2 — showing the loop is just "right once, then all-the-way left" |
| Recursive and iterative deletion are fundamentally different algorithms | The iterative version's explicit `parent`/`current` bookkeeping looks unfamiliar next to the compact recursive version | Naming the parity directly: both do the same search + one of the same three cleanup moves; iteration just tracks `parent` by hand instead of getting it free from the call stack |

---

## Instructor Notes

- **Pacing risk:** the iterative parent-pointer implementation is the densest material in the session (it's also the densest in the whole deck's raw slide text — many short slides, one code fragment growing across them). Deliver it at a brisk, high-level pace ("this does the same three cases, just without recursion") rather than tracing every pointer update live; Activity 2 already covers the one piece of iterative-style reasoning (the successor loop) worth slowing down for.
- **The exact resulting tree shape after the two-children deletion (delete 11) is drawn on the original slides but not fully recoverable from extracted text** — teach the *values* (11 replaced by 14) with confidence, since that's stated explicitly, but don't over-commit to describing the exact final left/right child arrangement beyond what's given here. <!-- placement: inferred — source slide diagrams for the post-deletion tree shape are ambiguous in text extraction -->
- **This session is 5 minutes over the 45-minute default** for the same reason as Session 15 — two full implementations (recursive + iterative) of the same algorithm is genuinely more content than a single-approach session. If you need to claw back to 45, cut Activity 2 to its verbal-only cut-rule version.
- **Callback opportunity:** the "local check isn't enough, you need the full picture" idea from Session 14's Spot-the-Bug activity resurfaces here in a new form — the successor value has to be correct relative to the ENTIRE subtree it's leaving, not just the node it's replacing. Worth a 10-second verbal callback if time allows, not worth building a slide for.
