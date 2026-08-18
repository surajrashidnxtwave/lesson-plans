# Session 10 — Maximum Path Sum of Binary Tree

**Duration** 60 min · **Topic** Binary Tree — Maximum Path Sum · **Prerequisite** Diameter Of Binary Tree (Session 09)
**Session type** Concept lecture

<!-- placement: inferred — 60 min rather than the 45 min default. The deck teaches this concept in two full passes: (1) the algorithm itself — path vocabulary, approach, dry run, pseudocode/code/complexity (slides 1–49) — and (2) a second, slower pass building the "deflection point" intuition for WHY the recursion works, plus a line-by-line pseudocode walkthrough (slides 50–121). Diameter (Session 09) only needed one such pass around a single idea (height + running max); this session stacks a second conceptual layer (any-node paths, ignoring negative branches, deflection points) on top of that pattern, which is genuinely more to teach in one sitting. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Maximum Path Sum of Binary Tree | https://docs.google.com/presentation/d/1GfVjTl50KdOAQMpefbNJ8GRcX4GXOln4qnkuWlOgckw/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a path in a binary tree, distinguishing path length (number of edges) from path sum (sum of node values), and state that a path may start and end at any node — including the same node twice. *(REMEMBERING)*
2. Explain the approach: at each node, combine the node's own value with the maximum path sums contributed by its left and right subtrees, ignoring any subtree whose contribution is negative. *(UNDERSTANDING)*
3. Apply the `maxDownPath` / `maxPathSum` recursion by hand to compute the maximum path sum of a given binary tree. *(APPLYING)*
4. Analyze, for a given node, the difference between the value it can *return* to its parent (its single best branch) and the value it *contributes* to the global answer (both branches combined) — the deck's own "deflection point" idea. *(ANALYZING)*
5. State and justify the O(N) time / O(H) space complexity of the optimal approach. *(UNDERSTANDING)*

<!-- placement: inferred — phrased from the deck's own Approach/Complexity recap slides (15, 19, 42–43, 48–49, 82) -->

---

## Warm-Up Poll — Retrieval Practice on Session 09 (Diameter Of Binary Tree) (0–7 min)

Say: *"Eight quick ones on yesterday's Diameter of a Binary Tree before we build on top of it today."*

**Q1.** What is the diameter of a binary tree measured in?
`A` Number of nodes · `B` Number of edges · `C` Number of levels · `D` Sum of node values
→ *Read:* B. The deck is explicit: "the length of a path is measured by the number of edges." If a chunk of the room says A (nodes), correct it now — today's session reuses the same tree-walking pattern but sums *values* instead of counting edges, and that distinction needs to be crisp going in.

**Q2.** Must the diameter's longest path pass through the root?
`A` Always · `B` Never · `C` It may or may not · `D` Only in a skewed tree
→ *Read:* C.

**Q3.** In the brute-force diameter approach, what makes it O(N²)?
`A` Recomputing the height of a node's subtrees from scratch, for every single node in the tree · `B` Using two arrays instead of one · `C` Sorting the nodes first · `D` Recursion depth exceeding N
→ *Read:* A. If C or D come up, the "height gets recalculated over and over" mental model hasn't landed — restate it before Q4, since today's optimal approach is a direct answer to this exact inefficiency.

**Q4.** What did the optimal `height(root, &ans)` function do that the brute-force `height()` didn't?
`A` It also updated a global `ans` with the current node's diameter candidate, as a side effect of computing height · `B` It returned the diameter directly instead of the height · `C` It used a queue instead of recursion · `D` It ignored negative subtree heights
→ *Read:* A. This "do the useful side-effect update while you're already there computing something else" pattern is exactly what today's `maxDownPath` also does.

**Q5. (MSQ)** Select ALL that are true about the diameter formula used at each node.
`A` diameter-at-node = height(left subtree) + height(right subtree) · `B` height-at-node = 1 + max(height(left), height(right)) · `C` `ans` is updated with `max(ans, lh + rh)` · `D` diameter-at-node = max(height(left), height(right))
→ *Read:* A, B, C are correct.

**Q6.** For the 15-node dry-run tree (root = node 3), what final diameter value did the class compute?
`A` 7 · `B` 8 · `C` 9 · `D` 6
→ *Read:* B. If most say 7, they stopped tracking `ans` at node 3's own local diameter and never noticed node 6 overtake it with 8 — that's exactly why you keep a running max instead of trusting the root's own number.

**Q7.** In the optimal approach, the extra space used comes from:
`A` An explicit array of size N · `B` The recursion call stack · `C` A hash map storing all N heights · `D` Sorting overhead
→ *Read:* B.

**Q8. (MSQ)** Which of these are true about the O(H) space complexity? *(pick all that apply)*
`A` In a skewed tree, H = N, so space is O(N) · `B` In a balanced tree, H = log N, so space is O(log N) · `C` The brute-force and optimal approaches have different space complexities · `D` H is always equal to N
→ *Read:* A, B are correct. C is the trap — both approaches are O(H); only the *time* complexity improved from O(N²) to O(N).

**Running it** — poll tool, ~40–50 s per question. Total 7 min.

---

## Hook (7–11 min)

Say: *"Yesterday, every question was about edges — how many hops between two nodes. Today the tree has numbers on it, and some of those numbers are negative."*

Draw a tiny 3-node tree on the board: root `15`, left child `-20`, right child `25`. Ask: *"Diameter of this tree — trivial, it's 2 edges, root to either leaf. But what's the biggest total you can make by adding up values along some path through this tree?"*

Let a few guesses land (some will include `-20`). Then: *"By the end of today you won't want to touch that `-20` at all — and you'll know exactly why the algorithm agrees with you."*

---

## Slide Block A (11–20 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary, slides 1–15 -->
Covers: title/agenda → what a "path" is in a binary tree (path length = edges, path sum = sum of node values) → the worked path-length/path-sum example (tree with root 42, path `12 → 9 → 17 → 28`, length 3 edges, sum 66) → Problem Statement → Example 1 (root 15, children -20/25, output 50 via `15 → 25 → 10`) → Example 2 (output 27 via `12 → 1 → 10 → -4 → 8`).

**Beats to emphasise**

- **Path length ≠ path sum.** The deck deliberately shows both on the same example tree — 3 edges, but a sum of 66 — precisely so students stop conflating "how far" with "how much." Say both numbers out loud for that one example.
- **A path can start and end anywhere, including the same node.** This is a hard break from Session 09, where every dry run was framed leaf-to-leaf. Flag it as a deliberate contrast, not a footnote.
- Walk Example 1 and Example 2 exactly as the deck states the winning path — don't just show the output number, say the node sequence out loud both times.

**Checkpoint (at 20 min)** — cold-call two students:
> *"In one sentence — what's the difference between a path's length and a path's sum?"*
> **Answer:** Length is the number of edges on the path; sum is the total of the node *values* along that same path. A path can have a small length and a huge sum, or the reverse.

---

## ⚡ Activity 1 — Predict-the-Output (20–25 min)

**Format:** Predict-the-Output · **Exposes:** the instinct to route the answer through every "big-looking" node on the tree regardless of sign, instead of treating a negative branch as something to walk away from.

**Setup line (say this):**
> *"Here's the tree from Example 1 — root 15, with -20 and 25 as its two children, 5 hanging off -20, and 10 hanging off 25. Before I reveal the answer: write down the maximum path sum you can make in this tree, and which nodes it passes through."*

**What students do:** 30–45 seconds writing individually, then a show of hands between two or three candidate answers you write on the board (e.g., "35? 50? 0?").

**How it surfaces:** If someone lands on 35 or lower, ask them to say their path out loud, node by node — most wrong guesses either route through `-20` or add up every node in the tree instead of following one connected path.

**Debrief line:**
> *"The winning path is 15 → 25 → 10, sum 50. Node -20 and its child 5 never show up anywhere in the answer — the moment a branch's contribution goes negative, the algorithm treats it as worth zero rather than letting it drag the total down. That's the 'ignoring any negative sums' line from the approach slide, and it's the entire reason 50 beats every path that touches -20."*

**Cut rule:** If running short, skip the individual write-and-guess step — go straight to "35 or 50, hands up" and then the debrief.

---

## Slide Block B (25–34 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary, slides 15–49 -->
Covers: Approach (traverse every node top-to-bottom; at each node, use the left and right child's contribution, ignoring negative sums; keep a running max) → Dry Run on the tree rooted at node 1 (children 2 and 10; leaves 4, 3, 8; deeper nodes 9, 5, 6) → Pseudocode (`maxDownPath` / `maxPathSum`) → Complexity Analysis → C++/Python code.

**Beats to emphasise**

- Two variables, two jobs: `maxDownPath` **returns** `root.data + max(L, R)` — the best single branch a parent can extend — while it **updates** the global `ans` with `root.data + L + R` — both branches, because that's the biggest sum *through* this node as a bend point. This return-vs-update split is the crux of the whole algorithm; say it twice.
- Run the dry run exactly as the deck sequences it: leaves first (4, 3, then 8), up through 9, then 6, then 5, then 10, then finally node 1. Track `ans` out loud at every single update — it moves `-∞ → 4 → 9 → 8 → 17 → 6 → 11 → 38` and then stops changing at node 1 (34 doesn't beat 38).
- `ans` starts at `-1e9` / `-infinity`, **not** `-1` like yesterday's diameter — flag this contrast explicitly, since students will pattern-match on Session 09's `ans = -1`.
- Complexity: O(N) time (every node visited once), O(H) space (recursion stack) — same shape as yesterday's optimal diameter approach.

**Checkpoint (at 34 min)** — show hands:
> *"At node 10 in the dry run, the left branch contributed 17 and the right branch contributed 11. What did node 10 return to its parent (node 1), and what did it feed into `ans`?"*
> **Answer:** It returned `10 + max(17, 11) = 27` to node 1 — only its better branch. It fed `10 + 17 + 11 = 38` into `ans` — both branches. 38 turns out to be the final answer; 27 is just what got passed upward.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay (39–44 min)

**Format:** Dry-Run Relay · **Exposes:** whether students can independently keep the return-value and the `ans`-update separate at each node, without you narrating it for them.

**Setup line (say this):**
> *"Same tree as the deck's walkthrough — root 1, left child 2 (with leaf children 4 and 3), right child 10 (with subtree 9→8 on one side and 5→6 on the other). I'll call a node, you give me two numbers: what it returns to its parent, and what it feeds into the running max."*

**What students do:** Call on a different student per node, working bottom-up: node 4, node 3, node 2, node 8, node 9, node 6, node 5, node 10, node 1. Each answers with (return value, ans-candidate).

**How it surfaces:** The most common slip is feeding `max(L, R)` into `ans` instead of `L + R` (or vice versa for the return value). When it happens, write both formulas on the board side by side and re-ask just that one node.

**Debrief line:**
> *"Node 10 is where the real answer lives — 10 + 17 + 11 = 38, using BOTH its branches. But node 10 could only ever hand 27 up to node 1, because a path can't fork twice. That gap — 38 stays exactly where it is, 27 is all that travels upward — is the entire trick of this algorithm."*

**Cut rule:** If running short, skip the leaf nodes (4, 3, 8) — state their values aloud yourself (a leaf always returns its own value) and start the relay at node 9.

---

## Slide Block C (44–51 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary, slides 50–121 -->
Covers: a second pass over path vocabulary (any-node path, path sum, "maximum path sum" defined again with the -20/25/10 tree) → the "deflection point" framing, worked across the same 1/2/10-rooted tree — for each candidate deflection node (1, 2, 3, 4, 8, 9, 10, 5, 6), the deck computes the max path sum "deflecting" at that node, landing on node 10's 38 as the overall winner → a line-by-line walkthrough of the `maxDownPath`/`maxPathSum` pseudocode.

**Beats to emphasise**

- **"Deflection point" is just a name for "the node where the path stops going up and starts going down the other side."** Every node in the tree is a *candidate* deflection point; the algorithm's job is to find the deflection point with the highest combined sum. Node 10 (sum 38) wins over node 1 the root (sum 34) — deliberately picked by the deck to prove the answer does **not** have to live at the root.
- The deck recomputes the same 1/2/10 tree's deflection sums explicitly: node 1 → 34, node 4 → 4, node 2 → 9, node 3 → 3, node 10 → **38**, node 9 → 17, node 8 → 8, node 5 → 11, node 6 → 6. Reading this list aloud in order is the fastest way to make "the answer can be anywhere" concrete.
- The pseudocode walkthrough (line-by-line: base case → recurse left, clamp to 0 if negative → recurse right, clamp to 0 if negative → compute `x = data + L + R` → update `ans` → return `data + max(L, R)`) is a direct, slower repeat of Slide Block B — deliver it at a brisk pace as reinforcement, not as new material.

**Checkpoint (at 51 min)** — cold-call:
> *"Is the maximum path sum guaranteed to pass through the root? Why or why not?"*
> **Answer:** No — exactly like yesterday's diameter, the answer is the best value across *every* node's deflection sum, and in today's own worked example the winning deflection point is node 10, not the root (node 1).

---

## ⚡ Activity 3 — Spot the Bug (51–56 min)

**Format:** Spot the Bug · **Exposes:** what breaks when the "if L < 0, set L = 0" / "if R < 0, set R = 0" clamp is deleted from `maxDownPath` — i.e., why "ignoring negative sums" has to be an explicit step, not an assumption.

**Setup line (say this):**
> *"I've deleted two lines from the pseudocode — the ones that reset L and R to 0 when they come back negative. Same tree as Activity 1: root 15, -20 and 25 as children, 5 under -20, 10 under 25. Trace it by hand without the clamp — what wrong answer does this buggy version produce, and at which node does it go wrong?"*

**What students do:** In pairs, retrace node `-20` → node `15` on paper, without clamping negative values to zero.

**How it surfaces (the answer):** Without the clamp, node `-20` returns `-20 + max(5, 0) = -15` up to the root instead of being blocked. At node `15`: unclamped `L = -15`, `R = 35` (from the 25/10 branch), so `x = 15 + (-15) + 35 = 35`, and `ans` never climbs past 35. **The buggy version reports 35, not 50.**

**Debrief line:**
> *"Without the clamp, a strongly negative branch reaches all the way up and drags the root's own path sum down with it. Those two 'if it's negative, treat it as zero' lines are the only thing standing between this algorithm and a wrong answer on any tree that has a bad branch sitting above a good one."*

**Cut rule:** If running late, skip the pair-tracing — ask the class only "will the buggy answer be higher or lower than 50?" (lower), reveal 35, and go straight to the debrief.

---

## Exit Ticket (56–60 min)

> On paper or in chat: *"Draw any 3-node tree with at least one negative value. Write the maximum path sum, and underline the node(s) your path does NOT use."*
> **Answer shape:** Any correct trace is acceptable — the point is that the underlined, excluded node(s) should be exactly the ones whose branch contribution is negative.

Scan responses on the way out. If several students route their path *through* a negative node anyway, that's the signal to reopen Activity 3's clamp explanation before Session 11 begins.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A path must start at the root or end at a leaf | Session 09's diameter dry runs were always framed leaf-to-leaf | Slide Block A's explicit "any-node path" definition, reinforced in Activity 1 |
| A negative-valued node should still be included if the path "looks" high-value overall | Intuition says "more nodes = more chances at a big number" | Activity 1's predict-then-reveal, and Activity 3's Spot-the-Bug showing the wrong answer (35) without the clamp |
| The value returned to a node's parent is the same value used to update the global `ans` | Both use the same L/R inputs, so they look identical | The Slide Block B / Activity 2 checkpoint distinguishing `data + max(L,R)` (return) from `data + L + R` (ans update) |
| The maximum path sum must pass through the root | Carried over from thinking of trees as always rooted at the "main" computation | Slide Block C's deflection-point list, where node 10 (not the root) wins |
| `ans` should start at `-1` like yesterday's diameter | Direct pattern-matching from the immediately preceding session | Point out node values can be as low as -1000 (per the deck's stated constraints), so `-1` is not "negative enough" — `-1e9`/`-infinity` is required |

---

## Instructor Notes

- **Pacing risk:** This is a 60-minute session precisely because the deck teaches the idea twice (algorithm-first, then intuition-first via deflection points). If you're running long, compress Slide Block C's pseudocode line-by-line walkthrough — it repeats Slide Block B almost verbatim — rather than cutting either activity.
- **Activity 3's bug is the one worth protecting if time is tight.** It's the only place students see, concretely, *why* the negative-sum clamp exists rather than just being told it exists.
- **`ans = -1e9` vs `ans = -1`:** call this out by name at least twice (Slide Block B and again in the misconceptions table) — it is the single most likely copy-paste error students will carry into practice problems from yesterday's diameter code.
- **Keep the 1/2/10-rooted tree drawn on the board (or a slide) through Slide Block B, Activity 2, and Slide Block C** — all three sections reuse it, and redrawing it three times wastes minutes you don't have.
