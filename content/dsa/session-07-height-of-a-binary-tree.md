# Session 7 — Height of a Binary Tree

**Duration** 50 min <!-- placement: inferred — heavier than the 45-min default; 123 slides, three separate full worked traces of the same example tree (an intro walkthrough, a guided dry run, and a granular per-recursive-call retrace with h(x) notation), plus a genuinely distinct conceptual nuance (height-by-nodes vs. height-by-edges) that changes the base case and is a known gotcha the deck calls out explicitly --> · **Topic** Binary Tree — Properties · **Prerequisite** Level Order Traversal (Session 06)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Height of a Binary Tree | https://docs.google.com/presentation/d/1hyM0duOwFw78I_a7majxxe6ZGF_9icnJtlwgQNvfujs/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define the height of a binary tree as the number of nodes along the longest path from the root to a leaf, and state the two base cases: an empty tree has height 0, a single-node tree has height 1. *(REMEMBERING)* <!-- placement: inferred phrasing, grounded in Slides 4, 8 -->
2. Explain why height is computed recursively as `1 + max(leftHeight, rightHeight)`, and why the recursion bottoms out at a null node returning 0. *(UNDERSTANDING)* <!-- placement: inferred phrasing, grounded in Slides 9, 37 -->
3. Trace the recursive height computation bottom-up on a given binary tree, computing each node's height only once both of its children's heights are known. *(APPLYING)* <!-- placement: inferred phrasing, grounded in the dry run, Slides 10-28 -->
4. Distinguish the "number of nodes" convention for height from the "number of edges" convention, and adjust the algorithm's base case (`0` vs. `-1`) accordingly. *(ANALYZING)* <!-- placement: inferred phrasing, grounded in Slides 34-35, which state this distinction directly -->
5. State and justify the time and space complexity of the recursive height algorithm — O(N) time; O(H) space, worst case O(N) for a skewed tree. *(ANALYZING)* <!-- placement: inferred phrasing, grounded in Slides 30-31, 38, 115-120 -->

---

## Warm-Up Poll — Retrieval Practice on Session 06: Level Order Traversal (0–6 min)

Say: *"Eight quick ones on yesterday's level order traversal. No names, no grades — just tell me what's still there."*

**Q1.** What data structure does level order traversal use to process nodes in order?
`A` Stack · `B` Queue · `C` Array · `D` Linked list
→ *Read:* B. If this misses, the whole session was about the wrong data structure — worth a 20-second re-anchor before Q2.

**Q2.** Level order traversal visits nodes:
`A` Root, then left subtree, then right subtree, recursively · `B` All nodes at one depth before moving to the next depth, left to right · `C` Left, then right, then root · `D` Alternating left and right, one node at a time

**Q3.** For the tree used in session 06's dry run (root 1; children 2, 3; 2's children 4, 5; 3's children 6, 7), what is the level-order output?
`A` 1 2 4 5 3 6 7 · `B` 1 2 3 4 5 6 7 · `C` 4 5 6 7 2 3 1 · `D` 1 3 2 7 6 5 4
→ *Read:* B — direct recall of yesterday's worked dry run.

**Q4.** *(MSQ — pick all that apply)* Which of these are true of the queue as used in level order traversal?
`A` First In, First Out · `B` Last In, First Out · `C` Nodes are removed from the front and children are added at the back · `D` It's the same structure the call stack uses in recursive traversals
→ **Answer:** A and C.

**Q5.** What is the time complexity of level order traversal?
`A` O(log N) · `B` O(N) · `C` O(N²) · `D` O(H)

**Q6.** What is the worst-case space complexity of the queue in level order traversal, and when does it occur?
`A` O(1), always · `B` O(N), only when the tree is skewed · `C` O(N), at the widest level of a full/complete tree · `D` O(H), always
→ *Read:* C. This is the one students most often mix up with recursive traversals' O(H) — worth re-stating before moving on.

**Q7.** In the deck's cinema-ticket-line analogy for how a queue behaves, who gets served first?
`A` Whoever is at the back of the line · `B` Whoever is at the front of the line · `C` Whoever shouts loudest · `D` It's random
→ *Read:* B. Hold onto this — today has no queue and no line at all. We're back to pure recursion.

**Q8.** Level order traversal is an example of which traversal strategy?
`A` Depth-First Search · `B` Breadth-First Search · `C` Binary Search · `D` In-order traversal
→ *Read:* B. Today's topic sits back on the Depth-First side of that split — recursion, call stack, no queue.

**Running it** — poll tool, ~40 s/question, project the distribution after each. Total 6 min including reads.

---

## Hook (6–9 min)

Say: *"Yesterday we needed an explicit queue sitting outside the tree to get the order right. Today, no queue at all — we're back to plain recursion, and the question is almost embarrassingly simple: how tall is this tree?"*

Draw a tree with at least three different root-to-leaf paths of different lengths (mirror the deck's own opening image, Slides 4-6, which labels three candidate paths A, B, and C on the same tree). Ask: *"Three different paths from root to a leaf, three different lengths. Which one is 'the height' of this tree — the shortest, the longest, or do we average them?"*

Let guesses land, then confirm: *"The longest one. Height is always about the worst case — the deepest a search could possibly have to go."* <!-- placement: inferred hook, built directly from the deck's own three-paths framing on Slides 4-6 -->

---

## Slide Block A (9–20 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 1-9: Welcome/title/agenda, height definition (two equivalent framings), problem statement with base cases, recursive approach -->
Covers: Height defined two equivalent ways — "number of levels in the tree" and "number of nodes along the longest root-to-leaf path" → Problem Statement, with the explicit base-case notes: height of an empty tree is 0, height of a single-node tree is 1 → Approach: if the tree is empty, height is 0; recursively find the left subtree's height and the right subtree's height; the current node's height is `1 + max(leftHeight, rightHeight)`.

**Beats to emphasise**

- **The two definitions are the same number, seen two ways.** "Number of levels" and "number of nodes in the longest root-to-leaf path" always agree — say this explicitly, because the deck presents them as two separate framings without stating outright that they're equivalent. <!-- placement: inferred connective statement; the deck shows both framings (Slides 4-5, and again as "Method 1"/"Method 2" later) but does not explicitly say they always produce the same number -->
- **Read the two base cases as a pair, not separately:** empty tree → 0, single node → 1. Students will try to reason about one without the other; make them recite both together.
- **The formula has exactly three moving parts:** left subtree's height, right subtree's height, and `1 +`. Nothing else. Say it as one sentence: *"Ask both children how tall they are, take the taller answer, add one for yourself."*

**Checkpoint (at 20 min)** — cold-call two students:
> *"State the recursive formula for a node's height in one sentence, and tell me what a `null` node returns."*
> **Answer:** A node's height is 1 plus the larger of its left and right subtree heights; a `null` node returns 0.

---

## ⚡ Activity 1 — Live Dry-Run Relay: Bottom-Up Height (20–26 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** the assumption that recursion evaluates top-down (root's answer known first); in reality the deepest calls return first, and every parent is stuck waiting on both children.

**Setup line (say this):**
> *"Here's the tree from the dry run: root 1; 1's left child is 2, right child is 3; 2 has no left child but its right child is 4; 3's left child is 5 and right child is 6; 5's left child is 7 and has no right child. I am not going to write a single height value on the board unless one of you gives it to me — and I will only accept a node's height once you've already told me both of its children's heights."*

**What students do:** Called on in the order the recursion actually resolves (matching the deck's own dry run, Slides 10-28):
- Node 4 (leaf): height 1
- Node 2: `1 + max(0, 1) = 2` (left height 0 — no left child)
- Node 7 (leaf): height 1
- Node 5: `1 + max(1, 0) = 2` (right height 0 — no right child)
- Node 6 (leaf): height 1
- Node 3: `1 + max(2, 1) = 3`
- Node 1 (root): `1 + max(2, 3) = 4`

**How it surfaces:** If a student tries to answer for node 2 (or node 1) before its children's heights are on the board, stop and ask: *"Which subtree don't we know yet?"* Refuse to write anything until they name it and go compute that one first.

**Debrief line:**
> *"Every recursive call sits there, stuck, waiting for its children to answer before it can answer itself. That's not a queue this time — that's the call stack, and it always resolves bottom-up, leaves first, root last."*

**Cut rule:** If running short, state the left half (nodes 2 and 4) yourself and only run the relay on the right half (5, 7, 6, then 3), then combine at node 1 together as a class.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (31–43 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 29-38, 41-51: pseudocode, complexity, C++/Python code, the nodes-vs-edges convention note, and the "three methods" recap -->
Covers: Pseudocode (`height(root){ if(root==null) return 0; leftHeight=height(root->left); rightHeight=height(root->right); return 1+max(leftHeight,rightHeight) }`) → Time Complexity O(N), each node visited once → Space Complexity O(H), the call stack's depth, worst case O(N) for a skewed tree → C++ and Python code → an explicit alternate convention: some textbooks/judges define height as the number of *edges* on the longest path instead of the number of nodes, which only changes the base case, from `return 0` to `return -1` → a "three methods" recap: counting levels, counting nodes on the longest path, and the recursive formula — all three producing the same number.

**Beats to emphasise**

- **The base case is the only thing that changes between the two height conventions.** Node convention: `if root == null, return 0`. Edge convention: `if root == null, return -1`. Everything else in the function is identical. This is stated directly in the deck (Slides 34-35) and is the single most practically useful thing in this session — students will meet both conventions on different judges and in different textbooks.
- **Space complexity is about the call stack, not a data structure you built.** Contrast directly with yesterday's queue: nothing here is explicitly created by the programmer — the recursion itself consumes stack space proportional to how deep it goes, which is the height, `H`.
- The **"three methods" slides** (level counting, longest-path counting, and the recursive formula) are not three different algorithms — they're three ways of arriving at the same number. Say this outright; the deck itself demonstrates the formula method on a subtree that is literally node 3's own subtree from the main dry run (left height 2, right height 1, height `1+max(2,1)=3`), reusing the same worked example rather than introducing a new tree.

**Checkpoint (at 43 min)** — show hands:
> *"Using the edge convention instead of the node convention, what would this same tree's height come out to?"*
> **Answer:** 3, not 4 — the edge convention is always exactly one less than the node convention, because every leaf now returns 0 instead of 1.

---

## ⚡ Activity 2 — Spot the Bug: Nodes vs. Edges (43–47 min)

**Format:** Spot the Bug · **Exposes:** the assumption that "height" is a single fixed definition, when in fact the only thing distinguishing the two accepted conventions is one base-case value.

**Setup line (say this):**
> *"Same function, one number changed. `return 0` becomes `return -1` in the base case. Before I tell you why anyone would do that — guess: for our tree from the dry run, height 4 by nodes, what does this new version return?"*

Show both, side by side:
```
height(root):                         height(root):
    if root == null: return 0             if root == null: return -1
    ...                                    ...
```

**What students do:** Predict the new number (3), then justify it using just a single leaf node: in the node convention, `height(leaf) = 1 + max(0, 0) = 1`; in the edge convention, `height(leaf) = 1 + max(-1, -1) = 0`.

**How it surfaces:** If students say "still 4," make them compute a single leaf's height under the edge-convention base case live — show that the leaf's own height changes from 1 to 0 first, and that the off-by-one then rides all the way up to the root untouched.

**Debrief line:**
> *"Every textbook and every judge picks one of these two conventions. The formula never changes — only the base case does. Read the problem statement's worked example before you trust your gut on which one you're being asked for."*

**Cut rule:** If short on time, skip the full leaf-to-root propagation and just state the rule directly — *"edge-convention height is always node-convention height minus one"* — then move on.

---

## ⚡ Activity 3 — Predict-the-Output: Apply the Formula Cold (47–49 min)

**Format:** Predict-the-Output · **Exposes:** whether students can apply the recursive height formula independently, without the instructor guiding them node by node the way Activity 1 did.

**Setup line (say this):**
> *"Forget the whole tree. Here's just one subtree, isolated: its left child's height is 2, its right child's height is 1. Five seconds, on your own — what's this node's height?"*

**What students do:** Write or say the number silently, then reveal together. Correct answer: 3, since `1 + max(2, 1) = 3`. <!-- placement: inferred activity, but the exact numbers (left height 2, right height 1) are not invented — they are node 3's own subtree from the deck's main dry run, reused here deliberately as an unguided check -->

**How it surfaces:** If someone answers 2 (dropped the `+1`) or 4 (added instead of taking the max), make them say the formula out loud, word by word, before recomputing.

**Debrief line:**
> *"That's the entire algorithm, done. Every node in a tree of any size answers this exact same one-line question about its two children — nothing more."*

**Cut rule:** If short on time, cut this activity entirely and ask it instead as the Slide Block B checkpoint question — it is short enough to serve as either.

---

## Exit Ticket (49–50 min)

> On paper or in chat: *"A single-node tree just has a root, no children. What's its height using the node convention, and what's its height using the edge convention?"*
> **Answer:** Node convention: 1 (the deck's own stated base case for a single-node tree). Edge convention: 0 (no edges exist on a path that never leaves the root). <!-- placement: inferred exit-ticket scenario; both numbers follow directly from the base cases stated in Slides 8 and 34-35, not from an invented example -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Height = number of edges, always | Some online judges and resources default to the edge convention, and students may have seen it before this course | Slides 34-35's explicit dual-convention note, plus Activity 2's side-by-side base-case comparison |
| Recursion computes the root's height first, since the root is "first" | Reading order (top of the tree, top of the code) suggests top-down evaluation | Activity 1's bottom-up relay — refusing to accept a parent's height until both children's heights are already known |
| Height of a single-node tree is 0 | Confusing "height" with "number of edges" or with zero-indexed level counting | Explicitly stating the deck's own base-case note: empty tree → 0, single node → 1 |
| `height = leftHeight + rightHeight + 1` (sum instead of max) | Pattern-matching against other formulas (e.g., counting total nodes) rather than height specifically | Walking the pseudocode line by line: it is `max`, not `+`, between the two subtree heights |
| Space complexity is always O(log N) | The balanced-tree case is the one usually taught first as the "typical" case in complexity discussions | Explicit statement in Slide Block B: a skewed tree pushes the space complexity to O(N), same as its height |

---

## Instructor Notes

- **Pacing risk:** Slides 52-114 retrace the *exact same* dry-run tree a third time, now annotated with `h(x)` notation for each recursive call. This is reinforcement of the Activity 1 relay, not new content — narrate it briskly ("we already did this, here it is written as h(x) instead") rather than re-deriving every value from scratch.
- **This deck is internally consistent** — unlike some other decks in this unit, the worked example tree (root 1; left 2 with only a right child 4; right 3 with children 5 and 6, where 5 has only a left child 7) is used identically across the intro framing, the guided dry run, the "three methods" recap, and the granular per-call retrace. No conflicting tree or output was found here.
- **The "Example" subtree in the Method 3 slides (Slides 50-51, values giving height 3) is not a new example** — it is node 3's own subtree, lifted directly out of the main dry-run tree. Presenting it as a fresh, independent example (as the deck's slide order might suggest) risks implying there's a second worked tree in this session; there isn't. <!-- placement: inferred observation about deck structure, not a content error, but worth naming so the instructor doesn't accidentally introduce a tree that doesn't exist -->
- **Two slides near the very end (122-123) are duplicate recap images** of the same dry-run tree and values already covered — no new information to narrate; treat them as a visual summary to flash past.
- The Classroom Quiz line above is a placeholder only — there is no MCQ bank in the source material for this course. Pull 5-6 questions from the platform's question bank live; do not build your own.
