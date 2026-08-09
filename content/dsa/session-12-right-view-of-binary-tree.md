# Session 12 — Right view of Binary Tree

**Duration** 45 min · **Topic** Binary Tree · **Prerequisite** Top view of Binary Tree (Session 11)
**Session type** Concept lecture

<!-- placement: inferred — 93 slides, but slides 26–35 duplicate slides 4–25 in summary form and slides 36–89 re-walk the same single dry-run tree in finer-grained steps. Genuinely new algorithmic content is level-order-with-last-per-level, a smaller conceptual jump than Session 11's map+horizontal-distance approach, so the reference's default 45 min holds. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Right view of Binary Tree | https://docs.google.com/presentation/d/16kiWXDN5y1e_v8DyGykK7WCQWMBVFFhYVwwr7zwOdRU/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define the right view of a binary tree as the set of nodes visible when the tree is viewed from the right side. *(REMEMBERING)*
2. Explain why capturing the **last node dequeued at each level** of a level-order traversal produces the right view. *(UNDERSTANDING)* <!-- placement: inferred phrasing, from deck's own Approach/Summary slides 9, 24 -->
3. Trace the queue-based level-order algorithm on a given tree to produce its right-view list, correctly tracking level boundaries with `len = q.size()`. *(APPLYING)*
4. State and justify the time complexity O(N) and space complexity O(N) of the right-view algorithm. *(ANALYZING)*
5. Contrast Right View's "track the last node per level" strategy against Top View's "track the first node per column" strategy from the previous session. *(ANALYZING)* <!-- placement: inferred — not stated by the deck itself, but the two sessions are back-to-back and this contrast is the single highest-value teaching lever available -->

---

## Warm-Up Poll — Retrieval Practice on Top view of Binary Tree (Session 11) (0–6 min)

> Retrieval practice on the session immediately before this one. No new content — this is recall.

Say: *"Seven quick ones on yesterday's Top View problem before we look at a new one."*

**Q1.** In Top View, what determines whether a node is visible from above — its depth in the tree, or its horizontal distance from the root?
`A` Depth · `B` Horizontal distance (column) from root · `C` Its value · `D` Whether it's a leaf
→ *Read:* B. If this is shaky, the rest of the poll won't land — restate the column idea before Q2.

**Q2.** When a node at column `c` is dequeued, what column do you enqueue its left and right children with?
`A` Left = c, Right = c · `B` Left = c−1, Right = c+1 · `C` Left = c+1, Right = c−1 · `D` Both children get column c+1
→ *Read:* B — left always moves one column left, right always moves one column right.

**Q3.** In the map, do we store the **first** node encountered at a column, or the **last**?
`A` First · `B` Last · `C` Whichever has the larger value · `D` Doesn't matter
→ *Read:* First. Hold onto this answer — today's algorithm keeps the *opposite* one, and that contrast is the whole session.

**Q4.** For the tree `1 2 3 null 4 null null 5 6 7 null null 8` (root 1; left 2, right 3; 2's right child 4; 3's left 5, right 6; 4's left 7; 6's left 8), what is the top view, left to right?
`A` 1 2 3 · `B` 7 2 1 3 8 · `C` 5 2 1 3 6 · `D` 7 4 1 3 8
→ *Read:* B (`7, 2, 1, 3, 8`) — straight from the deck's own Example 2.

**Q5. (MSQ)** Which data structures does the Top View algorithm use? *(pick all that apply)*
`A` Queue · `B` Map · `C` Stack · `D` Priority queue
→ *Read:* A and B — a queue for level-order traversal, a map keyed by column.

**Q6.** What is the time complexity of Top View, and why?
`A` O(N) — each node visited once · `B` O(N log N) — each node visited once, plus O(log N) per map insertion · `C` O(N²) · `D` O(log N)
→ *Read:* B. The traversal itself is O(N); the map insert per node is what adds the log N.

**Q7.** True or False: if two nodes land on the same column, Top View keeps the one encountered **later** in the BFS.
`A` True · `B` False
→ *Read:* False — first-seen-wins, which is exactly why the map does a "not already present" check before writing.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Yesterday you stood above the tree and looked straight down — Top View. Today, walk around to the side and look straight across. Same tree, same nodes, completely different question: which nodes can you actually see?"*

Put the deck's Example 1 tree on the board: root `6`; left child `3`, right child `5`; `3`'s children `7` and `4`; `5`'s children `9` and `2`.

Ask: *"From the right side, which nodes do you see? Shout them out."*

Let a few guesses land — someone will likely say `6, 5, 9` (chasing the biggest number, or always going right). Don't correct yet: *"Hold that answer. In five minutes you'll have a rule that tells you for certain — and it isn't 'always go right.'"*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

Covers: Problem Statement → Example 1 (root `6/3/5/7/4/9/2` → right view `6, 5, 2`) → Example 2 (root `8/2/6/3/null/1/5/null/null/0` → right view `8, 6, 5, 0`) → Approach (level-order traversal, track the last/rightmost node per level).

**Beats to emphasise**

- **Right view is defined level by level, not by following a single path.** The deck's Approach slide (9) is explicit: "perform a level-order traversal... at each level, track the last (rightmost) node encountered... add this rightmost node to a list."
- Walk Example 1 slowly against the Hook's guesses: Level 0 → `6`. Level 1 → nodes `3, 5`, rightmost is `5`. Level 2 → nodes `7, 4, 9, 2` (left to right), rightmost is `2` — **not** `9`, even though `9` is the bigger number and sits under `5`. This directly disarms the "always go right" / "biggest number" guesses from the Hook.
- Example 2's tree is the one used for the full dry run coming next — flag that to the class so they recognise it.

**Checkpoint (at 17 min)** — cold-call two students:
> *"In one sentence: what makes a node part of the right view — its value, or something else?"*
> **Answer:** Being the last node processed, left to right, at its level during a level-order traversal — not its value and not simply "having no right sibling."

---

## ⚡ Activity 1 — Predict-the-Output: Which Node Survives Each Level? (17–22 min)

**Format:** Predict-the-Output · **Exposes:** whether students can identify "last node dequeued at this level" without running the algorithm — and whether the Hook's "always follow right" instinct has actually been replaced.

**Setup line (say this):**
> *"Same Example 1 tree — root 6, left 3, right 5, 3's children are 7 and 4, 5's children are 9 and 2. Cover the answer. On your own, in ten seconds, write the right-view list. Then I want a show of hands for what you got at Level 2 specifically."*

**What students do:** Write their own prediction silently, then show hands for their Level-2 answer (options will likely split between `2` and `9`).

**How it surfaces:** If a meaningful chunk pick `9`, don't just state the answer — walk the level-order queue live: after processing `3` and `5`, the queue holds `{7, 4, 9, 2}` in exactly that order (children of `3` first, then children of `5`), so the *last* one dequeued is `2`, not `9`. `9` is visually "further right" on some mental images of the tree, but it is not the last node processed in left-to-right level order.

**Debrief line:**
> *"The right view isn't 'the node furthest to the right visually' — it's 'the last node the queue hands you before the level ends.' Those two ideas coincide most of the time, which is exactly what makes the rare mismatch dangerous."*

**Cut rule:** If running late, skip the written prediction step and go straight to the show-of-hands on Level 2 — that's the one moment of the activity that carries the whole point.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (27–36 min) — DELIVER SLIDES AS-IS

Covers: Dry Run on Example 2's tree (root `8`; left `2`, right `6`; `2`'s left `3`; `6`'s left `1`, right `5`; `1`'s left `0`) → Pseudocode → Time Complexity O(N) → Space Complexity O(N) → C++/Python code.

**Beats to emphasise**

- Run the dry run exactly as the deck stages it: `queue = {8}` → dequeue `8`, enqueue `2, 6`, end of level → `ans = {8}`. Dequeue `2`, enqueue `3` (no right child) → still level 1, don't close it yet. Dequeue `6`, enqueue `1, 5`, **now** level 1 is done → `ans = {8, 6}`. Continue through level 2 (`3, 1, 5` → last is `5` → `ans = {8, 6, 5}`) and level 3 (`0` only → `ans = {8, 6, 5, 0}`).
- Point at the pseudocode line `len = q.size()` **before** the `for i from 0 -> len-1` loop (slide 19) — this snapshot is what tells the algorithm exactly how many nodes belong to the current level, before any of that level's children get pushed in and blur the boundary.
- Time Complexity O(N): every node visited once, constant work per node — no map, no log factor, unlike Top View.
- Space Complexity O(N): the deck notes the queue can hold up to N/2 nodes at the widest (lowest) level of a complete tree, and the result list holds at most N entries.

**Checkpoint (at 36 min)** — show hands:
> *"In the dry run, right after we dequeue node 2 and enqueue its child 3 — is level 1 finished yet?"*
> **Answer:** No. `len` was snapshotted as 2 (nodes `2` and `6`) before the loop started; only after `6` is also dequeued and processed does that inner loop finish and level 1 close, pushing `6` into `ans`.

---

## ⚡ Activity 2 — Dry-Run Relay: Be the Queue (36–41 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can execute the level-by-level bookkeeping themselves — get the queue size, drain exactly that many, push children for the next level, keep only the last `temp` — without the instructor narrating it.

**Setup line (say this):**
> *"Same tree as the dry run — 8 at the root, 2 and 6 below it, 3 under 2, 1 and 5 under 6, 0 under 1. Seven volunteers, one per node — 8, 2, 6, 3, 1, 5, 0. Each of you: when it's your turn to be dequeued, state the queue's current size, say what you enqueue, and say what goes into `temp`. Last one to speak in your level says what gets pushed to `ans`."*

Run the deck's own 5-step loop (slide 39: get size of queue → k; take out the front value; add its children to queue; push the data to temp; push temp to ans; repeat k times) live, level by level, on the tree above.

**What students do:** Each "queue" student calls out, in order: `size = ___`, `front = ___`, `enqueue = ___`, `temp = ___`. At the end of each level, the class chorus-answers what got pushed to `ans`.

**How it surfaces:** If a student pushes `temp` into `ans` after every single pop (instead of only once, at the end of the level), stop and point at the pseudocode: `temp` gets overwritten every iteration inside the level's loop, and it's only pushed to `ans` once, after the loop for that level finishes.

**Debrief line:**
> *"`temp` is disposable — it gets overwritten by every node in the level. `ans` only wants the very last one standing. That's the entire algorithm in one sentence."*

**Cut rule:** If short on time, relay only levels 1 and 2 (where the size actually changes from 2 to 3) and state level 3's single-node result rather than relaying it.

---

## ⚡ Activity 3 — Spot the Bug: "Just Follow Right" (41–44 min)

**Format:** Spot the Bug · **Exposes:** the classic right-view shortcut — "start at root, keep going `root->right->right->right...` until null" — which looks correct on simple trees but silently drops nodes. <!-- placement: inferred — this specific bug pattern is not named by the deck, but it is directly falsified by the deck's own Example 2 tree, which is why that tree is used here -->

**Setup line (say this):**
> *"Here's a one-line 'shortcut' someone might hand in: forget the queue, just start at the root and keep moving right — root, root's right child, that child's right child, and so on — printing each stop. Try it on Example 2's tree: 8, left 2 right 6, 2's left 3, 6's left 1 right 5, 1's left 0. Where does it stop, and is that the full right view?"*

**What students do:** Trace the shortcut out loud: `8 → 6 → 5 → (5 has no right child) → stop`. Result: `8, 6, 5`.

**How it surfaces:** Compare against the correct dry-run answer, `8, 6, 5, 0`. The shortcut never visits `0` at all — `0` hangs off `1`'s **left** child, and `1` is only reachable via `6`'s **left** child, a branch the "always go right" rule never enters.

**Debrief line:**
> *"The rightmost node at a level doesn't have to live on the rightmost spine of the tree. That's exactly why the algorithm processes every node level by level instead of guessing a path — the queue never skips a branch, so it can't miss a node like 0."*

**Cut rule:** If running late, skip the class tracing it themselves — state the shortcut, show the trace `8, 6, 5` on the board, and go straight to comparing it against `8, 6, 5, 0`.

---

## Exit Ticket (44–45 min)

> On paper or in chat: *"For the Example 1 tree (root 6; left 3, right 5; 3's children 7, 4; 5's children 9, 2), write the right-view list. Then, in one sentence, say why the algorithm tracks the LAST node dequeued at each level rather than the first."*
> **Answer:** `6, 5, 2`. Because level-order traversal processes each level strictly left to right, so the last node dequeued at a level is the one furthest right — and therefore the one actually visible from the right side.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Right view = keep following `root → right → right → ...` until null | Works on small/symmetric trees, feels intuitive | Activity 3 — tracing it against Example 2's tree, where it misses node `0` entirely |
| The rightmost node at a level is the one with the biggest *value* | "Rightmost" sounds like a size comparison | Activity 1 — Level 2 of Example 1 has `9` and `2`; the bigger number (`9`) is not the answer |
| Forgetting to snapshot `len = q.size()` before the inner loop | Feels redundant when you can "just check the queue" | Slide Block B checkpoint — showing what breaks if children get counted into the current level's loop |
| Right View needs a map/column-tracking like Top View | The two sessions are back to back and both use a queue | Warm-Up Poll Q3/Q5 contrast, restated explicitly in Slide Block A |
| `temp` accumulates a list across the whole level | It's used inside a loop, so it "feels" like it should collect values | Activity 2 — `temp` is overwritten every iteration; only the last value survives to `ans` |

---

## Instructor Notes

- **This session reuses Session 11's queue/BFS mechanic almost entirely** — the only real conceptual delta is "track the last node per level" instead of "track the first node per column via a map." Lean on that contrast constantly; it is the highest-value teaching lever available and it's exactly what the Warm-Up Poll is built to surface.
- **Slides 26–35 of the deck repeat slides 4–25** in summary form (problem, approach, complexity, code, all again). Treat them as optional recap — cut first if the session is running long.
- **Slides 36–89 ("Logical Approach")** re-walk the *same* tree as the concise dry run (slides 10–18), just broken into much smaller steps (get size → front → enqueue children → push to temp → push temp to ans). Don't project all of it live — it's the backing detail for Activity 2's relay, use it there and skip re-showing it as slides.
- **The deck states the result list's space cost is "at most N"** (slide 21) — true as a loose upper bound, but the list is really bounded by the tree's height (number of levels), which is usually far smaller than N. Don't over-correct this in front of students; the deck's simplification is fine at this level. Mention the tighter bound only if a sharp student pushes on it.
- **Constraints are tiny** (`0 <= n <= 50`) — the examples are small on purpose; reassure students the O(N)/O(N) analysis holds regardless of tree size.
