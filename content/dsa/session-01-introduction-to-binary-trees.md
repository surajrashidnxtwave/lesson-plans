# Session 01 — Introduction to Binary Trees

**Duration** 75 min · **Topic** Introduction to Binary Trees · **Prerequisite** Arrays & recursion basics (assumed, not verified against this deck) <!-- placement: inferred --> · **Session type** Concept lecture

<!-- placement: inferred — duration set to 75 min, not the 45-min default. Rationale: this deck carries 9 terminology definitions, 4 properties/formulas, 6 full tree-type definitions each with a worked "which one is X" comparison, a real-life-examples section, AND a second full pass of worked balanced-tree height calculations (slides 47-74) that the reference session's Python-intro deck simply doesn't have an equivalent of. 45 min is not enough to deliver this without cutting either the type comparisons or the balanced-tree calculations, both of which are load-bearing for later sessions (traversals, BSTs, height/depth problems). -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Introduction to Binary Trees | https://docs.google.com/presentation/d/1IlPlKtEUeak8Yx-68B1NBz1kIWT2bUFXVQyEW18wqGI/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a binary tree and its core terminology — node, parent, root, child, leaf node, internal node, subtree, height, ancestor. *(REMEMBERING)*
2. Explain the two conventions for measuring height (counting nodes on the longest root-to-leaf path vs. counting edges) and state which one a given problem is using. *(UNDERSTANDING)* <!-- placement: inferred from deck's own NOTE on Slide 14 and the conflicting height values on Slides 70-71 -->
3. State the structural rule that distinguishes each of the six binary tree types: Full, Balanced, Complete, Perfect, Degenerate, Skewed. *(UNDERSTANDING)*
4. Classify a given binary tree diagram as one of the six types (or none of them) by applying the definitions and, for Balanced trees, by computing left/right subtree height differences node by node. *(ANALYZING)*
5. Connect the hierarchical-data-structure model to real-world hierarchies (file systems, family trees) and recognise when such a hierarchy is *not* a strict binary tree. *(UNDERSTANDING)* <!-- placement: inferred from Slide 45-46 summary + Slides 41-44 -->

---

## Warm-Up Poll — Diagnostic (0–7 min)

> **This is the true first session of the entire DSA course.** There is no previous session to recall, so — exactly as with any day-one session — the poll is a *diagnostic*, not retrieval practice. No wrong answers. Purpose is to calibrate pace and to establish, on minute one, that this classroom expects everyone to answer.

Say: *"Seven quick questions before we touch a single tree diagram. Nobody is graded, nobody is named. I need to know who I'm teaching."*

**Q1.** Have you studied any data structure before (arrays, linked lists, stacks, queues)?
`A` Never heard the term · `B` Heard of arrays only · `C` A few, in theory · `D` Yes, comfortably, including some coding
→ *Read:* If A+B > 60%, slow down on "hierarchical data structure" in Slide Block A — don't assume they have a mental model of "structure" beyond arrays.

**Q2.** Are you comfortable with recursion (a function that calls itself)?
`A` Never heard of it · `B` Heard of it, don't fully get it · `C` Can trace through simple examples · `D` Yes, comfortably
→ *Read:* This isn't tested today, but it's the backbone of every traversal in Session 02. If C+D < 40%, flag it now — you may need a 2-minute recursion refresher before Session 02's warm-up.

**Q3.** When you hear the word "tree" in a computing context, what comes to mind first?
`A` A plant / nature · `B` A family tree · `C` A folder/file structure on my computer · `D` No idea
→ *Read:* B and C are both good instincts — the Real-Life Examples block later validates whichever one they picked. Don't correct A yet; let the Hook do it.

**Q4.** Guess: in a "binary" tree, how many children can one node have, at most?
`A` 1 · `B` 2 · `C` Unlimited · `D` Not sure
→ *Read:* B is where the whole session lands. If most pick C, that's your hook — the file-system example is about to look very "unlimited," and today's whole point is the strict 2-child rule.

**Q5.** True or false (guess): every hierarchy you can draw as a tree is automatically a "binary" tree.
`A` True · `B` False · `C` Not sure
→ *Read:* Note the split. You'll revisit this exact question in the Hook and again in Slide Block B's real-life-examples beat.

**Q6.** Which of these do you most want out of this course? *(MSQ — pick up to 2)*
`A` A job / placement · `B` Build my own projects · `C` Clear college coursework · `D` Curiosity

**Q7.** How do you prefer to learn a new structure like this?
`A` Watch a diagram first, then try · `B` Try labelling it myself first, ask later · `C` Read the definition first · `D` Work with a partner
→ *Read:* If B+D is high, lean harder on Activities 1-3 and shorten your talk-through of definitions.

**Running it** — poll tool, ~45 s per question, project the distribution after each. Never name individuals. Total 7 min including your reads.

---

## Hook (7–12 min)

Put both of these on the board side by side, nothing else:

**Diagram A** — a file system tree (from the deck): `Root directory of C Drive` branching into `Documents and Settings`, `Program files`, `Desktop`, `Favorites`, `Adobe`, `Microsoft office` — six children hanging off one root.

**Diagram B** — the deck's own binary tree definition diagram (Slide 4): every node has *at most two* children, left and right.

Ask: *"Both of these are 'trees' — hierarchies with a root and branches. Which one is a valid Binary Tree, by definition?"*

Let the disagreement happen (tie back to **Q5** of the poll — some said "false," some said "true"). Then: *"Diagram A is a tree. It is not a binary tree — that root has six children, and a binary tree allows at most two. Every rule in the next hour comes from that one restriction: at most two children, called left and right. That's the whole deal today."*

---

## Slide Block A (12–27 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range — Slides 4-17: Introduction, the 9 Key Terminologies (Node, Parent, Root, Child, Leaf Node, Internal Node, Subtree, Height, Ancestors), and Properties of Binary Trees -->

**Beats to emphasise**

- **Node = value + two pointers.** Say it exactly like that, every time a new term is introduced — it's the one sentence that makes every later term (parent, child, leaf) fall out logically instead of needing separate memorisation.
- **Root has no parent — it's a position, not a shape guarantee.** Students will later try to assume the root always has two children; kill that assumption now (Slide 9).
- **Parent vs. Ancestor is the sharpest distinction in this block.** Parent = one level up, direct. Ancestor = *every* node on the path from root down to this one, excluding the node itself (Slide 15). Say both definitions back-to-back, on the same diagram, so the contrast is visible in one glance.
- **Height has two competing conventions and the deck flags this itself** (Slide 14 NOTE): count *nodes* along the longest root-to-leaf path, or count *edges*. Don't gloss over this — pick one convention explicitly for this classroom's exams/coding practice and say which one out loud, because it changes every height-based answer by exactly 1.
- **Properties/formulas** (Slides 16-17): at most 2 children per node; at most 2^i nodes at level *i* (root = level 0); a tree of height *h* has at most 2^(h+1) − 1 nodes <!-- placement: inferred — the exponent characters were lost in the deck-text extraction on Slide 17; verify the exact exponent against the live slide before writing it on the board -->; minimum possible height for *n* nodes is ⌈log₂(n+1)⌉. Don't derive these — state them, show one worked plug-in each, move on.

**Checkpoint (at 27 min)** — cold-call two students:
> *"Node 8 in a tree is the parent of node 15, and node 2 is the parent of node 8. Is node 2 a parent of node 15, an ancestor of node 15, or both?"*
> **Answer:** Ancestor, not parent — node 2 is two levels above node 15, and "parent" only ever means the one node directly above.

---

## ⚡ Activity 1 — Predict-the-Label (27–34 min)

**Format:** Predict-the-Output (adapted: students predict a terminology *label*, not a numeric output) · **Exposes:** Parent-vs-Ancestor confusion, Leaf-vs-Internal confusion, and the "root has no parent" special case — the three traps set up in Slide Block A.

**Setup line (say this):**
> *"Fresh tree, nodes numbered 1 through 8, same as the recap diagrams later in this deck. I'll ask one term at a time. Everyone commits an answer — thumbs to the term's letter — before I reveal."*

Use the deck's own recap facts, which are stated outright rather than requiring you to reverse-engineer the diagram's exact edges <!-- placement: inferred — the recap tree's full edge layout is not recoverable from the text extraction alone; run the questions below only, in the order shown on the live slides, rather than inventing extra node relationships -->:

1. *"Node 8 is the child of node 7. True or false — is node 8 also an ancestor of node 7?"* → **False.** It's the reverse: node 7 is an ancestor of node 8. (Slide 63)
2. *"The root of this tree — does it have a parent? Yes or no?"* → **No, never.** (Slides 64-65)
3. *"The ancestors of node 4 are which nodes?"* → **Nodes 1 and 2** — every node on the path from root to node 4, excluding node 4 itself. (Slide 72)
4. *"This tree has 4 levels. Using the 'count nodes on the path' convention, what's its height? Using the 'count edges' convention?"* → **4** (nodes) or **3** (edges) — both are "correct," which is exactly the point. (Slides 70-71 give both values for the same tree.)

**What students do:** Thumbs-vote per question, then one cold-call to justify the answer in a full sentence before you confirm.

**How to handle wrong answers:** If students flip parent/ancestor on Q1, redraw the one-sentence rule from the checkpoint ("ancestor = everyone above, parent = one step above") right on top of this tree. If Q4 produces only one answer confidently, that's a red flag — it means the ambiguity didn't land in Slide Block A; restate which convention *this classroom* uses and move on.

**Debrief line:**
> *"Every one of these came straight from a definition you just heard — no trick questions. If you hesitated, it's because two of these terms sound alike. They aren't the same. Parent is one step. Ancestor is every step."*

**Cut rule:** If running short, drop Q1 and Q4, keep Q2 and Q3 — they carry the two ideas (root has no parent; ancestor ≠ node itself) most likely to resurface in Session 02+.

---

## Classroom Quiz (34–39 min)

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (39–54 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range — Slides 18-46: the six binary tree types (Full, Balanced, Complete, Perfect, Degenerate, Skewed), each with a "which one is X" comparison, plus Real-Life Examples (File Systems, Family Hierarchy) and the deck's own summary slides -->

**Beats to emphasise**

- Deliver each type as **rule, then the deck's own two-example comparison, then the deck's own stated reason the "wrong" example fails.** Don't paraphrase the reason — the deck states it precisely each time, e.g.:
  - **Full:** every node has 0 or 2 children. The non-example fails because "node 20 has 1 child" (Slide 22).
  - **Balanced:** left/right subtree heights differ by at most 1 at every node. The non-example fails because the root's subtree-depth difference is 2 (Slide 26).
  - **Complete:** every level filled except possibly the last, and the last level fills left-to-right with no gaps. The deck shows two distinct ways this fails: "the second-last level is not completely filled" (Slide 29) and "last-level nodes are not filled from left to right" (Slide 30) — these are two *different* failure modes, not the same one twice.
  - **Perfect:** every level completely filled, all leaves at the same level. Fails because "all leaf nodes are not at the same level" (Slide 34).
  - **Degenerate:** every node has exactly one child except the leaf — looks like a linked list. Fails when any node (including the root) has two children (Slide 37). <!-- placement: inferred — the deck text for which of "Example 1" / "Example 2" is the degenerate one is garbled in extraction (Slides 36-37 give conflicting order); verify against the live slide before stating which example is which. The discriminating rule itself is not in doubt. -->
  - **Skewed:** a special case of degenerate, split into left-skewed (only left children) and right-skewed (only right children) — this one isn't a "which is/isn't," both examples shown are valid, just different subtypes (Slide 39).
- On **Real-Life Examples** (Slides 41-44): call back to the Hook immediately. File systems and org-chart-style family hierarchies are trees, but only count as *binary* trees if every node is capped at two children — most real file systems aren't.
- The deck's own summary (Slides 45-46) is your closing line for this block, verbatim: *"Height of a Binary Tree is the longest path from root to leaf node. Full, Complete, Perfect, Balanced, Degenerate and Skewed trees exhibit different structural characteristics."*

**Checkpoint (at 54 min)** — show of hands:
> *"A tree has every level completely full, except the last, and the last level's nodes are pushed as far left as possible with no gaps. Complete, or Perfect?"*
> **Answer:** Complete. Perfect requires the LAST level to also be completely full — Complete only requires every level *except* the last to be full.

---

## ⚡ Activity 2 — "Which One Is It?" (54–61 min)

**Format:** Predict-the-Output (applied to classification instead of a numeric result) · **Exposes:** the Complete/Perfect/Full three-way confusion and the tendency to eyeball a tree shape instead of checking the specific rule.

**Setup line (say this):**
> *"I'm going to put up the deck's own 'which one' comparison for each of the six types, one at a time. Before I reveal the answer, vote: Example 1, Example 2, both, or neither. Then tell me the ONE-SENTENCE reason — not just the label."*

Run through, in this order, using the deck's own comparison slides: Full (Slides 20-22) → Balanced (Slides 23-26) → Complete (Slides 27-30) → Perfect (Slides 31-34) → Degenerate (Slides 35-37) → Skewed (Slide 39, both valid — the "gotcha" question).

**What students do:** Vote by show of hands per type, then one student gives the one-sentence rule before you reveal the deck's stated reason.

**How to handle wrong answers:** The single most common miss is Complete vs. Perfect — if a class calls a Complete tree "Perfect," don't just correct the label, make them re-check the specific clause that fails ("is the LAST level completely full, yes or no?"). For Skewed, the trap is expecting a "wrong" example — flag out loud that both are valid, just different subtypes, before voting so nobody wastes time hunting for a bug that isn't there.

**Debrief line:**
> *"Six types, six different single-clause rules. Every one of today's 'gotcha' answers came down to checking one specific clause, not eyeballing the overall shape. That's the skill — not memorising six pictures, memorising six one-line tests."*

**Cut rule:** If running short, do Full, Balanced, and Complete only (the three most likely to reappear in coding-round questions) and skip Perfect, Degenerate, and Skewed — state their one-line rules verbally instead of running the full vote.

---

## ⚡ Activity 3 — "Is This Balanced?" Dry-Run Relay (61–70 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** the misconception that "balanced" means "equal number of nodes on each side" rather than "height difference ≤ 1 at every node."

**Setup line (say this):**
> *"I'm giving you a tree, node by node. Starting from the leaves, going up — at every node, you tell me the height of its left subtree, the height of its right subtree, and the difference. We don't get a verdict on the whole tree until we've done this at every single node, including the root."*

Use the deck's own fully-worked example (Slides 51-57): a tree built from values 5, 15, 8, 12, 20, 10, 3, 4, 7. Go bottom-up exactly as the deck does, computing the height-difference at each node, ending at the root with left subtree height 3, right subtree height 2, difference |3−2| = 1 → **Balanced**.

Then contrast immediately with the deck's second worked tree (values 5, 15, 7, 10, 3, 4): left subtree height 3, right subtree height 0, difference |3−0| = 2 → **Not Balanced**.

**What students do:** Go around the room, one node per student, calling out that node's left-height/right-height/difference before you reveal the deck's own number for that node. Keep a running tally on the board exactly like the deck's slide build-up.

**How to handle wrong answers:** If a student answers based on counting total nodes on each side rather than height, stop and redo that one node together — this is the exact misconception the activity targets, don't let it slide past to preserve pace.

**Debrief line:**
> *"Balanced never means equal head-count on both sides. It means the taller side is never more than one level taller than the shorter side — and you only find that out by checking height at every single node, not just eyeballing the root."*

**Cut rule:** If running short, do only the first tree (the Balanced one, Slides 51-54) live as a full relay, then simply show the second tree's (Not Balanced) final numbers from Slide 57 as a fast contrast rather than re-running the relay.

---

## Exit Ticket (70–75 min)

**Exit ticket** — on paper or in chat before anyone leaves:

> Draw or describe a tree with exactly 3 nodes that is a Full Binary Tree AND a Perfect Binary Tree AND a Balanced Binary Tree, all at once. Then write one sentence: is it possible for a tree to be Complete but not Full? Why or why not?
> **Answers:** A root with exactly two children (0 or 2 children at every node = Full; both levels completely filled = Perfect; height difference 0 = Balanced) satisfies all three at 3 nodes. Yes, Complete-but-not-Full is possible — e.g. a root with a left child only and no right child is Complete (last level filled left-to-right with no gaps) but not Full (that node has exactly 1 child).

Scan responses on the way out. Confusion on the second half of the ticket is the signal to open Session 02 with a 2-minute recap of Complete vs. Full before moving into traversals.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| "Height" has one fixed numeric value for any given tree | Most other measurements in maths are unambiguous | Showing Slides 70-71's own two different height values (4 vs. 3) for the identical tree, and stating which convention this classroom uses |
| Ancestor and Parent mean the same thing | Both describe "someone above me in the hierarchy" | Running Activity 1's Q1/Q3 back-to-back on the same diagram |
| Complete, Perfect, and Full binary trees are basically the same thing | The names all describe "a tree that's nicely filled in" and the shapes look similar at a glance | Activity 2's one-sentence-rule requirement — forcing the specific clause, not the label, to be spoken aloud |
| "Balanced" means equal number of nodes on the left and right | The everyday meaning of "balanced" is about equal weight/count | Activity 3's node-by-node height relay, which never once counts total nodes |
| Any hierarchy diagram (file systems, org charts) is automatically a "binary" tree | Both are commonly drawn as branching diagrams and casually called "trees" | The Hook's file-system vs. binary-tree-definition side-by-side, reinforced in Slide Block B's Real-Life Examples beat |

---

## Instructor Notes

- **This session runs 75 min, not the course default of 45.** If your slot is hard-capped at 45-60 min, cut Activity 3 first (move the Balanced-tree dry-run to a follow-up or homework walkthrough) before cutting any of the six type definitions in Slide Block B — the six-type distinction is the content most likely to reappear across the rest of the course and in coding-round questions.
- **Resolve the height convention (nodes vs. edges) explicitly and write it on the board for the rest of the course.** This is a real, deck-acknowledged ambiguity (Slide 14's own NOTE), not an instructor error — but if you don't pick one, students will carry two silently-conflicting definitions into every future height/depth problem.
- **The deck has a second, denser pass of worked material starting around Slide 47** (an extra Perfect-tree comparison, the full Balanced-tree height-difference walkthroughs, and a full terminology recap on a fresh 1-8 tree). This lesson plan redistributes that material into Activities 1-3 rather than delivering it as additional slides verbatim — showing all of Slides 47-74 as more lecture content on top of Slides 4-46 would roughly double the session with substantial repetition. <!-- placement: inferred restructuring — confirm this matches how the deck is actually meant to be delivered before running it cold. -->
- **Verify the Degenerate-tree "which example is which" against the live slide** (Slides 36-37) before Activity 2 — the raw text extraction gives a genuinely ambiguous/conflicting order for Example 1 vs. Example 2, even though the discriminating rule itself (any node with two children breaks degeneracy) is unambiguous.
- **Verify the exact exponent in the "2^(h+1) − 1 nodes" formula** (Slide 17) against the live slide before writing it on the board — the superscript character did not survive the text extraction cleanly.
- **Have the two Hook diagrams already drawn or printed before the session starts.** Building the file-system tree live on the board burns time you don't have in a 75-minute session with this much other content.
