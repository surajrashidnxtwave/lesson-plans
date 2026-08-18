# Session 3 — Pre-Order Traversal

**Duration** 45 min · **Topic** Binary Tree — Pre-Order Traversal · **Prerequisite** Binary Tree Traversals (Session 02)
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Pre-Order Traversal | https://docs.google.com/presentation/d/1T0Yi2jtmx18Gcfgg0Gz076sP5bj-zk2rEyq-k9p1xOQ/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define pre-order traversal as the Root → Left → Right visiting order. *(REMEMBERING)*
2. Explain why the recursion terminates at a null node (the base case) rather than treating it as an error. *(UNDERSTANDING)* <!-- placement: inferred -->
3. Trace a pre-order dry run on a given binary tree, correctly sequencing visits and backtracks, to produce the output list. *(APPLYING)*
4. Write the pre-order recursive function (pseudocode, C++, or Python) from the Node template. *(APPLYING)*
5. Analyze the time and space complexity of pre-order traversal — O(n) time; O(h) space, with best case O(log n) and worst case O(n). *(ANALYZING)*

**Duration note:** 45 min (default). This session teaches one traversal end-to-end (problem → dry run → pseudocode → complexity → code) with no additional topics layered on, so it doesn't demand the longer runtime a multi-topic session would need. <!-- placement: inferred -->

---

## Warm-Up Poll — Retrieval Practice on Session 02 (0–7 min)

Say: *"Before we go one level deeper today, eight quick questions on everything from last session. This is the whole foundation today is built on — if it's shaky, tell me now, not halfway through a dry run."*

Reference tree for all tree-specific questions below: root = 1; 1's children = 2 (left), 3 (right); 2's children = 4 (left), 5 (right); 3's children = 6 (left), 7 (right).

**Q1.** What does "traversal" mean for a binary tree?
`A` **Visiting each node exactly once, in a specific order ✅** · `B` Visiting only the leaf nodes · `C` Deleting every node once · `D` Counting the nodes

**Q2.** Which DFS order visits a node itself before either of its subtrees?
`A` In-order · `B` **Pre-order ✅** · `C` Post-order · `D` Level-order
→ *Read:* This is today's entire session. If this is shaky, slow down Slide Block A.

**Q3.** For the reference tree, what is the in-order traversal?
`A` 1,2,4,5,3,6,7 · `B` **4,2,5,1,6,3,7 ✅** · `C` 4,5,2,6,7,3,1 · `D` 1,2,3,4,5,6,7

**Q4.** For the reference tree, what is the pre-order traversal?
`A` **1,2,4,5,3,6,7 ✅** · `B` 4,2,5,1,6,3,7 · `C` 1,3,2,7,6,5,4 · `D` 4,5,2,6,7,3,1
→ *Read:* If this is wrong, today's whole dry run will feel arbitrary instead of mechanical. Worth a 20-second recap before Slide Block A.

**Q5.** For the reference tree, what is the post-order traversal?
`A` 1,2,4,5,3,6,7 · `B` 4,2,5,1,6,3,7 · `C` **4,5,2,6,7,3,1 ✅** · `D` 1,3,2,6,7,4,5
→ *Read:* This is usually the weakest-recalled of the three orders. <!-- placement: inferred — the source deck's own slide states this sequence ending in "10" instead of "1"; corrected here since post-order must always end on the root. See Instructor Notes. -->

**Q6.** What is the level-order (BFS) traversal of the reference tree?
`A` **1,2,3,4,5,6,7 ✅** · `B` 4,5,6,7,2,3,1 · `C` 1,2,4,3,6,5,7 · `D` 7,6,5,4,3,2,1

**Q7 (MSQ — pick all that apply).** Which of these are true of DFS?
`A` **Goes deep into one subtree before moving to the next ✅** · `B` **Uses recursion or an explicit stack ✅** · `C` Visits nodes level by level · `D` Is another name for BFS
→ *Read:* If C or D got picked, they're merging DFS with BFS/level-order. Clear that up now — today's session is a single specific DFS order, and that confusion will resurface all week.

**Q8.** Which DFS order visits a node itself only after BOTH its subtrees are fully done?
`A` In-order · `B` Pre-order · `C` **Post-order ✅** · `D` Level-order

**Running it** — poll tool, ~50 s per question. Project the distribution after each question. Total 7 min including reads.

---

## Hook (7–10 min)

Put the Example 1 tree from today's slides on the board (root 1; children 2, 3; 2's children 4, 5; 5's right child 8; 3's children 6, 7) with nothing labelled except an arrow reading "Root → Left → Right" next to it.

Ask: *"Last session you learned three DFS orders. Today we're zooming into exactly one — pre-order. I've given you the whole rule already: root, then left, then right. Without me teaching anything else, shout out what you think the full pre-order sequence is for this tree."*

Let 2–3 guesses happen. Most will get the first couple of values right (1, 2) and then drift — because "root first" alone doesn't say *how far* into the left subtree to go before switching to the right one.

Then: *"You're not wrong about the rule. You're missing the mechanism — and that mechanism is recursion. That's the whole session."*

---

## Slide Block A (10–19 min) — DELIVER SLIDES AS-IS

Covers: Problem Statement → Approach → Dry Run (the 8-node example tree: root 1; children 2, 3; 2's children 4, 5; 5's right child 8; 3's children 6, 7).

**Beats to emphasise**

- **The Approach is two rules, not one.** (1) Visit the current node *first* — print/store its value. (2) Recurse left completely, *then* recurse right. Say both out loud every time the dry run reaches a new node.
- **The null check is the whole stopping condition.** When the dry run says a node "has no left or right child," that's the base case firing silently — it isn't a special case; it fires at every leaf and at every missing child.
- **Backtracking is one level at a time.** The dry run explicitly narrates "Backtracking from node X to node Y" at each step — never straight to the root. This is the single detail Activity 1 tests hardest.
- Run the dry run at the deck's own pace, node by node: 1 → 2 → 4 (leaf, backtrack) → 5 → 8 (leaf, backtrack to 5, then to 2) → backtrack to 1 → 3 → 6 (leaf, backtrack) → 7 (leaf, backtrack to 3, then to 1, done). Final sequence: **1, 2, 4, 5, 8, 3, 6, 7.**

**Checkpoint (at 19 min)** — cold-call one student:
> *"Without looking at the slide — what's the FIRST value pre-order ever adds to the result, and why is it always that value no matter how the tree is shaped?"*
> **Answer:** The root. Pre-order visits the current node before recursing into either subtree, so the very first visit is always the root.

---

## ⚡ Activity 1 — Dry-Run Relay (19–24 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students are tracking the recursive backtracking step by step, versus having simply memorized the final sequence `1, 2, 4, 5, 8, 3, 6, 7`.

**Setup line (say this):**
> *"Same tree that's on the slide. I'm going to point at a node, and whoever I call on tells me ONE of three things: 'visit' — add it to the result, 'go left' or 'go right', or 'backtrack to node X'. No reciting the final answer — track it live with me, one move at a time."*

**What students do:** Instructor points at Node 1; a student says "visit." Point at the left child; next student says "go left, to Node 2." Continue round-robin through the deck's own dry-run sequence, calling on a different student at each step.

**How to handle wrong answers:** If a student calls "visit" on an already-visited node, or skips a backtrack step, stop immediately and re-trace the previous two moves on the board using the tree diagram. Do not let the relay continue on a broken step — every later move depends on the current position being right.

**Debrief line:**
> *"Notice you never had to remember 'the algorithm' as a whole. Every move was forced by two rules: root first, then all the way left before you're allowed to go right. That's what recursion actually is — no memory required, just the same rule applied at every node."*

**Cut rule:** If running short, skip the right subtree (nodes 6 and 7) — jump from "backtrack to Node 1, go right to Node 3" straight to the completed sequence. The left-subtree portion (nodes 2, 4, 5, 8) already exposes the backtracking mechanic. Do not cut the debrief line.

---

## Classroom Quiz (24–29 min)

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–37 min) — DELIVER SLIDES AS-IS

Covers: Pseudocode → Complexity Analysis → C++ Code → Python Code.

**Beats to emphasise**

- **Pseudocode is three lines that mirror the Approach exactly:** `if (root == null) return`, `print(root->data)`, then `preorder(root->left)` followed by `preorder(root->right)`. Point at each line and name which Approach rule it implements.
- **Time complexity O(n):** every node is visited exactly once, no repeated work — say this plainly, it's the easy half.
- **Space complexity O(h):** the cost isn't the values printed, it's the call stack built up by recursion. Best case (balanced tree) is O(log n); worst case (a fully skewed, linked-list-shaped tree) is O(n). Use the on-screen tree — it has 8 nodes and height 3, i.e. `h = log2(8)`, so it's sitting at the *best* case, not the worst.
- Run the C++ and Python code live if time allows — both implement exactly the pseudocode's three lines.

**Checkpoint (at 37 min)** — show hands:
> *"Our tree has 8 nodes and a height of only 3. Is `O(h)` here behaving like the best case or the worst case?"*
> **Answer:** Best case — `log2(8) = 3`, so height equals `log n` almost exactly. A worst-case (fully skewed) 8-node tree would have height 8, not 3.

---

## ⚡ Activity 2 — Predict-the-Output (37–41 min)

**Format:** Predict-the-Output · **Exposes:** whether students understand pre-order's Root → Left → Right *rule*, versus having only memorized this specific tree's sequence.

**Setup line (say this):**
> *"One line on the pseudocode is about to move. `preorder(root->left)` and `preorder(root->right)` swap places. Same tree, root is still visited first. Before I reveal the new output — write down your prediction."*

**What students do:** Individually or in pairs, write a predicted sequence for the swapped pseudocode on the same 8-node tree, then compare against the reveal.

**How to handle wrong answers:** The most common wrong guess is reversing the *entire* original sequence. If that comes up, walk the swapped version live on the board, node by node — showing that only the left/right choice at each individual node flips, not the whole list.

**Debrief line:**
> *"Root went first both times — that never changed. What changed is which subtree gets explored first. That's the entire difference between pre-order and the two other DFS orders you'll meet this week."*

**Answer key:** Swapped (Root → Right → Left) output for this tree: **1, 3, 7, 6, 2, 5, 8, 4.**

**Cut rule:** If running short, skip the individual-write step and go straight to a show-of-hands guess before revealing. Keep the reveal and the debrief line — they carry the point.

---

## Exit Ticket (41–45 min)

On paper or in chat before anyone leaves, give this small tree (root 1; left child 2; right child 3; 2's left child 4 — built small on purpose so the answer can't be recalled from memory rather than derived): <!-- placement: inferred — transfer tree, not sourced from the deck, chosen to test the rule rather than a memorized output -->

> Write the pre-order traversal of this tree: root = 1, 1.left = 2, 1.right = 3, 2.left = 4.
> **Answer:** `1, 2, 4, 3`.

Scan responses on the way out. A wrong answer here almost always means the backtrack-after-a-leaf step (Node 4 → back to Node 2 → on to Node 3) didn't land — flag it for a 60-second recap at the start of Session 4.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Pre-order, in-order, and post-order mnemonics blur together | All three share the words "Left, Root, Right" in different orders | Warm-Up Poll Q4/Q5, then writing "Pre = Root first, always" on the board and leaving it up |
| Recursion "jumps" straight back to the root after a leaf, instead of backtracking one parent at a time | Students see the final list, not the call-stack mechanics | Activity 1's node-by-node relay, forcing an explicit "backtrack to node X" call at every step |
| The null check exists to "handle an error" | School conditioning treats hitting a missing case as a failure | Pointing at the pseudocode's `if (root == null) return` and naming it as the mechanism that ends every branch cleanly, not an error path |
| Space complexity is O(1) because recursion "doesn't use extra memory" | The recursive calls are invisible — nothing is explicitly allocated on screen | Tracing call-stack frames live against the dry run during Slide Block B |
| O(h) and O(n) are two unrelated formulas rather than the same formula at different tree shapes | Best-case and worst-case are taught as separate facts to memorize | The Slide Block B checkpoint — computing `h` for the actual on-screen tree and comparing it to both extremes |

---

## Instructor Notes

- **Pacing risk:** Slide Block A's dry run is the heart of the session — do not compress it to save time elsewhere. If you're behind schedule, use Activity 1's cut rule instead of rushing the dry run itself.
- **The tree is small on purpose (8 nodes).** Resist the urge to draw a bigger tree for the live activities — the deck's own tree is what the Classroom Quiz and any follow-up practice will most likely reference.
- **Have the tree diagram already drawn on the board** (or the slide already open) before Activity 1 starts — redrawing it live costs 2 minutes you don't have in a 45-minute session.
- **This is session 1 of a 3-session arc** (pre-order → in-order → post-order) that all reuse the same recursive skeleton with only the print statement's position changing. Flag this explicitly at the end of the session — it previews Session 4 and makes the whole arc feel like one idea, not three.
- **Warm-Up Poll Q5 answer corrected from the source deck.** Session 02's own slide states the reference tree's post-order sequence ending in "10" instead of "1" — almost certainly a stray digit picked up from a diagram during slide authoring, since post-order must always end on the root. Verify against the live deck before class; the corrected value used here is `4, 5, 2, 6, 7, 3, 1`.
