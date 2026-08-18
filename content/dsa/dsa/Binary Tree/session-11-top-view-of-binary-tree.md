# Session 11 — Top View of Binary Tree

**Duration** 45 min · **Topic** Binary Tree — Top View · **Prerequisite** Maximum Path Sum of Binary Tree (Session 10)
**Session type** Concept lecture

<!-- placement: inferred — 45 min default holds here. Unlike Session 10, this deck teaches one clear algorithm (BFS + a column-indexed map) with one dry-run tree walked twice — once as the main dry run (slides 11–20), once again in a slower, more granular "Logical Approach" pass (slides 36–76) that is mechanically the same trace. The second pass is reinforcement, not new content, so it compresses well into a single activity rather than a third slide block. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Top View of Binary Tree | https://docs.google.com/presentation/d/1y6Q4LPdz6ZI1Fw7v6maXAZeAOyMcBhW6teDUZDaIjbA/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define the top view of a binary tree as the set of nodes visible when the tree is viewed from directly above — one node per horizontal distance from the root. *(REMEMBERING)*
2. Explain why a level-order (BFS) traversal combined with a map keyed by horizontal distance correctly captures the topmost node at each column. *(UNDERSTANDING)*
3. Apply the `topView` algorithm — tracking horizontal distance (`col - 1` left, `col + 1` right) via a queue and a map — to compute the top view of a given binary tree by hand. *(APPLYING)*
4. Analyze why two nodes at *different* levels can share the same horizontal distance, and why only the first one reached by the traversal (not the last) belongs in the output. *(ANALYZING)*
5. State and justify the O(N log N) time / O(N) space complexity of the `topView` approach. *(UNDERSTANDING)*

<!-- placement: inferred — phrased from the deck's own recap slides (9–10, 22–23, 26–27, 31, 35) -->

---

## Warm-Up Poll — Retrieval Practice on Session 10 (Maximum Path Sum of Binary Tree) (0–7 min)

Say: *"Eight quick ones on yesterday's Maximum Path Sum before we switch gears to a new kind of problem today."*

**Q1.** In Maximum Path Sum, can the path start and end at the SAME node?
`A` No, a path needs at least 2 nodes · `B` Yes — a single node is itself a valid path · `C` Only if that node is the root · `D` Only if that node is a leaf
→ *Read:* B.

**Q2.** What happens to a negative subtree contribution (`L` or `R`) in the `maxDownPath` recursion?
`A` It's added in anyway, negative or not · `B` It's clamped to 0 before being used · `C` It throws an error · `D` It's multiplied by -1
→ *Read:* B. This was yesterday's single most load-bearing line. Today's algorithm doesn't touch it again, but if hands are shaky here, it's worth a 20-second reminder before moving on — it's the kind of thing that resurfaces in mixed practice sets.

**Q3.** What initial value did `ans` get assigned in `maxPathSum`, and why?
`A` 0, because sums start at zero · `B` -1, same as the diameter session · `C` A very large negative number (`-1e9` / `-infinity`), because node values can themselves be negative · `D` The root's own value
→ *Read:* C.

**Q4.** For Example 1's tree (root 15, children -20 and 25, with 5 under -20 and 10 under 25), what was the maximum path sum?
`A` 35 · `B` 15 · `C` 50 · `D` 30
→ *Read:* C — via `15 → 25 → 10`.

**Q5. (MSQ)** Select ALL true statements about `maxDownPath`'s RETURN value versus its `ans` UPDATE at a given node.
`A` The return value uses only ONE branch: `data + max(L, R)` · `B` The `ans` update can use BOTH branches: `data + L + R` · `C` They are always equal at every node · `D` The return value is what a parent node receives to extend a path further upward
→ *Read:* A, B, D are correct. C is the trap.

**Q6.** What term did the deck use for the node where a maximum path "bends" from going up one branch to descending another?
`A` Pivot node · `B` Deflection point · `C` Root cause · `D` Turning vertex
→ *Read:* B.

**Q7.** Time and space complexity of the optimal max-path-sum approach?
`A` O(N) time, O(H) space · `B` O(N²) time, O(N) space · `C` O(N log N) time, O(N) space · `D` O(H) time, O(N) space
→ *Read:* A.

**Q8.** In the deflection-point walkthrough (tree rooted at 1, with subtree 2/4/3 on the left and subtree 10/9/8/5/6 on the right), which node turned out to be the deflection point for the OVERALL maximum path sum of 38?
`A` Node 1 (the root) · `B` Node 2 · `C` Node 10 · `D` Node 9
→ *Read:* C — the root's own deflection sum was only 34; node 10 beat it.

**Running it** — poll tool, ~40–50 s per question. Total 7 min.

---

## Hook (7–9 min)

Say: *"For the last two sessions, every algorithm went DOWN the tree — recurse into a subtree, wait for an answer, combine it on the way back up. Today's algorithm doesn't recurse at all."*

Draw a small tree on the board (or reuse Example 1's tree: root 50, children 30 and 70, with 20 and 40 under 30, and 60 and 90 under 70). Ask: *"If you physically stood over this tree and looked straight down, which nodes would you actually see?"*

Take a couple of guesses, then: *"Notice you didn't need to go 'down' any branch to answer that — you needed to know how far LEFT or RIGHT each node sits. That's the whole shift today: horizontal distance, not depth."*

---

## Slide Block A (9–16 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary, slides 1–10 -->
Covers: title/agenda → Problem Statement ("find the top view by listing nodes left to right; top view = nodes visible when the tree is viewed from the top") → Example 1 (root 50, output `20 30 50 70 90`) → Example 2 (root 1, output `7 2 1 3 8`) → Approach (level-order/BFS traversal tracking horizontal distance from the root; a map stores the first node value seen at each horizontal distance; left child gets `col - 1`, right child gets `col + 1`; after traversal, read the map out in increasing order of horizontal distance).

**Beats to emphasise**

- **"Viewed from the top" is a literal instruction, not a metaphor.** Trace Example 1's tree on the board and physically point out that nodes 40 and 60 are directly *underneath* nodes 50 and 30 respectively — they exist, but they're hidden from above.
- **Horizontal distance, not level, is the key.** Two nodes at completely different depths can share the same horizontal distance (Example 1: node 60 shares horizontal distance 0 with the root, even though it's two levels down).
- State the three-step approach exactly as the deck gives it: BFS with `(node, column)` pairs → "first node per column wins" → read the map out sorted by column.

**Checkpoint (at 16 min)** — cold-call two students:
> *"In one sentence — why does the top view use BFS instead of the recursive DFS style from the last two sessions?"*
> **Answer:** BFS processes the tree level by level, top to bottom, so the *first* node the traversal reaches at any given horizontal distance is guaranteed to be the topmost one. DFS could reach a deeper node's column before a shallower one's, which would break the "first-seen-wins" rule the map depends on.

---

## ⚡ Activity 1 — Predict-the-Output (16–21 min)

**Format:** Predict-the-Output · **Exposes:** the assumption that "top view" means only the two outer boundary branches of the tree, rather than one visible node per horizontal distance.

**Setup line (say this):**
> *"Tree on the board: 50 at the root, 30 and 70 as its children, then 20 and 40 under 30, and 60 and 90 under 70. If you looked straight down at this tree from above, which nodes would you actually see, left to right? Write your list."*

**What students do:** 30–45 seconds writing individually, then share a couple of different guesses.

**How it surfaces:** A common wrong guess includes node 60 (e.g., "20, 30, 60, 70, 90"). Ask that student to state node 60's horizontal distance out loud (0 — same as the root) and then ask who else is at horizontal distance 0 and was reached first (node 50).

**Debrief line:**
> *"The answer is 20, 30, 50, 70, 90 — five nodes, not seven. Nodes 40 and 60 both sit directly underneath node 50 in the exact same vertical column, so they're hidden. Top view isn't 'the two edges of the tree' — it's one winner per column, and the winner is whoever the level-order traversal reaches FIRST."*

**Cut rule:** If running short, skip the individual predictions — ask directly "is node 60 in the output, yes or no?" and go straight to the debrief.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (26–34 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary, slides 11–25 -->
Covers: the main Dry Run on Example 2's tree (root 1; left child 2, whose right child is 4, whose children are 5 and 6; right child 3, a leaf; 5's left child is 7; 6's right child is 8) — a queue-and-map trace processing nodes in BFS order `1, 2, 3, 4, 5, 6, 7, 8` → Pseudocode → Time Complexity (O(N log N): N node visits, each with an O(log N) map insertion) → Space Complexity (O(N): map and queue can each hold up to N entries) → C++/Python code.

**Beats to emphasise**

- Run the dry run exactly in the deck's own order and narrate the map check at every single node: `1`(col 0, store) → `2`(col -1, store) → `3`(col 1, store) → `4`(col 0, **already there, skip**) → `5`(col -1, **already there, skip**) → `6`(col 1, **already there, skip**) → `7`(col -2, store) → `8`(col 2, store). Final map, read in column order `-2, -1, 0, 1, 2`: **7, 2, 1, 3, 8**.
- **Why O(N log N), not O(N):** the traversal itself only touches each node once (that part is O(N)), but every map insertion costs O(log N) because the map keeps itself sorted by column — that's where the extra factor comes from. This is worth stating explicitly since both prior sessions (09, 10) had a plain O(N).
- The output is read out in **column order**, not the order nodes were visited — say this out loud right after the dry run, it's easy to blur the two.

**Checkpoint (at 34 min)** — show hands:
> *"Node 4 sits at horizontal distance 0, same as the root. Does node 4 make it into the final output — yes or no, and why?"*
> **Answer:** No. Column 0 was already claimed by node 1, which the BFS reached first. Node 4 is processed later and its column check finds an existing entry, so it's skipped.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay (34–39 min)

**Format:** Dry-Run Relay · **Exposes:** that the deciding factor at each step is "is this column already in the map," not the node's value or its depth.

**Setup line (say this):**
> *"Same tree as the deck: root 1, columns ranging from -2 to 2. I've drawn an empty queue and an empty map on the board. Each of you gets one dequeue step — tell me the node, its column, whether that column is already in the map, and what gets enqueued next."*

**What students do:** Eight students, one per node, in BFS order (1, 2, 3, 4, 5, 6, 7, 8), each stating: node, column, map hit-or-miss, and which children get enqueued with their updated column.

**How it surfaces:** If a student at node 4, 5, or 6 says their node should be stored, stop and ask: *"Who else already owns this column, and who got there first?"* — point back at the map on the board.

**Debrief line:**
> *"Notice the queue never once cared what a node's VALUE was — only its column, and whether that column already had an owner. That's exactly why this has to be BFS: level order guarantees the topmost node at any column is always dequeued before anything underneath it can steal its spot."*

**Cut rule:** If running short, run only nodes 1, 2, 4, and 7 (the root plus one full left branch) — that's enough to show one "store" and one "skip," and you can state the rest of the trace verbally.

---

## ⚡ Activity 3 — Spot the Bug (39–43 min)

**Format:** Spot the Bug · **Exposes:** that "first node per column wins" — not "last node per column wins" — is the rule, and that BFS order is exactly what makes "first" mean "topmost."

**Setup line (say this):**
> *"I've changed one line: instead of 'store a value in the map only if this column has never been seen,' the code now always overwrites — 'store it every time, no check.' Same tree as Activity 2. Trace ALL five columns to the end: what does the map hold once the queue is empty, and how does that compare to the correct 7, 2, 1, 3, 8?"*

**What students do:** Retrace the full BFS order (1, 2, 3, 4, 5, 6, 7, 8) in pairs, this time noting, for every column, which node was visited *last* — since with the bug, last-write-wins.

**How it surfaces (the answer):** Column -2 (only node 7) and column 2 (only node 8) are unaffected. But column -1 sees node 2 first, then node 5 — the buggy map ends up holding 5, not 2. Column 0 sees node 1 first, then node 4 — the buggy map ends up holding 4, not 1. Column 1 sees node 3 first, then node 6 — the buggy map ends up holding 6, not 3. **The buggy output is 7, 5, 4, 6, 8, not 7, 2, 1, 3, 8.**

**Debrief line:**
> *"Flip that one condition — from 'store only if new' to 'always store' — and every column with more than one node comes out wrong, because now the LAST node visited per column wins instead of the first. You've silently built the tree's bottom view instead of its top view, using the exact same traversal."* <!-- placement: inferred — the deck never names "bottom view," but it's the direct mechanical consequence of this bug, included here to make the fix concrete and memorable -->

**Cut rule:** If running late, trace only column 0 (node 1, then node 4 overwriting it) — one clean example is enough — and state the rest of the broken output (`7, 5, 4, 6, 8`) verbally instead of tracing all five columns.

---

## Exit Ticket (43–45 min)

> On paper or in chat: *"Draw any 4-node binary tree where at least two nodes share the same horizontal distance. Circle the one that would actually appear in the top view, and explain why in one sentence."*
> **Answer shape:** The circled node must be the one the BFS/level-order traversal reaches first — i.e., the shallower one, or if tied in depth, the one processed earlier in that level's left-to-right queue order.

Scan responses on the way out. If several students circle the *deeper* node "because it's more central" or similar reasoning, that's the signal the "first-seen-wins via BFS" rule hasn't fully landed — worth a 30-second recap before the next session.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Top view = only the two outermost/boundary branches of the tree | "View" sounds like it should mean silhouette or boundary | Activity 1 — node 60 sits inside the boundary but is still hidden, because it shares a column with an already-claimed node |
| The output should be listed in the order nodes were visited (BFS order) | That's how every other traversal in this course has produced its output so far | Slide Block B's explicit "read the map out sorted by column" step, separated from the visiting order |
| The LAST node reached at a column should be the one recorded | "Most recently seen" feels like the natural default for overwriting a stored value | Activity 3's Spot-the-Bug, which shows exactly what breaks (and why it becomes a bottom view) when this is the rule |
| Horizontal distance is the same thing as a node's value or index | The diagrams label columns with plain small integers (-2, -1, 0, 1, 2), which can look interchangeable with node data at a glance | Explicitly separating "column" (a distance from the root) from "data" (the node's own value) every time the map is drawn |
| This has to be solved recursively (DFS), like the last two sessions | Sessions 09 and 10 were both DFS-and-a-running-value patterns | The Hook and the Slide Block A checkpoint, both making the BFS requirement explicit and explaining why DFS would break the "first-seen-wins" guarantee |

---

## Instructor Notes

- **Pacing risk:** Activity 3 (Spot the Bug) is the payoff for the whole session — it's the moment students see *why* "first wins" matters, not just that it's the rule. If time is tight, compress Activity 2's relay to the 4-node cut version rather than shortening Activity 3.
- **The deck itself contains a mid-session "THANK YOU" / "ALL THE BEST" pair of slides (right after the first complexity recap, before the deck repeats the whole dry run in more granular form).** This lines up loosely with where the Classroom Quiz sits in this plan, though the quiz here is placed by session-time (roughly the halfway mark), not by that slide boundary. <!-- placement: inferred — noting the coincidence for instructor awareness, not treating it as authoritative -->
- **Slides 36–76 of the deck are a much slower, frame-by-frame repeat of the same dry run already covered in Slide Block B.** Don't deliver them narration-slide-by-slide in class — Activity 2's relay format covers the same ground faster and with more student engagement. Keep the slides available for students to review afterward.
- **Have the Example 2 tree (root 1, the one used in Slide Block B, Activity 2, and Activity 3) drawn on the board before class starts** — it's reused three times, and redrawing it each time burns minutes this 45-minute session doesn't have.
