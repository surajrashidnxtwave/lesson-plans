# Session 20 — Merge Two BSTs

**Duration** 50 min · **Topic** Binary Search Tree · **Prerequisite** Predecessor and Successor in BST
**Session type** Concept lecture

<!-- placement: inferred — 50 min instead of the 45-min default because the two-stack dry run is the longest, most step-dense walkthrough in the entire BST topic (roughly 25 slides tracing individual pushes/pops), and needs real board time to land. -->

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Merge Two BSTs | https://docs.google.com/presentation/d/1cHTFivGiZX_ws3OimObzkzH5v_rUK3yctx0xxamYLwI/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given two BSTs, return a single array containing every element from both, in sorted order. *(REMEMBERING)*
2. Explain why the merge technique pushes each root's left spine onto a stack first, and why that mirrors the first steps of an iterative in-order traversal. *(UNDERSTANDING)*
3. Trace the merge dry run: repeatedly comparing the two stacks' top values, popping the smaller, and — if the popped node has a right child — pushing that child's own left spine onto the same stack. *(APPLYING)*
4. Compare this technique to the merge step of merge sort — combining two already-sorted sequences by repeatedly comparing their fronts. *(ANALYZING)* <!-- placement: inferred analogy, not stated explicitly in the deck -->
5. State the time complexity O(N1 + N2) and space complexity O(H1 + H2), and explain why space depends on height, not node count. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Predecessor and Successor in BST (0–6 min)

Say: *"Six on yesterday's predecessor/successor before today's problem, which reuses the same pointer-walk instinct on two trees at once."*

**Q1.** The predecessor of a node is defined as…
`A` Its parent · `B` The node immediately before it in an in-order traversal · `C` Its left child · `D` The smallest value in the tree
→ *Read:* B.

**Q2.** In the optimal predecessor walk, what happens when `current->data < target`?
`A` Record it as a candidate and move right · `B` Record it as a candidate and move left · `C` Discard it and move right · `D` Stop immediately
→ *Read:* A.

**Q3. (True/False)** The successor walk's rule is the exact mirror of the predecessor walk's rule.
→ *Read:* True — move left/record on `current > target` instead of right/record on `current < target`.

**Q4.** What is the space complexity of the optimal predecessor/successor approach?
`A` O(N) · `B` O(H) · `C` O(1) · `D` O(N log N)
→ *Read:* C — just a couple of pointers, no array, no recursion stack (it's an iterative walk).

**Q5.** In the brute-force predecessor/successor approach, what is the sorted array built from?
`A` A pre-order traversal · `B` An in-order traversal · `C` A post-order traversal · `D` The tree's raw node order
→ *Read:* B.

**Q6.** If a target is the FIRST value in the in-order sequence, its predecessor is…
`A` The root · `B` NULL · `C` Itself · `D` The largest value in the tree
→ *Read:* B.

**Running it** — poll tool, ~30–40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Two sorted piles of cards, and you need one sorted pile. You don't shuffle everything together and re-sort from scratch — you look at the two top cards, take the smaller one, and repeat. That's the merge step from merge sort. Today's problem is exactly that, except your 'top card' isn't the front of an array. It's whatever a stack of tree pointers says is the smallest value not yet used, from each of two BSTs."*

---

## Slide Block A (9–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Problem statement + both worked examples → Approach (push each root's left spine onto its own stack; repeatedly compare tops, pop the smaller, push the popped node's right child's left spine onto that same stack) → full dry run of Example 1.

**Beats to emphasise**

- State the approach in the deck's own three steps: *"(1) Push every root's left descendants onto its own stack. (2) Compare the two stacks' top values; pop whichever is smaller into the result. (3) If what you popped has a right child, push THAT child's left descendants onto the same stack, then repeat from step 2 until both stacks are empty."*
- Connect step (1) explicitly to prior sessions: *"Pushing a node then all its left children, one at a time, is literally how you'd START an iterative in-order traversal. Each stack, on its own, is just doing an in-order traversal of its own tree — one step at a time, on demand."*
- Walk the dry run at a **representative pace**, not every single slide: show the initial stacks being loaded (stack 1 from BST 1's root, stack 2 from BST 2's root, each following its left spine down), then walk 6–8 compare-pop-push cycles live exactly as the deck sequences them, then jump to the final merged result and confirm it matches the deck's stated output.
- Flag the one detail that's easy to skip: **you push the popped node's RIGHT child's left spine — not just the right child itself.** This is the exact rule Activity 1 tests.

**Checkpoint (at 24 min)** — cold-call:
> *"If the value you just popped has NO right child, what happens to that stack on the next comparison?"*
> **Answer:** Nothing gets pushed — the stack's next top is simply whatever was already sitting underneath the popped value (an ancestor from earlier in that same left-spine push).

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: Compare, Pop, Push (24–32 min)

**Format:** Dry-Run Relay · **Exposes:** whether students remember to push the popped node's right-subtree left-spine (not just note the right child and move on), which is the step almost everyone forgets on a first pass.

**Setup line (say this):**
> *"I'm going to read out comparisons exactly the way the deck's dry run does them, one at a time. Before I tell you which stack wins, YOU tell me: which value pops, and — this is the part people skip — does anything new get pushed as a result?"*

Reuse the deck's own sequence from Example 1 (BST 1 rooted at 10 with left-spine values including 5, 2, 1; BST 2 rooted at 12 with left-spine values including 6, 4). Read out the comparisons in the deck's own order: *"1 vs. 4 → 1 is smaller, pop 1. 2 vs. 4 → 2 is smaller, pop 2, and 2 has a right child (3), so push 3's left spine. 3 vs. 4 → 3 is smaller, pop 3. 5 vs. 4 → 4 is smaller, pop 4. 5 vs. 6 → 5 is smaller, pop 5, and 5 has a right child (7), so push 7's left spine."* Continue through as many comparisons as time allows, tracking the growing result array `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...` against the deck's own running list.

**How it surfaces:** If a student pops a value but doesn't mention pushing its right subtree's left spine (when it has one), stop: *"You can't just walk away from that pop. If what you popped had a right child, that child's whole left spine has to go on the stack now, or you'll silently skip every value in between."*

**Debrief line:**
> *"Every single pop is one step of an in-order traversal on ONE of the two trees. The merge never builds two separate sorted lists first — it runs both in-order traversals AND the sorted merge in the exact same pass."*

**Cut rule:** Stop after 6–8 comparisons instead of tracing the full ~18-step dry run; state the deck's final merged array directly rather than tracing every remaining step.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (37–45 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide-block boundary -->
Covers: Pseudocode (`pushLeft` helper + `mergeBSTs` main loop) → code (C++/Python) → complexity analysis (O(N1 + N2) time, O(H1 + H2) space).

**Beats to emphasise**

- Walk the pseudocode's two pieces separately: `pushLeft(root, s)` just walks left, pushing every node it passes, until it hits `null` — this is the "load a stack with a left spine" operation used both at the very start AND every time a right child needs to be added mid-merge.
- The main loop's condition — `if s1 is empty OR (s2 is not empty AND s2's top < s1's top)` — is just a careful way of saying "always compare both tops, UNLESS one stack has already run out." Walk this condition slowly; it's denser to read than to execute.
- Complexity: **time is O(N1 + N2)** because every node from both trees is pushed and popped exactly once. **Space is O(H1 + H2)**, NOT O(N1 + N2) — each stack only ever holds one root-to-current path (a left spine) at any moment, bounded by that tree's height, never the whole tree.

**Checkpoint (at 45 min)** — show hands:
> *"Why is the space complexity O(H1 + H2) and not O(N1 + N2), when every node does eventually get pushed at some point during the algorithm?"*
> **Answer:** Nodes get pushed and popped continuously throughout the run — the stack's *maximum simultaneous size* at any instant is just one left-spine's worth of nodes (bounded by height), not every node the algorithm will ever touch across the whole run.

---

## ⚡ Activity 2 — Real-World Callout: Two Sorted Leaderboards (45–49 min)

**Format:** Real-World Callout · **Exposes:** whether the two-stack technique feels like an arbitrary trick or a recognisable general strategy once it's re-stated outside tree vocabulary.

**Setup line (say this):**
> *"Two class leaderboards, both already sorted by score, and you need one combined leaderboard. No re-sorting allowed — you're too lazy for that. What's the laziest CORRECT way to build the merged list, given both lists are already sorted?"*

**What students do:** 30 seconds, pairs, then a few call-outs.

**How it surfaces:** Push toward the answer if students suggest "just concatenate and sort again": *"That works, but it throws away the fact that both lists were already sorted. Use that fact instead."* Land on: "look at both fronts, take the smaller, repeat."

**Debrief line:**
> *"That's the merge step from merge sort, and it's exactly what today's two stacks are doing on BSTs. Each stack isn't 'a leftover pile of tree nodes' — it's standing in for 'the next smallest value not yet used from this tree,' updated on demand, one pop at a time."*

**Cut rule:** Skip entirely if short on time — it's reinforcement of an idea already delivered, not new content.

---

## Exit Ticket (49–50 min)

> On paper or in chat: *"In one sentence: why does pushing a popped node's right child's LEFT SPINE (rather than just the right child itself) keep the stack correctly ordered for merging?"*
> **Answer:** Because the smallest not-yet-used value in that right subtree is its leftmost node, not the subtree's root — so the stack's new top has to be that leftmost node for the next comparison to stay correct.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| You need to build both trees' full sorted arrays first, then merge them | Feels like the "obvious" two-step version of the problem | Naming the O(H1+H2) space complexity — the whole point is avoiding two full O(N) arrays |
| Popping a node and pushing just its right CHILD (not the right child's left spine) is enough | "Push the right child" sounds complete on first read of the approach | Activity 1 — the exact moment most relay attempts silently skip values |
| You alternate popping from stack 1 and stack 2 in a fixed order | Feels natural to "take turns" between the two trees | Re-reading the comparison condition — you always compare BOTH tops and pop the smaller, regardless of which stack it came from |
| Time complexity is O(N1 × N2) | "Merging two things" sounds like it should involve comparing every pair | The complexity breakdown — every node is pushed and popped exactly once, so costs add (O(N1+N2)), they don't multiply |

---

## Instructor Notes

- **This session has no separate "brute force" section in the deck**, unlike Sessions 17, 18, and 19 — the two-stack technique is presented as the only approach. If a student asks "what's the naive way," the honest answer is "build both sorted arrays via in-order traversal, then do a standard two-pointer array merge" — mention it as the obvious O(N1+N2) time / O(N1+N2) space baseline the taught technique improves on for space, but don't build it out as a full section since the deck doesn't. <!-- placement: inferred — flagging the asymmetry with the rest of the topic's session structure -->
- **Pacing risk:** the full dry run is the longest in the whole BST topic — resist the urge to trace every single comparison live. Six to eight comparisons, done carefully with the class following along, teaches the pattern; the remaining dozen are repetition of the same rule.
- **Exact stack contents at each step are reconstructed from the dry-run narration text**, not from directly legible stack diagrams in the raw slide extraction — cross-check against the live deck before presenting specific intermediate stack states as fact. The final merged output and the overall comparison SEQUENCE (which value pops when) are stated unambiguously in the deck and can be presented with full confidence. <!-- placement: inferred -->
- **Topic close-out note:** this is the last of the seven BST sessions in this block. If time allows at the very end (not part of the 50-minute budget above), a 2-minute verbal recap tying together Search → Insert → Delete → Kth Smallest → Validate → Predecessor/Successor → Merge as "seven ways of exploiting the same one-line ordering rule from Session 14" is a strong close, though it is not part of any single session's deck content. <!-- placement: inferred, optional -->
