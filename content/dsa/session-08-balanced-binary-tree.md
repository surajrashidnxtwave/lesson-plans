# Session 8 — Balanced Binary Tree

**Duration** 50 min · **Topic** Balanced Binary Tree · **Prerequisite** Height of a Binary Tree (Session 07)
**Session type** Concept lecture

<!-- placement: inferred — deck is 133 slides, dense with animation-frame repeats rather than new content; default 45 min was not enough to cover both approaches with two full dry runs and two activities, so duration was raised to 50 min. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Balanced Binary Tree | https://docs.google.com/presentation/d/1MscAVuewwMhNE52LB11SF5Rp6zEcccmvaXrZa08GS2I/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a balanced binary tree: for every node, the heights of its left and right subtrees differ by at most 1, **and** both subtrees are themselves balanced. *(REMEMBERING)*
2. Explain why the brute-force approach recomputes `height()` from scratch at every node, producing O(N²) time. *(UNDERSTANDING)*
3. Apply the brute-force algorithm by hand — compute the height difference at a node, decide pass/fail, recurse — to determine whether a given tree is balanced. *(APPLYING)*
4. Apply the optimal single-traversal algorithm (a shared `ans`/flag updated inside the same `height()` call) to check balance in one pass. *(APPLYING)*
5. Analyze why folding the balance check into the height computation eliminates the brute force's redundant work, cutting time complexity from O(N²) to O(N). *(ANALYZING)* <!-- placement: inferred phrasing, content drawn from deck's own Brute Force vs Optimal complexity summary slides -->

---

## Warm-Up Poll — Retrieval Practice on Session 07 (0–8 min)

**Prerequisite: Height of a Binary Tree (Session 07)**

Say: *"Eight quick ones on last session. Today's whole topic is built directly on top of `height()` — if that function is shaky, say so now, not halfway through today."*

**Q1.** What does the "height" of a binary tree measure?
`A` Number of nodes in the tree · `B` Number of levels in the tree / number of nodes along the longest root-to-leaf path · `C` Number of leaf nodes · `D` Number of edges only
→ *Read:* If most pick A, they're confusing height with total node count (size) — clear this up in one sentence before Slide Block A, which assumes it's solid.

**Q2.** What is the height of an empty tree?
`A` 0 · `B` 1 · `C` -1 · `D` Undefined
→ *Read:* Answer is A. Also restate: a tree with a single node has height 1 — that's the other base fact from Session 07 today's pseudocode inherits unchanged.

**Q3.** The recursive formula from last session: `height(root) = ?`
`A` `max(leftHeight, rightHeight)` · `B` `1 + max(leftHeight, rightHeight)` · `C` `leftHeight + rightHeight` · `D` `1 + leftHeight + rightHeight`
→ *Read:* Answer B. Today's `balanced()` calls this exact formula — verbatim, no changes.

**Q4.** *(MSQ — pick all that apply)* Which are true about `height()`'s base case?
`A` Returns 0 when root is null · `B` Returns -1 in the edges-based definition some textbooks use · `C` Returns 1 always · `D` Is checked before recursing into children
→ *Read:* A, B, D. This MSQ is the setup for today's pseudocode, which reuses this exact base case.

**Q5.** In Session 07's worked tree (root 1; children 2, 3; node 2's child 4; node 3's children 5, 6; node 5's child 7), height(4) = 1. What is height(node 2)?
`A` 1 · `B` 2 · `C` 3 · `D` 0
→ *Read:* height(2) = 1 + max(0, 1) = 2. Answer B. If they say 1, they forgot the `+1` for node 2 itself.

**Q6.** In the same tree, what is the final height of the whole tree (at the root)?
`A` 3 · `B` 4 · `C` 5 · `D` 2
→ *Read:* Answer B (h(1) = 1 + max(h(2)=2, h(3)=3) = 4). If they say 3, walk the `+1` at the root again out loud.

**Q7.** What is the time complexity of computing height recursively?
`A` O(log N) · `B` O(N) · `C` O(N²) · `D` O(H)
→ *Read:* Answer B — every node visited exactly once. Hold this number; today you'll see what happens when a function calls `height()` *repeatedly*.

**Q8.** What is the worst-case space complexity of `height()`, and when does it occur?
`A` O(H), worst case O(N) for a skewed tree · `B` O(N), always · `C` O(1) · `D` O(H), worst case O(log N)
→ *Read:* Answer A. This O(H)-via-recursion-stack idea carries over unchanged into today's functions — flag it now so it isn't "new" later.

**Running it** — poll tool, ~40 s per question, project the distribution. Total 8 min including reads.

---

## Hook (8–11 min)

Draw two trees on the board, same rough size, unlabeled as to which is which — one is today's deck's Example 1 (root with a node two levels down whose subtrees differ by 2), one is Example 2 (root with a node two levels down whose subtrees differ by 1). Don't say which is which.

Ask: *"By eye — which of these two looks more 'balanced' to you?"*

Let disagreement happen; both look like reasonably full trees at a glance. Then: *"Here's the problem — 'balanced' isn't a look. It's a number, checked at every single node, and the check is done by a function you already know."* Hold up last session's `height()` pseudocode. *"Today, `height()` gets called twice per node, and what it returns decides whether that node passes or fails. One of these two trees fails — and not at the root."*

---

## Slide Block A (11–22 min) — DELIVER SLIDES AS-IS

Covers: Introduction/Definition → Problem Statement → Example 1 (False) & Example 2 (True) → Brute Force Approach → brief Dry-Run glimpse → Pseudocode/Code → Complexity Analysis.

**Beats to emphasise**

- **The definition is recursive, not a one-time check.** "Heights differ by at most 1" must hold at *every* node, and both subtrees must *themselves* be balanced — say this twice, it's the single most missed idea of the session.
- **Example 1 (False):** the deck's own explanation: *"At node 2, left subtree height = 2, right subtree height = 0. Difference = 2 → exceeds 1. Hence the tree is not balanced."* Note out loud that this is a 7-node tree carried over from Session 07's own height example — same shape, new question.
- **The Dry Run for Example 1 only checks two nodes before it stops:** node 1 (its own children's heights differ by 1 → passes) then node 2 (its own children's heights differ by 2 → **fails, and the walk stops there**). <!-- placement: inferred — deck's dry run for this tree only shows checks at node 1 and node 2 before concluding "not balanced"; it does not walk node 3's side, illustrating that one failing node anywhere is enough to short-circuit the whole result. Exact left/right child assignment for this tree is not fully recoverable from the extracted deck text — the height/diff numbers themselves are given directly and are what this beat relies on. -->
- **Brute Force Approach:** for each node, `height()` is called fresh on both children — and that `height()` call walks the *entire* subtree underneath, every time. This is the seed for Activity 1.
- Quick teaser only — don't do the full brute-force dry run here (that's Activity 1's job): show the pseudocode's two functions (`height()`, `balanced()`) side by side and point out `balanced()` calls `height()` twice, then calls *itself* twice more, each of which will call `height()` twice again.
- **Complexity:** O(N²) time (`height()` recomputed at every node), O(H) space (recursion stack).

**Checkpoint (at 22 min)** — cold-call:
> *"In one sentence — what's the difference between what today's `height()`-based check does, and what plain `height()` did last session?"*
> **Answer:** Last session, `height()` just returned a number. Today, `balanced()` calls `height()` on both children and checks whether the *difference* between those two numbers is more than 1 — same helper function, new arithmetic on top.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Spot the Bug: Why Brute Force Is O(N²) (27–33 min)

**Format:** Spot the Bug · **Exposes:** students accept "O(N²)" as a label without seeing *why* — they don't realize `height()` re-walks the same subtree from scratch every time a different ancestor asks for it.

**Setup line (say this):**
> *"Here's the balanced tree from the deck — 8 nodes: root 1; children 2 and 3; node 2's children 4 and 5; node 4's left child 8; node 3's children 6 and 7. I'm going to call `height(node 4)` to check node 2's balance. That call walks down into node 8. Now — when `balanced()` recurses into node 4 itself a moment later, does it call `height()` on node 8 *again*?"*

**What students do:** Trace, out loud, how many separate `height()` calls — fired from different ancestor nodes on their way down — end up passing through node 8 before the whole check finishes. (Answer: at least twice — once when node 2 needs its left height, once when node 4 needs its own left height, potentially more depending on how the recursion is structured.)

**How it surfaces:** If a student says "no redundancy, each node is visited once" — point at node 8 specifically and ask them to count every distinct call stack that reaches it. Do the count on the board.

**Debrief line (say this):**
> *"Every node underneath gets walked past by one `height()` call for every ancestor above it that needed a height. That repeated walking, multiplied across every node in the tree, is the entire O(N²) — not a mysterious formula, just the same subtree being re-measured over and over. The optimal approach fixes exactly this: one function does the height *and* the balance check, in the same single walk."*

**Cut rule:** If running short, skip the full count and just state the redundancy verbally using node 8 as the example — but do not cut the debrief line, it's the entire bridge into Slide Block B.

---

## Slide Block B (33–43 min) — DELIVER SLIDES AS-IS

Covers: Optimal Approach → Dry Run (Example 2 tree, ans-tracking) → Pseudocode → Code → Complexity Analysis.

**Beats to emphasise**

- **The one-line insight:** fold the balance check *into* `height()` itself. A shared flag (`ans`, starting `true`) gets flipped to `false` the moment any node's height difference exceeds 1 — `height()` still returns the height as before, but now also updates `ans` on the way.
- **Full Dry Run on the 8-node tree** (root 1; children 2, 3; node 2's children 4, 5; node 4's left child 8; node 3's children 6, 7 — same tree as Activity 1), walked leaf-up exactly as the deck does it:
  - Node 8 (leaf): left=0, right=0 → diff=0, `ans` stays `true`. height(8) = 1.
  - Node 4: left=1 (from 8), right=0 (no right child) → diff=1, `ans` stays `true`. height(4) = 1 + max(1,0) = 2.
  - Node 5 (leaf): left=0, right=0 → diff=0, `ans` stays `true`. height(5) = 1.
  - Node 2: left=2 (from 4), right=1 (from 5) → diff=1, `ans` stays `true`. height(2) = 1 + max(2,1) = 3.
  - Node 6 (leaf) and Node 7 (leaf): both diff=0, both height=1.
  - Node 3: left=1, right=1 → diff=0, `ans` stays `true`. height(3) = 1 + max(1,1) = 2.
  - Node 1 (root): left=3 (from 2), right=2 (from 3) → diff=1, `ans` stays `true`. height(1) = 4.
  - **`ans` was never flipped — the tree is balanced.**
- **Contrast explicitly with Example 1's tree:** same two ingredients (left height, right height) at every node, but there `ans`/the check would flip to `false` the moment node 2's diff of 2 is found — and once flipped, no later node can undo it.
- **Complexity:** Time O(N) — `height()` now does double duty but is still called exactly once per node. Space: the deck states this as O(N) on its complexity slide, but its own explanation describes it as depending on the recursion stack depth (the tree's height, H) — i.e. **O(H), worst case O(N) for a skewed tree**, same shape as the brute-force approach's space complexity. <!-- placement: inferred — flagging a wording inconsistency in the deck's own slide (headline says "O(N)", explanation describes O(H)); teach it as O(H) worst-case O(N) so students aren't confused later when they see O(H) written elsewhere for the same idea. -->

**Checkpoint (at 43 min)** — show hands:
> *"At node 2 in the tree we just walked, height difference was 1 — a pass. If that had been a difference of 2 instead, does the tree still get checked past node 2, the way brute force does?"*
> **Answer:** No — the moment `ans` flips to `false`, it can never flip back. The rest of the traversal still runs (to compute remaining heights correctly) but the final answer is already decided.

---

## ⚡ Activity 2 — Dry-Run Relay: Track lh, rh, and ans (43–48 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can now do the height/diff/`ans` bookkeeping themselves without watching it happen on a slide.

**Setup line (say this):**
> *"Same 8-node tree. I'll point at a node — you give me left height, right height, the difference, and whether `ans` changes. Get all four before I confirm. If your height is right but your `ans` call is wrong, that's the interesting mistake — that's the whole point of today."*

**What students do:** Relay through nodes 8, 4, 5, 2 (or however far time allows) in the same leaf-up order as Slide Block B — one student per node, cold-called, stating the four numbers before the instructor confirms against the deck.

**How it surfaces:** The most common error is reporting the *height* correctly but skipping the diff/`ans` step, or reporting `ans` as if it resets per node. When this happens, point at the pseudocode line `if (diff > 1) ans = false` and have the student re-read it aloud, then re-answer.

**Debrief line (say this):**
> *"Height climbs by one every step up the tree, automatically. `ans` only ever moves in one direction — true to false — and only when a node actually fails. Two different jobs, same function call, same single pass."*

**Cut rule:** If running short, relay only nodes 4 and 2 — the two nodes where a diff of exactly 1 makes the "still balanced" point — and state the leaf nodes' results directly rather than relaying them.

---

## Exit Ticket (48–50 min)

> **Part 1:** In today's Example 1 tree, the root's own height difference was 1 (a pass). If you had *only* checked the root and stopped, what would you have wrongly concluded — and why is that wrong?
> **Answer:** You'd wrongly conclude "balanced," because the root's own children's heights differ by only 1. The tree actually fails two levels down, at node 2 (difference of 2) — checking only the root misses it entirely.
>
> **Part 2:** In one sentence — what does the optimal approach do that brute force does *not* do?
> **Answer:** It does not recompute `height()` separately, from scratch, for every node — it computes height and checks balance in the same single pass, using a shared flag.

Scan responses on the way out. If Part 1 answers show "the root already told us it's fine," that's the misconception to open Session 09 against — Session 09's diameter problem has the exact same trap (the answer often isn't decided at the root).

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A tree is balanced if the root's two subtrees look roughly equal in height | Natural to stop checking once the top level looks fine | Slide Block A / Activity 1's Example 1 walk — root passes (diff 1), node 2 fails |
| "Balanced" means visually symmetric — same shape on both sides | Informal, everyday use of the word "balanced" | Point at the balanced 8-node tree: node 2 has two children, node 3's side is shaped differently, yet it passes — the *only* test is the ≤1 height-difference rule, not shape |
| If the root passes, every node passes | Inverse of the row above — assuming the check is "monotonic" from the top down | Same Example 1 walk: root passes, node 2 (two levels down) fails anyway |
| Brute force and optimal check different things | Same arithmetic (`diff = abs(left - right)`, `if diff > 1`) appears in both sets of pseudocode | Side-by-side pseudocode comparison in Slide Block B — the *check* is identical; only *how many times `height()` runs* differs |

---

## Instructor Notes

- **Deck repeats itself heavily for animation, not new content.** Slides 89–133 re-run the *exact same two example trees* (Example 1 unbalanced, Example 2 balanced) a third time via the raw abs-diff method, purely as visual reinforcement. Click through these briskly — they need no new instructor commentary, the numbers are already familiar by then.
- **Space-complexity wording:** the deck's Optimal Approach complexity slide headlines "O(N)" for space but its own explanation describes recursion-stack depth (O(H), worst case O(N)) — teach the O(H) framing consistently with Session 07 and the Brute Force approach in this same session, and don't let the "O(N)" headline imply the optimal approach changed something about space usage. It didn't.
- **`height()` is now doing double duty.** Remind students it's the *exact* function from Session 07, unchanged — only the *caller* (`balanced()`) is new. This is a good moment to point back at Warm-Up Q3/Q4.
- **Example 1's exact left/right structure is not fully recoverable from the source deck text** — only the height/diff numbers at node 1 and node 2 are given directly. This lesson plan relies only on those numbers (not a specific left/right diagram) for that tree; the 8-node balanced tree used in both activities and Slide Block B has a fully unambiguous structure from the deck's own step-by-step narration and is safe to draw exactly as described above.
- Have the 8-node tree (root 1; children 2, 3; node 2's children 4, 5; node 4's left child 8; node 3's children 6, 7) already drawn on the board before class — both activities and Slide Block B reuse it.
