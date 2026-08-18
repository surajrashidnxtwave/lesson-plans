# Session 6 — Level Order Traversal

**Duration** 50 min <!-- placement: inferred — heavier than the 45-min default; 101 slides, and the deck re-walks the same worked example three separate times (a guided dry run, a FIFO/cinema framing, then a granular per-pseudocode-line trace) — a strong signal this is the first BFS/queue-based traversal after three recursive DFS sessions and needs the extra runway --> · **Topic** Binary Tree — Traversals · **Prerequisite** Post-Order Traversal (Session 05)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Level Order Traversal | https://docs.google.com/presentation/d/1gmTJbXzHlwaLUWTz0aqXqAMAFF37GbM8VSAT7EUXCSg/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define level order traversal as visiting nodes level by level, top to bottom, left to right within each level, and name it as Breadth-First Search (BFS). *(REMEMBERING)* <!-- placement: inferred phrasing, grounded in Slide 5 -->
2. Explain why a queue (First In First Out) is the data structure that produces this visiting order, in contrast to the call stack used by the recursive traversals taught in prior sessions. *(UNDERSTANDING)* <!-- placement: inferred; the deck states the FIFO/queue rationale directly (Slides 9-10, 45-51) but the explicit DFS-contrast framing is mine -->
3. Trace the level-order algorithm on a given binary tree, correctly maintaining both the queue and the result list at each step. *(APPLYING)* <!-- placement: inferred phrasing, grounded in the dry run, Slides 11-20 -->
4. Implement level order traversal using the enqueue-root / dequeue-record-enqueue-children pattern, in pseudocode, C++, or Python. *(APPLYING)* <!-- placement: inferred phrasing, grounded in Slides 21-22, 26-28 -->
5. State and justify the time and space complexity of level order traversal — O(N) time, O(N) worst-case space (at most ~N/2 nodes at the widest level of a full tree). *(ANALYZING)* <!-- placement: inferred phrasing, grounded in Slides 23-25, 95-99 -->

---

## Warm-Up Poll — Retrieval Practice on Session 05: Post-Order Traversal (0–6 min)

Say: *"Six questions on yesterday's post-order traversal. No names, no grades — I just need to know what's still there before we build on top of it."*

**Q1.** What is the visiting order in a post-order traversal?
`A` Root, Left, Right · `B` Left, Root, Right · `C` Left, Right, Root · `D` Right, Left, Root
→ *Read:* C. If this misses, stop — nothing else in the poll will make sense.

**Q2.** In the session 05 dry run (root 1; 1's children 2, 3; 2's children 4, 5; 3's children 6, 7; 4's left child 8; 6's right child 9), which node is visited *first*?
`A` Node 1 · `B` Node 2 · `C` Node 8 · `D` Node 9
→ *Read:* C. Post-order always bottoms out at the deepest-left leaf before it visits anything.

**Q3.** *(MSQ — pick all that apply)* In the pseudocode `postorder(root){ if(root==null) return; postorder(root->left); postorder(root->right); print(root->data) }`, which lines execute *before* a node's own value is printed?
`A` The null check · `B` The recursive call on the left child · `C` The recursive call on the right child · `D` Nothing — the value prints first
→ **Answer:** A, B, and C — the whole point of post-order is that both subtree calls finish before the print line runs.

**Q4.** What is the time complexity of post-order traversal?
`A` O(log n) · `B` O(n) · `C` O(n²) · `D` O(h)
→ *Read:* B — every node is visited exactly once.

**Q5.** What does the O(h) space complexity of post-order traversal actually measure, and what causes it?
`A` The size of an explicit queue · `B` The depth of the recursive call stack · `C` The number of leaf nodes · `D` The number of print statements
→ *Read:* B. Hold onto this answer — today's traversal will *not* use the call stack the same way.

**Q6.** For the session 05 dry-run tree (root 1; children 2, 3; 2's children 4, 5; 3's children 6, 7; 4's left child 8; 6's right child 9), what is the correct post-order output?
`A` 1 2 4 8 5 3 6 9 7 · `B` 8 4 5 2 9 6 7 3 1 · `C` 8 9 4 5 6 7 2 3 1 · `D` 1 3 7 9 6 2 5 8 4
→ **Answer:** B — direct recall of the session 05 worked dry run.

**Q7.** True or False: post-order traversal's recursion stops when it reaches a null node.
`A` True · `B` False
→ **Answer:** A True. *Read:* Keep this in your head — today's stopping condition will be different ("queue is empty"), not this.

**Running it** — poll tool, ~40 s/question, project the distribution after each. Total 6 min including reads.

---

## Hook (6–9 min)

Draw (or project) the same tree from session 05's dry run: root 1; children 2 and 3; 2's children 4 and 5; 3's children 6 and 7.

Say: *"Post-order gave you 8 4 5 2 9 6 7 3 1 — root last, and you had to go all the way down to a leaf before printing anything. Today I want the same tree to give me 1 2 3 4 5 6 7 — top row first, then the next row, left to right. Same tree. Completely different order. What has to change?"*

Let a few guesses land. Then: *"Recursion alone won't do this — recursion naturally drills down one branch at a time. To go row by row, you need to remember every node you haven't visited yet, in the exact order you found them. That's not a stack. That's a queue."* <!-- placement: inferred hook, built directly from the contrast between session 05's tree/output and session 06's own Slide 7 example output -->

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 1-10: Welcome/title/agenda, BFS definition, problem statement, worked example output, approach (queue-based BFS) -->
Covers: Level order traversal definition (visit level by level, top to bottom, left to right) → Problem Statement → Example (tree 1 / 2,3 / 4,5,6,7 → output `1 2 3 4 5 6 7`) → Approach: use a queue to perform Breadth-First Search; when a node is processed, visit its value, then enqueue its left and right children.

**Beats to emphasise**

- **Name it BFS, out loud, more than once.** The deck itself labels this "Breadth First Search (Level Order)" on Slide 5 — say the two names together every time so students build the association: level order = BFS.
- **The queue is not optional bookkeeping — it *is* the algorithm.** The deck's own line: *"A queue ensures that nodes are processed in the exact order they appear at each level."* Land on this before moving to the dry run.
- **Per-node action is always the same three steps:** visit/collect its value, then enqueue its left child (if it exists), then enqueue its right child (if it exists). This three-step pattern repeats for every single node and is the thing students must be able to say without looking at the slide.

**Checkpoint (at 19 min)** — cold-call two students:
> *"In one sentence each: what does level order traversal visit, in what order, and what data structure makes that order happen?"*
> **Answer:** It visits every node level by level, top to bottom and left to right within a level, and a queue (FIFO) is what enforces that order.

---

## ⚡ Activity 1 — Real-World Callout: The Cinema Queue (19–24 min)

**Format:** Real-World Callout · **Exposes:** whether students actually understand *why* FIFO produces left-to-right, level-by-level order, or whether "queue" is just a vocabulary word they're repeating back.

**Setup line (say this):**
> *"You're in line at a cinema ticket counter. First person in line gets served first, gets their ticket, and leaves. Nobody cuts. Is that a stack or a queue — and what would break about level order traversal if it were the other one?"* <!-- placement: inferred setup line; the cinema/FIFO analogy itself is verbatim from the deck, Slides 45-47 -->

**What students do:** Call out "queue" and "FIFO." Push once more: *"If it were a stack instead — last person in line served first — what would our tree's output look like?"* Take 2-3 verbal guesses.

**How it surfaces:** If someone answers "stack" or hesitates, walk them back to the cinema line itself: point out that the *last* person to arrive getting served first would mean nodes discovered late (deeper, or further right) jump ahead of nodes discovered earlier — which destroys the level-by-level guarantee entirely.

**Debrief line:**
> *"Queue in, queue out, no cutting. That single rule — first in, first out — is the entire reason level order traversal visits top to bottom, left to right. Change the rule, and you change the traversal."*

**Cut rule:** If running late, skip the "what if it were a stack" push and just take the cinema-line answer (queue/FIFO) before moving on. Do not cut the debrief line.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–39 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 11-28: full dry run, pseudocode, complexity, C++/Python code -->
Covers: Dry run of the queue-based BFS on the tree (root 1; children 2, 3; 2's children 4, 5; 3's children 6, 7), tracking Queue and Result at every step → Pseudocode (`levelOrder(root)`: push root; while queue not empty, for each node at this level — pop the front, push its left/right children if they exist, append its data to `ans`) → Time Complexity O(N) → Space Complexity O(N), worst case ~N/2 nodes at the widest level of a full tree → C++ and Python code.

**Beats to emphasise**

- **Walk the dry run at the deck's own pace, node by node.** The Queue column and the Result column are two separate lists on screen at once — point at each explicitly on every step so students don't conflate "what's still in the queue" with "what's already been recorded."
- **The removal always happens before the enqueue.** Per the deck's own pseudocode order: pop the front → check/push its left child → check/push its right child → append its data to the result. Say the order out loud, in that order, every single time through the dry run.
- On **Space Complexity**, contrast explicitly with yesterday's post-order O(h): today's worst case is O(N) — a full tree's last level alone can hold roughly N/2 nodes in the queue simultaneously. This is a deliberate, stated contrast in the deck (Slide 25).

**Checkpoint (at 39 min)** — show hands:
> *"For our tree (root 1, children 2/3, grandchildren 4/5/6/7), when node 2 is popped from the front of the queue, what exactly happens, in order?"*
> **Answer:** Node 2 is removed from the front, its value 2 is appended to the result, then its left child 4 and right child 5 are pushed onto the back of the queue.

---

## ⚡ Activity 2 — Live Dry-Run Relay (39–45 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can independently drive the queue-and-result mechanics end to end, without the instructor doing the bookkeeping for them.

**Setup line (say this):**
> *"Same tree as the slides: root 1, children 2 and 3, and 2/3's children are 4, 5, 6, 7. Queue starts with just [1]. I will not touch the queue or the result list unless one of you tells me exactly what to remove, what to add to the result, and what to push — in that order."*

**What students do:** Called on in turn, one student per step, each must state all three actions for that step:
- Pop 1 → result `[1]` → push 2, 3 (queue: `[2,3]`)
- Pop 2 → result `[1,2]` → push 4, 5 (queue: `[3,4,5]`)
- Pop 3 → result `[1,2,3]` → push 6, 7 (queue: `[4,5,6,7]`)
- Pop 4 → result `[...,4]` → no children (queue: `[5,6,7]`)
- Pop 5 → result `[...,5]` → no children (queue: `[6,7]`)
- Pop 6 → result `[...,6]` → no children (queue: `[7]`)
- Pop 7 → result `[...,7]` → no children (queue: `[]` → done)

**How it surfaces:** If a student jumps straight to "the children are 4 and 5" without first stating "pop 2, add 2 to result," stop them and make them restate all three actions in order — the point is the sequence, not just the final children.

**Debrief line:**
> *"Notice the result list came out 1 2 3 4 5 6 7 — in numeric order — purely because the queue processed things in the order they were discovered. Nobody sorted anything."*

**Cut rule:** If running short, run the relay live only through node 3 (levels 0-1), then state the remaining steps (4 through 7) yourself and just confirm the final result list together.

---

## ⚡ Activity 3 — Spot the Bug: Does Push Order Matter? (45–48 min)

**Format:** Spot the Bug · **Exposes:** the assumption that "left to right" is automatic, rather than a direct consequence of the order children are pushed onto the queue.

**Setup line (say this):**
> *"Same pseudocode as the slides, except I've swapped one pair of lines. Tell me only what changes in the *output* — not whether the code looks wrong."*

Show:
```
if (temp->right != null) { q.push(temp->right) }
if (temp->left  != null) { q.push(temp->left)  }
ans.push(temp->data)
```
(right pushed before left — swapped from the deck's own left-then-right order.)

**What students do:** Trace just the first level by hand: root 1 is popped, and now pushes 3 before 2, so the queue becomes `[3, 2]` instead of `[2, 3]`. Predict the full output from there.

**How it surfaces:** If students say the output is unaffected, make them finish the trace: with queue `[3,2]`, node 3 is processed next (not 2), so 3's children (6, 7) get enqueued before 2's children (4, 5) — final output becomes `1 3 2 6 7 4 5`, not `1 2 3 4 5 6 7`.

**Debrief line:**
> *"Left-to-right isn't a property of queues in general — it's a property of *this* code, because we push left before right. Swap the push order, and you silently get a mirror-image traversal that still runs without errors."*

**Cut rule:** If short on time, skip the full re-trace — just ask verbally, *"If I pushed right before left, would level 2 still read 4, 5, 6, 7?"* and take the one-word answer ("no").

---

## Exit Ticket (48–50 min)

> On paper or in chat: *"Here's a tree: root 10, left child 20, right child 30; 20's children are 40 and 50 (30 has no children). Write the queue's contents right after node 10 is popped, and write the final level-order output."*
> **Answer:** Queue after popping 10: `[20, 30]`. Final output: `10 20 30 40 50`. <!-- placement: inferred exit-ticket scenario, built from the same queue mechanics as the deck's worked example, using new node values so it isn't a copy of the in-class tree -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Level order is just another flavour of the recursive traversals from previous sessions | Sessions 04-05 were all recursive, DFS-style, using the call stack | Naming it explicitly as Breadth-First Search from the first slide, and contrasting the queue mechanism against the call stack in the Hook |
| Once you see a node, you immediately enqueue its children — no removal step needed | Prior recursive traversals never required an explicit "remove from structure" step | Activity 2's dry-run relay, which forces pop-then-record-then-push in that exact order, every time |
| The Queue and the Result list are the same list | Both are lists of numbers shown side by side on the same slide throughout the dry run | Pointing at the two separate columns explicitly on every dry-run step in Slide Block B |
| Left-to-right visiting order is automatic, inherent to "using a queue" | Students haven't had to think about *why* an order emerges, only that a queue is FIFO | Activity 3's Spot the Bug — swap the push order and show the output changes |
| Space complexity is O(h), like the recursive traversals just covered | Direct carryover from session 05's post-order O(h) space | Explicit contrast in Slide Block B: the queue's worst case is O(N), driven by the widest level, not the tree's height |

---

## Instructor Notes

- **Pacing risk:** the deck re-walks the *same* example tree three times — once as a guided dry run (Slides 11-20), once through the cinema/FIFO framing (Slides 45-64), and once again as a granular per-pseudocode-line trace (Slides 65-99). Treat the second and third passes as reinforcement, not new content — don't re-derive the mechanics from scratch each time or you will run out of session.
- **Two spots in the deck are worth a heads-up before you present them.** Slides 35 and 38 carry a leftover "Post-Order Traversal Example 1" title and an output of `[4, 8, 5, 2, 6, 7, 3, 1]` that does not match level order's left-to-right, top-to-bottom pattern for any tree consistent with the rest of the deck — this reads as an un-edited artifact from a copied template, not new content. This lesson plan does not build on those two slides; consider skipping past them quickly or flagging them as a content-fix candidate. <!-- placement: inferred — I could not reconcile this output with the rest of the deck's worked example -->
- **The tree labels in the cinema/FIFO section (Slides 45-64) are extracted out of visual order** (a known artifact of text-run extraction pulling diagram labels in placement order, not reading order). This lesson plan treats that section as re-using the *same* tree as the main dry run (root 1; children 2, 3; grandchildren 4, 5, 6, 7), since the section's own pseudocode trace resolves to the same final result `1 2 3 4 5 6 7` (Slide 94) — but the instructor should visually confirm against the actual slides before presenting it as identical to the main example. <!-- placement: inferred; flagging rather than asserting with confidence -->
- **This is the class's first BFS/queue-based traversal after three recursive sessions.** Expect an instinctive reach for recursion; the Hook and Activity 1 are both built to interrupt that instinct early.
- The Classroom Quiz line above is a placeholder only — there is no MCQ bank in the source material for this course. Pull 5-6 questions from the platform's question bank live; do not build your own.
