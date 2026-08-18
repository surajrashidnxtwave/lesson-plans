# Session 13 — Lowest Common Ancestor of a Binary Tree

**Duration** 60 min · **Topic** Binary Tree · **Prerequisite** Right view of Binary Tree (Session 12)
**Session type** Concept lecture

<!-- placement: inferred duration choice — 125 slides, and unlike Sessions 11–12 (one BFS-based algorithm each), this deck teaches TWO distinct approaches (a bruteforce root-to-node path comparison, and an optimal recursive DFS) with two full worked dry runs for the optimal approach on two different trees. The recursive "return value bubbles up through the call stack" reasoning is also a bigger conceptual jump than the queue mechanics of Sessions 11–12. 45 min does not leave room to dry-run both approaches properly, so this session runs 60. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Lowest Common Ancestor of a Binary Tree | https://docs.google.com/presentation/d/1PfK6oST_X-plBPAQkknErZZXRYBwoNHF826rRmBc7b8/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define the Lowest Common Ancestor (LCA) of two nodes `p` and `q` as the deepest node that is an ancestor of both — and state the rule that a node counts as its own ancestor/descendant. *(REMEMBERING)*
2. Explain why, in the recursive approach, a node whose left AND right recursive calls both return non-null must itself be the LCA. *(UNDERSTANDING)*
3. Contrast the bruteforce root-to-node path-comparison approach with the optimal recursive approach for finding an LCA. *(ANALYZING)* <!-- placement: inferred phrasing — both approaches are explicitly named and dry-run in the deck (slides 39–44 vs. 10–35, 45–121), the comparison itself is inferred structure -->
4. Trace the recursive LCA algorithm on a given tree to determine the LCA of two specified nodes, including cases where one node is an ancestor of the other. *(APPLYING)*
5. State the time and space complexity of the optimal recursive solution — O(N) time, O(H) space — and identify the worst case (a skewed tree, where H = N). *(ANALYZING)*

<!-- placement: inferred — phrased from the deck's own Summary slides 33–35, 123–124 -->

---

## Warm-Up Poll — Retrieval Practice on Right view of Binary Tree (Session 12) (0–6 min)

> Retrieval practice on the session immediately before this one. No new content — this is recall.

Say: *"Seven quick ones on yesterday's Right View problem before we move to today's topic."*

**Q1.** In Right View, do we keep the FIRST node dequeued at each level, or the LAST?
`A` First · `B` Last
→ *Read:* B — the last node dequeued at a level is the rightmost, and therefore visible.

**Q2.** True or False: the Right View algorithm uses a map keyed by column, the same way Top View does.
`A` True · `B` False
→ *Read:* False. No map, no horizontal distance — just a queue and level-by-level tracking.

**Q3.** What do we snapshot as `len = q.size()` BEFORE entering the inner loop, and why?
`A` The total number of nodes in the tree · `B` The number of nodes at the current level, so children pushed during this level don't blur the boundary · `C` A random buffer size · `D` The tree's height
→ *Read:* B — that snapshot is what lets the loop process exactly one level at a time.

**Q4.** For the tree root `8`; left `2`, right `6`; `2`'s left `3`; `6`'s left `1`, right `5`; `1`'s left `0` — what is the right view?
`A` 8, 2, 6 · `B` 8, 6, 5, 0 · `C` 8, 6, 1, 0 · `D` 8, 3, 5, 0
→ *Read:* B (`8, 6, 5, 0`) — straight from the deck's own dry-run tree.

**Q5. (MSQ)** Which of these are true about Right View's complexity? *(pick all that apply)*
`A` Time is O(N) — each node visited once · `B` Space is O(N) — the queue can hold up to N/2 nodes at the widest level · `C` Time is O(N log N) like Top View · `D` Space depends on tree height only
→ *Read:* A and B. C is the trap — that log factor belongs to Top View's map, not Right View.

**Q6.** A student claims: "Right View is just root → right → right → right... until null." Is this always correct?
`A` Yes, always · `B` No — it can miss a node whose rightmost position at some level hangs off a left branch deeper in the tree
→ *Read:* B — this was Session 12's Activity 3, and it comes back today in a different disguise.

**Q7.** Inside the level's loop, what happens to `temp` on every iteration, and when does it get pushed into `ans`?
`A` It accumulates a growing list; pushed every iteration · `B` It's overwritten by the current node's data each iteration; pushed to `ans` once, after the loop for that level ends
→ *Read:* B — `temp` is disposable, `ans` only wants the survivor.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Put the deck's definition tree on the board: some tree with two nodes marked `p` and `q`.

Say: *"Point at the lowest common ancestor of these two marked nodes. Just point — you have eyes, a brain, and three seconds."*

Let a few students point correctly at a glance. Then: *"Every one of you just did that instantly, by eye. Now the actual assignment: write a rule a computer can follow, that gets the same answer, on a tree with a thousand nodes you can't glance at."*

Tie forward: *"Today you'll see two such rules — one that's obvious but wasteful, and one that's clever and does it in a single pass."*

---

## Slide Block A (9–16 min) — DELIVER SLIDES AS-IS

Covers: Definition of LCA ("the node located deepest in the tree that serves as an ancestor to two nodes p and q; a node is considered its own descendant") → Problem Statement → Example 1 (same tree, `p=4, q=14` → LCA `8`) → Example 2 (root `20/10/30/5/15/25/35/3/7/null/null/22`, `p=3, q=10` → LCA `10`).

**Beats to emphasise**

- **"A node is considered its own descendant" is not a footnote — it's the edge case that trips up almost every first attempt.** Example 2's query (`p=3, q=10`) is deliberately chosen so that `q=10` is itself an ancestor of `p=3` — the correct answer is `10` itself, not some node further up.
- Example 1's tree structure (same tree reused throughout the deck) has `p=4, q=14` landing in different subtrees of the root, giving LCA `8` — contrast this immediately against Example 2, where one target sits *above* the other. <!-- placement: inferred — Example 1's exact node layout in the raw slide text is reconstructed by cross-checking against the "Bruteforce Approach" tree (slide 39) that reuses the same structure; confident but worth a second look against the live deck before class -->
- Don't over-explain yet — the "why" for both examples is what the rest of the session builds toward.

**Checkpoint (at 16 min)** — cold-call two students:
> *"If q is already an ancestor of p, what's the LCA?"*
> **Answer:** `q` itself — a node counts as its own ancestor/descendant, so there's no need to look any further up the tree.

---

## ⚡ Activity 1 — Predict-the-Output: The Self-Ancestor Case (16–21 min)

**Format:** Predict-the-Output · **Exposes:** whether the "a node is its own descendant" rule actually landed, before students see the mechanical algorithm that enforces it. This is the deck's own Example 2 (`p=3, q=10` → LCA `10`) — stated in the deck but never dry-run step by step, which makes it ideal for prediction rather than replaying an animation.

**Setup line (say this):**
> *"Tree on the board: root 20; left 10, right 30; 10's children are 5 and 15; 30's children are 25 and 35; 5's children are 3 and 7; 25's left child is 22. Find node 3 and node 10. Before I say anything else — what's their LCA? Write it down."*

**What students do:** Write a single answer silently (10 seconds), then show hands for their answer.

**How it surfaces:** If students propose a deeper node — e.g. `5` (the parent of `3`) — because they're hunting for "the first place the two paths overlap" without noticing `10` is already an ancestor of `3`: walk the ancestor chain out loud — `3`'s ancestors are `5, 10, 20`; `10`'s ancestors are `20` (and itself). The deepest node common to both lists is `10` itself, precisely because `10` is allowed to be its own ancestor.

**Debrief line:**
> *"The moment one of your two target nodes sits on the path to the other, that target node IS the answer. Don't go hunting past it — you'd only be climbing further from the deepest common point, not closer."*

**Cut rule:** If running late, skip the written prediction and jump straight to the show-of-hands, then move directly into the debrief line.

---

## Slide Block B (21–28 min) — DELIVER SLIDES AS-IS

Covers: Bruteforce Approach — tree root `8`; left `3`, right `10`; `3`'s children `1, 6`; `10`'s children `null, 14`; `6`'s children `4, 7`; `14`'s left `13`; query `p=1, q=7` → path to `p`: `8 → 3 → 1`; path to `q`: `8 → 3 → 6 → 7`; common nodes: `8, 3`; lowest common = `3`.

**Beats to emphasise**

- This approach is deliberately simple to build trust: find the full root-to-node path for `p`, find the full root-to-node path for `q`, walk both paths together and take the last node they still agree on.
- Say explicitly what it costs: two full traversals to build both paths, plus the paths themselves stored in memory — O(N) time, O(Height) extra space for the stored paths, same as the optimal approach's space bound, but with more up-front work and two arrays to maintain instead of one pass.
- This tree and this exact query (`p=1, q=7` → LCA `3`) is the same one the optimal recursive approach dry-runs next — flag that connection so students recognise it's the same problem, solved two ways.

**Checkpoint (at 28 min)** — show hands:
> *"What are the two costs of the bruteforce approach that the optimal approach is about to avoid?"*
> **Answer:** It needs two separate searches (one for `p`, one for `q`) instead of one combined pass, and it needs to explicitly store both root-to-node paths to compare them.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block C (33–45 min) — DELIVER SLIDES AS-IS

Covers: Optimal recursive Approach (`if root is null or root == p or root == q: return root`; recurse left and right; `if !left: return right`; `if !right: return left`; `return root`) → Dry Run on the bruteforce tree (`p=1, q=7`) → Pseudocode → Time Complexity O(N) → Space Complexity O(H) → C++/Python code.

**Beats to emphasise**

- Walk the dry run exactly as the deck stages it: descend to node `1` (matches `p`, return `1` up to its parent `3`); descend the other side to node `7` (matches `q`, return `7` up through `6`, since `6`'s left returned null); at node `3`, left result = `1`, right result = `7` — **both non-null** — so node `3` returns *itself* up to the root as the LCA.
- State the combine rule as a single sentence and put it on the board: *"If both sides find something, you're standing at the split point — that's the LCA. If only one side finds something, hand it straight up unchanged."*
- Space complexity is O(H), the recursion call stack — **not** automatically O(N)/O(log N). It only becomes O(N) in the worst case, a fully skewed tree where height equals the number of nodes.

**Checkpoint (at 45 min)** — cold-call:
> *"At node 3 in the dry run, left result is node 1, right result is node 7. Both non-null. What does node 3 return, and why?"*
> **Answer:** Node `3` returns *itself*. Both subtrees reported back a non-null find, meaning `p` and `q` are in different branches below node `3` — so node `3` is the point where their paths to the root diverge, which makes it the LCA.

---

## ⚡ Activity 2 — Dry-Run Relay: Be the Call Stack (45–53 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can execute the recursive "descend, then combine on the way back up" logic themselves, using the deck's own second worked example — root `20`; left `10`, right `30`; `10`'s children `5, 15`; `30`'s children `25, 35`; `5`'s children `3, 7`; `25`'s left child `22`; query `p=22, q=35`.

**Setup line (say this):**
> *"Ten volunteers, one per node: 20, 10, 30, 5, 15, 25, 35, 3, 7, 22. I'll call a node's name — that student says what THEY see (is it null? is it p=22? is it q=35? or neither?), calls their children if neither, and waits to hear back BEFORE announcing what they return to their own parent. No one gets to answer for their parent."*

**What students do:** Follow the deck's own Optimal Solution 2 sequence:
- `20` isn't `p` or `q` → calls left (`10`) and right (`30`).
- `10`'s branch (`10 → 5 → 3`, `5 → 7`) finds neither `22` nor `35` anywhere → `10` eventually returns `null` up to `20`.
- `30` isn't `p`/`q` → calls left (`25`).
- `25` isn't `p`/`q` → calls left (`22`) — `22` matches `p` → returns `22` up to `25`. `25`'s right is null → `25` returns `22` up to `30`.
- `30` calls right (`35`) — `35` matches `q` → returns `35` up to `30`.
- `30`: left result = `22`, right result = `35` — **both non-null** → `30` returns *itself* to `20`.
- `20`: left result = `null` (from `10`), right result = `30` → hand `30` straight up unchanged.
- Final LCA = `30`.

**How it surfaces:** If the student playing `30` announces the wrong return value (e.g. propagates `22` or `35` instead of declaring itself the LCA), stop and re-point at the combine rule from Slide Block C: both sides non-null means *this* node is the answer, not either child's value.

**Debrief line:**
> *"Every node in that relay did exactly one of three things: found a target, found nothing, or discovered it was sitting between the two targets. Only the last case is the LCA — and notice nobody needed to see the whole tree to know it, just their own two children's answers."*

**Cut rule:** If running short, skip relaying node `10`'s entire null-returning branch — state that it returns null and start the live relay from node `30` downward, since that's where the interesting logic lives.

---

## ⚡ Activity 3 — Spot the Bug: Broken Combine Logic (53–58 min)

**Format:** Spot the Bug · **Exposes:** reading recursive code for its general shape ("it recurses left and right, then does something") instead of tracing the exact base case and combine conditions. Every variant below is a one- or two-line change from the deck's own pseudocode (slide 28/119).

**Setup line (say this):**
> *"Three versions of the LCA function on screen. Only one is what's actually in the deck. For the other two, find the missing or wrong line, and tell me what tree/query would expose the bug — you don't need to run it, just point at the line."*

Put all three on screen:

```
// Version A
lca(root, p, q) {
  if (root == null) return root
  lf = lca(root.left, p, q)
  rt = lca(root.right, p, q)
  if (!lf) return rt
  if (!rt) return lf
  return root
}

// Version B
lca(root, p, q) {
  if (root == null || root == p || root == q) return root
  lf = lca(root.left, p, q)
  rt = lca(root.right, p, q)
  if (!lf) return lf
  if (!rt) return lf
  return root
}

// Version C — the deck's actual version
lca(root, p, q) {
  if (root == null || root == p || root == q) return root
  lf = lca(root.left, p, q)
  rt = lca(root.right, p, q)
  if (!lf) return rt
  if (!rt) return lf
  return root
}
```

**What students do:** 90 seconds, then hands up per version.

**Answers**

| Version | Bug | What breaks |
|---|---|---|
| A | Base case drops `root == p \|\| root == q` | The recursion never recognises it has actually found `p` or `q` — it just keeps descending past them looking for `null`, so it can never correctly report "found `p` here" up to a parent. |
| B | `if (!lf) return lf` should be `return rt` | When the left side comes back empty, this returns the empty left result instead of handing up whatever the right side found — so a real match on the right side gets silently thrown away. |
| C | None — this is the deck's real algorithm | Matches the dry run from Slide Block C exactly. |

**How it surfaces:** If students accept Version A because "it still checks `root == null`," push: *"Walk it on our dry-run tree — root 8, target p=1. When does this version ever return node 1 itself?"* (Answer: never — it only returns non-null when a subtree is entirely empty, so a genuine match is never signalled upward.)

**Debrief line:**
> *"Both bugs are one wrong or missing line, and both are invisible unless you trace them against a query where they actually matter. That's exactly why we dry-run recursive code line by line instead of eyeballing it."*

**Cut rule:** If running late, drop Version A and only compare B against the real version C — B's bug is subtler and more instructive, and A vs C is a smaller, faster contrast to state verbally if needed.

---

## Exit Ticket (58–60 min)

> On paper or in chat: *"Using today's Optimal Solution 2 tree — root 20; left 10, right 30; 10's children 5, 15; 30's children 25, 35; 5's children 3, 7; 25's left child 22 — what is the LCA of p=5 and q=22?"*
> **Answer:** `20`. Path to `5`: `20 → 10 → 5`. Path to `22`: `20 → 30 → 25 → 22`. The only node common to both paths is the root, `20` — so that's the LCA.

Scan responses on the way out — this query has neither node as an ancestor of the other and the split happens at the root, so it's a clean check of whether students can apply the combine rule to a tree they've now seen twice, on a query that wasn't dry-run for them in class.

This closes the Binary Tree topic (Sessions 1–13). A short recap before dismissing is worth the 60 seconds: Top View (Session 11) read the tree by column with a map, Right View (Session 12) read it by level with a queue, and LCA (Session 13) read it by recursive descent-and-combine — three different lenses on the same tree structure.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The LCA must be a node distinct from both `p` and `q` | Feels wrong for one target to "be" the ancestor of the other | Activity 1 — Example 2's `p=3, q=10` case, where the correct answer is `10` itself |
| The base case `root == p \|\| root == q` means "we found the final answer" | It looks like a success condition | Slide Block C — the real decision happens at the ancestor where BOTH sides return non-null, not at the match itself |
| Bruteforce and optimal give different answers on the same input | They look like unrelated methods | Slide Blocks B and C dry-run the identical tree and query (`p=1, q=7` → LCA `3`) with both approaches |
| Space complexity is always O(N) for the recursive approach | "Recursion = expensive" is a common blanket rule | State O(H) explicitly, then contrast a balanced tree (`H ≈ log N`) against the worst-case skewed tree (`H = N`) |
| If `lf` is non-null, that's automatically the LCA | Feels like "found something, done" | Activity 3, Version B — you must also check whether `rt` is non-null; only when exactly one side is null do you propagate the other, and both non-null means the *current* node is the LCA |

---

## Instructor Notes

- **Why this session is 60 minutes, not the reference default of 45:** the deck teaches two genuinely different algorithms (bruteforce path comparison, optimal recursion) with full worked dry runs, plus a *second* full optimal dry run on a different tree. Compressing that into 45 minutes leaves no time to actually run both activities that make the recursion concrete — cut Slide Block content before cutting either dry-run activity.
- **This is the last session of the Binary Tree batch.** Close with the one-line recap in the Exit Ticket section (Top View → Right View → LCA as three lenses on the same structure) rather than pointing to a next session — there isn't one in this batch.
- **The deck's "Optimal Solution" (slides 45–77) and the earlier recursive "Approach" + "Dry Run" (slides 10–27) are the same algorithm on the same tree and the same query (`p=1, q=7`)** — just re-animated in two different visual styles (a pre-order-traversal narrative vs. a recursive-call narrative). Don't present them as two different algorithms; pick one animation pass to actually click through live in Slide Block C, and mention the other exists in the deck as reinforcement for students to revisit later.
- **Example 1's exact tree layout (slides 5–7, `p=4, q=14` → LCA `8`) is reconstructed by inference** — the raw slide text for this specific tree is more garbled than Example 2's clean `root = ...` list. It's cross-checked and consistent with the tree reused later in the "Bruteforce Approach" section (slide 39), so confidence is reasonably high, but glance at the live deck before presenting it as fact.
- **Slides 123–124 (Summary + "Optimal solution Steps")** give the deck's own compressed recap ("check for node p, check for node q, perform pre-order traversal") — useful as a closing slide, but this phrasing simplifies the real combine logic into something that sounds sequential. Don't let students walk away thinking the algorithm checks for `p`, then separately checks for `q`, in two passes — it's one pass that checks for either at every node.
