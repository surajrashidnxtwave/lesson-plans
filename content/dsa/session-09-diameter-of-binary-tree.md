# Session 9 — Diameter Of Binary Tree

**Duration** 55 min · **Topic** Diameter of a Binary Tree · **Prerequisite** Balanced Binary Tree (Session 08)
**Session type** Concept lecture

<!-- placement: inferred — this is the largest deck in the course (282 slides). The optimal-approach dry run alone spans roughly 200 of those slides because the deck walks the SAME 13-node tree twice — once as a call-stack narrative (slides ~74-97) and once again at extreme slide-by-slide granularity (slides ~157-280). That is a genuinely long worked example to teach properly, on top of a brute-force approach, a definition that breaks the "measured in nodes" habit from Sessions 07-08, and a prerequisite retrieval poll. 55 min (top of the suggested 50-55 range) was chosen over 45-50 because the core dry run cannot be rushed without losing the exact point the deck is making: the answer is decided at a node that isn't the root. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Diameter Of Binary Tree | https://docs.google.com/presentation/d/1uGpBJp47qrMbWN1Gd_GFTn_bKJ8B2k736frID7ONmn8/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define the diameter of a binary tree as the longest path, measured in **edges**, between any two nodes — noting the path may or may not pass through the root. *(REMEMBERING)*
2. Explain why the brute-force approach recomputes `height()` from scratch at every node, producing O(N²) time. *(UNDERSTANDING)*
3. Apply the brute-force algorithm by hand — at each node, sum left height + right height, track a running global maximum — to a given tree. *(APPLYING)*
4. Apply the optimal single-traversal algorithm (one `height()` call that also updates a running max diameter) to a given tree, producing both the height and the diameter in one pass. *(APPLYING)*
5. Analyze why combining height computation and diameter tracking into a single function eliminates the brute force's redundant work, reducing complexity from O(N²) to O(N). *(ANALYZING)* <!-- placement: inferred phrasing, content drawn from deck's own closing Brute Force / Optimal Solution summary slides -->

---

## Warm-Up Poll — Retrieval Practice on Session 08 (0–8 min)

**Prerequisite: Balanced Binary Tree (Session 08)**

Say: *"Seven on last session before we move on. Today's problem reuses the exact same `height()` helper again — third session in a row — so if the balance check isn't solid, tell me now."*

**Q1.** What must be true at **every** node for a binary tree to be called "balanced"?
`A` Left and right subtree heights are exactly equal · `B` Left and right subtree heights differ by at most 1, and both subtrees are themselves balanced · `C` The tree has the same number of nodes on both sides · `D` All leaves are at the same level
→ *Read:* Answer B. If they pick A, that's the "must be equal" misconception from last session — the ≤1 tolerance is the whole point.

**Q2.** Last session's unbalanced example tree failed at one specific node, even though the root itself passed. Which node?
`A` The root · `B` Two levels down, where one side's height was 2 more than the other · `C` A leaf node · `D` It never failed
→ *Read:* Answer B. This is the "root looked fine but a deeper node failed" trap — flags whether last session's key takeaway actually stuck.

**Q3.** What is the time complexity of the **brute-force** balanced-tree check, and why?
`A` O(N), each node visited once · `B` O(N²), because `height()` is recomputed from scratch at every node · `C` O(log N) · `D` O(H)
→ *Read:* Answer B.

**Q4.** What is the one change the optimal approach makes to fix that?
`A` It skips checking some nodes · `B` It combines height computation and the balance check into a single traversal using a shared flag · `C` It uses a different formula for height · `D` It only checks leaf nodes
→ *Read:* Answer B. Hold onto this idea — today's optimal diameter approach makes the *identical* move.

**Q5.** *(MSQ — pick all that apply)* Which are true of the shared `ans`/flag variable in the optimal balanced-tree check?
`A` It starts as `true` · `B` It can flip back to `true` after being set `false` · `C` It gets set `false` the moment any node's height difference exceeds 1 · `D` It's shared across recursive calls
→ *Read:* A, C, D. B is false and worth calling out explicitly — once it fails, it stays failed.

**Q6.** For the optimal balanced-tree check, what determines its space complexity?
`A` O(N), always, regardless of tree shape · `B` O(H) — the recursion stack depth, worst case O(N) for a skewed tree · `C` O(1) · `D` O(N²)
→ *Read:* Answer B. (The deck's own slide states this inconsistently in one spot — if a student answers "O(N)" don't mark it wrong outright; use it to reinforce that O(H) and worst-case-O(N) are the same idea, not competing ones.)

**Q7.** True or False: a tree can be balanced overall even if it isn't visually symmetric — the same shape on both sides.
→ **True.** *Read:* Ties back to last session's balanced 8-node example, which passed without being shape-symmetric.

**Running it** — poll tool, ~40 s per question. Total 8 min including reads.

---

## Hook (8–11 min)

Draw the deck's small 6-node tree on the board: root 1; children 2, 3 (node 3 is a leaf); node 2's children 4 (leaf) and 5; node 5's child 6 (leaf).

Ask: *"Longest path between any two nodes in this tree — not from the root, between *any two*. Go."*

Let a few guesses land — most will instinctively measure from the root, the way `height()` trained them to for two sessions straight. Then trace it on the board: node 6 → node 5 → node 2 → node 1 → node 3. *"Four edges. Notice this path uses the root — but it didn't have to. Today's whole session is about a path that, in the tree we'll spend the most time on, skips the root completely."*

---

## Slide Block A (11–22 min) — DELIVER SLIDES AS-IS

Covers: Introduction/Definition → Problem Statement → Example 1 (6-node tree) → Brute Force Approach → brief Dry-Run teaser → Pseudocode/Code → Complexity Analysis.

**Beats to emphasise**

- **Diameter is measured in edges, not nodes** — say this against Session 07/08's `height()`, which counts *nodes* along a path. Same tree, different unit, if students blur the two they'll be off by one on every answer from here on.
- **The path may or may not pass through the root.** This directly contradicts the pattern set by two sessions of root-anchored `height()` calls — call this out explicitly, it's today's single biggest habit to break.
- **Example 1** (the hook's tree): longest path is node 6 → 5 → 2 → 1 → 3, diameter = 4 edges.
- **Brute Force Approach:** for *every* node, sum `height(left) + height(right)`; keep a running global maximum across the whole tree. Contrast directly with last session: balanced-tree checks used the *difference* of left/right height; diameter uses the *sum* — same two ingredients, different arithmetic, different goal.
- **Brute-force teaser only** (full walk comes in the optimal section) — using the 13-node tree that anchors the rest of the session (root 3; children 4, 5; node 5's children 6, 7; node 6's children 8, 9; node 8's left child 10; node 10's left child 12; node 12's left child 14; node 9's right child 11; node 11's right child 13; node 13's right child 15):
  - At node 3 (root): left height = 1, right height = 6 → diameter here = 7 → `ans` = 7.
  - Skip ahead to node 6: left height = 4, right height = 4 → diameter here = 8 → `ans` = max(7, 8) = **8**. *"Hold that number — we're walking every node properly in a minute."*
- **Complexity:** O(N²) time (height recomputed fresh at every node), O(H) space.

**Checkpoint (at 22 min)** — cold-call:
> *"One sentence — what's the arithmetic difference between what `balanced()` checked last session and what `diameter()` checks today, given both call the same `height()` underneath?"*
> **Answer:** `balanced()` checks the *absolute difference* between left and right height at each node (must be ≤ 1). `diameter()` checks the *sum* of left and right height at each node, and keeps the running maximum across the whole tree.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Spot the Bug: Counting the Redundant Walks (27–33 min)

**Format:** Spot the Bug · **Exposes:** students accept "O(N²)" as a label without seeing *why* — they don't realize a deep leaf gets walked past by a fresh `height()` call from every single ancestor that needs a height above it.

**Setup line (say this):**
> *"Same 13-node tree from the teaser — node 14 is buried four levels down, under node 12, under node 10, under node 8, under node 6. When `diameter()` walks the tree top-down, calling `height()` on both children at every node it visits, how many separate `height()` calls end up passing through node 14 before the whole thing finishes?"*

**What students do:** Trace, on the board, every ancestor of node 14 that fires a `height()` call reaching down through it — node 12 (when computing its own left height), node 10 (same, one level up), node 8 (same), node 6 (same) — count them out loud.

**How it surfaces:** If a student says "just once, it's a leaf," redirect: *"Once for the `height()` call fired directly at it — but how many *different* `height()` calls, started at *different* nodes, pass through it on their way down?"* Walk the count together.

**Debrief line (say this):**
> *"Every node on the way to a deep leaf gets re-measured once for every ancestor above it that needed a height — and this happens for every node in the tree, not just node 14. That repeated re-walking, multiplied across the whole tree, is the entire O(N²). The optimal approach fixes exactly this the same way last session's balanced-tree fix did: one function computes the height and updates the answer, in the same single pass."*

**Cut rule:** If running short, state the redundancy verbally using node 14 as the example instead of counting live on the board — but keep the debrief line, it's the bridge into Slide Block B.

---

## Slide Block B (33–48 min) — DELIVER SLIDES AS-IS

Covers: Optimal Approach → the full node-by-node Dry Run (13-node tree) → Pseudocode → Complexity Analysis.

**Beats to emphasise**

- **The one-line insight, same shape as last session's fix:** fold the diameter tracking *into* `height()` itself. `height(root, &ans)` still returns `1 + max(leftHeight, rightHeight)` exactly as before — but on the way, it also does `ans = max(ans, leftHeight + rightHeight)`.
- **The full Dry Run — this is the core of the session.** Tree: root 3; children 4 (leaf), 5; node 5's children 6, 7 (leaf); node 6's children 8, 9; node 8's left child 10 (no right); node 10's left child 12 (no right); node 12's left child 14 (leaf); node 9's right child 11 (no left); node 11's right child 13 (no left); node 13's right child 15 (leaf). Walk it leaf-up, exactly as the deck does:

  | Node | left height | right height | diameter here (lh+rh) | ans after | height returned |
  |---|---|---|---|---|---|
  | 4 (leaf) | 0 | 0 | 0 | -1 → 0 | 1 |
  | 14 (leaf) | 0 | 0 | 0 | stays | 1 |
  | 12 | 1 (from 14) | 0 | 1 | 0 → 1 | 2 |
  | 10 | 2 (from 12) | 0 | 2 | 1 → 2 | 3 |
  | 8 | 3 (from 10) | 0 | 3 | 2 → 3 | 4 |
  | 15 (leaf) | 0 | 0 | 0 | stays | 1 |
  | 13 | 0 | 1 (from 15) | 1 | stays (1 < 3) | 2 |
  | 11 | 0 | 2 (from 13) | 2 | stays (2 < 3) | 3 |
  | 9 | 0 | 3 (from 11) | 3 | stays (3 = 3) | 4 |
  | **6** | **4 (from 8)** | **4 (from 9)** | **8** | **3 → 8** | 5 |
  | 7 (leaf) | 0 | 0 | 0 | stays | 1 |
  | 5 | 5 (from 6) | 1 (from 7) | 6 | stays (6 < 8) | 6 |
  | 3 (root) | 1 (from 4) | 6 (from 5) | 7 | stays (7 < 8) | 7 |

  **Final diameter = 8.** Longest path: 14 → 12 → 10 → 8 → 6 → 9 → 11 → 13 → 15 (8 edges) — and it never touches the root.
- **Stop on node 6 explicitly.** It's the one node whose *both* sides are tall (height 4 each) — it's the lowest common ancestor of the two farthest-apart leaves (14 and 15). Every other node on the eventual path only has one tall side, which is why `ans` only ever jumps at node 6.
- **Complexity:** Time O(N) — each node visited exactly once, constant work per node. Space O(H), recursion stack, worst case O(N) for a skewed tree.

**Checkpoint (at 48 min)** — cold-call:
> *"Why does `ans` jump from 3 to 8 at node 6, and not at the root, or anywhere else?"*
> **Answer:** Node 6 is the only node where both the left subtree (through 8→10→12→14) and the right subtree (through 9→11→13→15) are equally tall (height 4 each). Every node above node 6 — node 5, node 3 — only has one side that's tall; the other side is short, so their sums never come close to 8.

---

## ⚡ Activity 2 — Dry-Run Relay: Finish the Same Tree (48–53 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can now track `lh`, `rh`, and `ans` themselves — specifically whether they understand that height changes at *every* node while `ans` only changes *sometimes*.

**Setup line (say this):**
> *"Same tree, same table we just built. I'll point at a node from the right-hand branch — 9, 11, 13, or 15 — you give me left height, right height, diameter-here, and whether `ans` changes, before I confirm. If your height's right but your `ans` call is wrong, that's the mistake today is about."*

**What students do:** Relay through nodes 15, 13, 11, 9 in that order (leaf-up, right branch), one student per node, cold-called, stating all four numbers before the instructor confirms against the table above.

**How it surfaces:** The most common error is updating `ans` using the node's own *returned height* instead of `lh + rh` — e.g. saying "`ans` = 4" at node 9 instead of "diameter here = 0 + 3 = 3, no change, `ans` is already 3." When this happens, point at the pseudocode line `ans = max(ans, lh + rh)` and have the student re-read it aloud before re-answering.

**Debrief line (say this):**
> *"Height climbed by one at every single node on this branch. `ans` barely moved — it only jumps when a node has real length on *both* sides at once, and this branch never did until it joined up with the left branch at node 6. Two different numbers, tracked by the exact same function call — that's the whole trick of the optimal approach."*

**Cut rule:** If running short, relay only nodes 9 and 6 — the two nodes where `ans`'s final value is actually decided — and state the leaf/13/11 results directly instead of relaying them.

---

## Exit Ticket (53–55 min)

> Using today's 13-node tree: what is height(node 8), and what is the diameter contribution *at* node 8 (left height + right height)? Is node 8 responsible for the tree's final diameter of 8? Why or why not?
> **Answer:** height(8) = 4; diameter at node 8 = 3 + 0 = 3. Node 8 is **not** responsible — its own local sum never gets close to 8. The final diameter of 8 is decided entirely at node 6, where node 8's subtree height (4) and node 9's subtree height (4) combine as node 6's left and right heights.

Scan responses on the way out. If students answer "yes, node 8" or point at the root, that's the "diameter lives at the root" misconception resurfacing — worth a 30-second correction at the start of the next session.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Diameter is measured in nodes, like height | Two straight sessions of `height()` counting nodes | Contrast the hook's path (6→5→2→1→3, 4 edges, 5 nodes) — say both numbers out loud |
| The diameter always passes through the root | Every worked example in Sessions 07-08 was root-anchored | Point at node 6 in the main dry run — not the root — where the actual maximum is decided; the winning path never touches node 3 |
| `diameter()` and `balanced()` check the same thing, since both call `height()` | Same helper function, same recursive shape | Slide Block A's checkpoint: difference (≤1 test) vs. sum (maximize) |
| `ans` updates every time `height()` is called | Both live inside the same function call | Activity 2's debrief — height changes at every call; `ans` only changes when `lh + rh` beats the running max |
| Brute force "isn't really different," just written with two functions | The arithmetic (`lh + rh`, compare to max) looks identical in both approaches | Activity 1 — counting how many times a single deep leaf gets re-walked by separate `height()` calls |

---

## Instructor Notes

- **This deck is one dry run told three ways.** The 13-node tree's optimal-approach computation appears as a call-stack narrative (~slides 74-97) and again at extreme slide-by-slide granularity, one pseudocode line highlighted per slide (~slides 157-280) — well over 200 of the deck's 282 slides cover this single computation. This lesson plan follows the call-stack narrative pace; treat the ultra-granular slides as a self-study pointer for a student who wants to see every micro-step, not something to click through live.
- **The dry-run chain, concretely:** starting from the brute-force teaser in Slide Block A through the end of Activity 2, the class works the *same* 13-node tree and the *same* final answer (diameter = 8) three separate times at increasing depth — teaser (2 nodes), full table walk (13 nodes), and student relay (a subset). This repetition is deliberate scaffolding, not redundancy — say so if a sharp student asks why they're doing "the same tree again."
- **Reuse the board drawing.** The 13-node tree (root 3 → 4, 5; 5 → 6, 7; 6 → 8, 9; 8 → 10 → 12 → 14; 9 → 11 → 13 → 15) is used in the brute-force teaser, the full optimal dry run, and Activity 2 — draw it once, leave it up for the whole session.
- **Continuity note worth a spoken aside:** the deck's *other* optimal-approach dry run (an 8-node tree: root 1; children 2, 3; node 2's children 4, 5; node 4's left child 8; node 3's children 6, 7 — giving diameter 5) is the *identical* tree used for Session 08's balanced-tree optimal dry run. Worth telling students explicitly — "same tree, different question, same `height()` underneath" — as a continuity anchor, even though this lesson plan builds its main dry run and both activities around the 13-node tree instead, since that's the one the deck's most detailed walkthrough uses.
- **Deck data-quality flag:** Example 2 in the edge-counting section (a separate, smaller reinforcement example, not the 13-node tree used above) has two different nodes both extracted as label "7" in the source text. <!-- placement: inferred — likely an artifact of overlapping diagram text boxes in the original slide; verify the actual node labels against the live slide before presenting that particular example, or skip it in favor of Example 1 and the 13-node tree, both of which are unambiguous. --> This lesson plan does not rely on that example anywhere.
- **Protect Slide Block B's time above all else.** If the session is running behind by the Classroom Quiz, trim the brute-force teaser in Slide Block A further (state node 6's numbers directly instead of also showing node 3) rather than compressing the optimal dry run — that dry run is the actual skill being taught today.
