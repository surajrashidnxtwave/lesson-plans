# Session 02 — Binary Tree Traversals

**Duration** 60 min · **Topic** Binary Tree Traversals · **Prerequisite** Introduction to Binary Trees (Session 01) · **Session type** Concept lecture

<!-- placement: inferred — duration set to 60 min (matching the reference session's length), not the 45-min default. Rationale: this deck covers a code-level Node template (in two languages), the conceptual case for traversal (Data Retrieval + Tree Modification), all three DFS orders plus BFS/Level-order, and a full 7-node worked dry run across all four traversals — comparable in density to Session 01, though less terminology-heavy and more procedural. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Binary Tree Traversals | https://docs.google.com/presentation/d/1Jd2OWb4FjwoWDe6efW1-Zl5ce2aEok950kLvW1FoAPY/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define traversal as visiting every node of a binary tree exactly once, in a specific order, to process or print its data. *(REMEMBERING)*
2. Explain why traversal is necessary, citing Data Retrieval (checking/searching a value) and Tree Modification (locating a node to change the tree's structure) as the two motivating use cases. *(UNDERSTANDING)*
3. State the visit-order rule for each of the four traversal types: In-order (Left, Root, Right), Pre-order (Root, Left, Right), Post-order (Left, Right, Root), and Level-order (level by level, left to right). *(REMEMBERING)*
4. Apply each of the four traversal rules to a given 7-node binary tree to produce its correct output sequence. *(APPLYING)*
5. Differentiate the three Depth-First orders (which all recurse into one subtree before the other) from Breadth-First/Level-order (which finishes an entire level before moving down). *(ANALYZING)* <!-- placement: inferred -->
6. Trace a Node-class definition (`data`, `left`, `right`, constructor) to explain how left/right pointers physically represent the tree being traversed. *(APPLYING)* <!-- placement: inferred -->

---

## Warm-Up Poll — Retrieval Practice on Session 01 (0–7 min)

> From this session onward, the warm-up poll is retrieval practice on the *immediately preceding* session, not a diagnostic. Today's poll checks Session 01 (Introduction to Binary Trees) terminology and tree-type recall, since today's traversal rules assume students already have a working mental model of "node," "root," and "at most two children."

Say: *"Seven questions on last session. This is the same tree vocabulary you'll need in about ten minutes — so if you're unsure, guess and we'll fix it live."*

**Q1.** What is the maximum number of children a node can have in a binary tree?
`A` 1 · `B` 2 · `C` 3 · `D` Unlimited
→ *Read:* This is the one fact every traversal rule assumes — "left" and "right," nothing else. If this isn't near 100%, restate it before Slide Block A.

**Q2.** A node with no children is called a ___.
`A` Root · `B` Parent · `C` Leaf node · `D` Ancestor

**Q3.** *(MSQ — pick 2)* Which of these are true of the root node?
`A` It's the topmost node · `B` It has no parent · `C` It always has exactly two children · `D` It's always a leaf
→ *Read:* If C gets picked, they're conflating "root" (a position) with "Full/Perfect" (a shape guarantee) — a Session 01 misconception. A 15-second correction now saves confusion later.

**Q4.** Last session flagged that two people can give different "height" values for the *same* tree. Why?
`A` One of them made an arithmetic mistake · `B` One counts nodes on the longest path, the other counts edges · `C` Height only applies to Perfect trees · `D` Height depends on which programming language is used
→ *Read:* If fewer than ~60% get this, re-state which convention this classroom uses — height/depth reasoning resurfaces constantly once we start writing traversal code.

**Q5.** A binary tree where every level is completely filled AND every leaf sits at the same level is called:
`A` Complete · `B` Perfect · `C` Full · `D` Balanced
→ *Read:* Complete vs. Perfect was the single most-missed distinction last session. If this is weak, spend 30 seconds re-drawing it: Complete only requires every level *except possibly the last* to be full.

**Q6.** A binary tree where every node has either 0 or 2 children, never exactly 1, is:
`A` Balanced · `B` Full · `C` Degenerate · `D` Skewed

**Q7.** True or false: a file system's directory tree, where a folder can contain many subfolders, is always a valid Binary Tree.
`A` True · `B` False
→ *Read:* False — ties directly into today's Node template, where every node has exactly two pointers (`left`, `right`) and nothing else. A folder with five subfolders simply can't be represented by one `Node` object the way this deck defines it.

**Running it** — poll tool, ~45 s per question, project the distribution after each. Never name individuals. Total 7 min including your reads.

---

## Hook (7–11 min)

Put this on the board, quoting the deck directly (Slide 18):

> **Why do we need traversal?**
> **PROBLEM:** To check / search the value of the node.

Draw the 7-node tree from the deck (root 1; left child 2 with children 4 and 5; right child 3 with children 6 and 7). Ask: *"I want to know if the value 60 exists somewhere in a tree like this. No rules yet — just tell me, where do YOU start looking, and in what order do you check the rest?"*

Take 2-3 different ad hoc answers — someone will say "start at the root," someone will say "check the biggest branch first," someone will improvise something inconsistent.

Then: *"You just each invented your own traversal — and none of you visited the nodes in the same order. That's a problem the moment two people need to agree on a result, or the moment you need to write code that does this the same way every single time. Today: four traversal orders, defined precisely enough that any two programmers get the identical sequence."*

---

## Slide Block A (11–20 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range — Slides 4-20: What is a Traversal, the Node template (C++ then Python), Why We Need Traversal, and Importance of Traversal (Data Retrieval + Tree Modification) -->

**Beats to emphasise**

- **Traversal, precisely:** "visiting each node of the tree exactly once, in a specific order" (Slide 4) — the word "exactly once" matters as much as "specific order"; flag both.
- **The Node template is the foundation for everything today.** Each node holds one value (`data`) and two pointers (`left`, `right`); the constructor's whole job is to set the value and default both pointers to null/None so a brand-new node starts with no children (Slide 7). Build the example tree live, edge by edge, exactly as the deck does (`root`, then `root->left`, `root->right`, then the four grandchildren) rather than describing it — show the pointers connecting.
- **Show the Python version as a fast syntax comparison, not a re-teach** (Slide 16) — same three fields, same idea, `None` instead of `nullptr`. <!-- placement: inferred — decide before class which language to lead with based on this course's primary language track; treat the other as a ~1 minute comparison. -->
- **Two concrete reasons traversal matters** (Slides 19-20, 50): **Data Retrieval** — checking whether a value exists in the tree; **Tree Modification** — locating a specific node so you can change the tree's structure around it (e.g. attaching a new child). These aren't the same operation — retrieval only reads, modification changes the tree.

**Checkpoint (at 20 min)** — cold-call two students:
> *"In one sentence each — what's the difference between traversing for Data Retrieval versus traversing for Tree Modification?"*
> **Answer:** Data Retrieval just checks whether a value is present (read-only). Tree Modification uses the same kind of search to locate a node, then changes the tree — e.g. attaching a new child to it.

---

## ⚡ Activity 1 — Spot the Bug: The Missing Pointer (20–25 min)

**Format:** Spot the Bug · **Exposes:** the assumption that "the constructor obviously sets everything up right" — a real bug students will write themselves the first time they hand-build a `Node` class. <!-- placement: inferred — this specific broken snippet is instructor-authored for this activity; the deck does not contain an explicit "mistakes" bank the way Session 1's Python-intro reference deck does. It is built directly on the deck's own stated constructor behaviour (Slide 7: "Sets left and right to nullptr — no children initially"). -->

**Setup line (say this):**
> *"Same Node class as the slide, except I've broken exactly one line. Before I build the example tree with this constructor, tell me what goes wrong — and where it shows up."*

Put this on screen:

```cpp
class Node {
public:
    int data;
    Node* left;
    Node* right;
    Node(int val) {
        data = val;
        right = nullptr;
        // left was never touched
    }
};
```

**What students do:** 60 seconds silent, then hands up — no shouting the answer, describe the *symptom* first.

**How it surfaces:** If nobody spots it immediately, prompt: *"According to the slide, what should the constructor set left and right to? Read both lines back to me from the code above."* The gap becomes visible once they read it aloud.

**Answer:** `left` is never initialized, so a brand-new node's left pointer holds garbage/undefined memory instead of `nullptr` — any later check like `if (node->left == nullptr)` becomes unreliable, and building the example tree (`root->left = new Node(20)`) will still technically work by *overwriting* the garbage, but any node left as a true leaf on its left side stays broken.

**Debrief line:**
> *"The constructor's only job is to leave you with a clean, fully-defined node — value set, both pointers null. Forget one line and you don't get an error today, you get a bug that only shows up three traversals from now when you check a pointer that was never actually nullptr. Read constructors completely, not just the lines that look important."*

**Cut rule:** If running short, skip the live discussion of *why* it doesn't crash immediately and just state the fix (`left = nullptr;`) — the core lesson (constructors must set every field) still lands from the setup line alone.

---

## Classroom Quiz (25–30 min)

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform. Note: the deck itself places a "Quiz Time!" marker slide at Slide 34, roughly 42% through its 81 slides — close enough to this session's halfway point (30 min of 60) that this placement lines up with the deck's own structure, not just the course-wide convention. -->

---

## Slide Block B (30–40 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range — Slides 21-33: Types of Traversal (DFS vs. BFS), In-order, Pre-order, Post-order definitions with the DFS summary table, and the BFS/Level-order definition -->

**Beats to emphasise**

- **Two families: DFS and BFS** (Slide 21). DFS goes deep into one subtree before moving to the next, using recursion or an explicit stack. BFS visits level by level, left to right (Slide 22, Slide 33).
- **The three DFS orders, stated exactly as the deck's own summary table gives them** (Slide 32) — say all three back-to-back so the contrast is audible:
  - **In-order:** Left subtree → Root → Right subtree
  - **Pre-order:** Root → Left subtree → Right subtree
  - **Post-order:** Left subtree → Right subtree → Root
- The only difference between the three is **when the root gets visited relative to the two subtrees** — before both (pre), between them (in), or after both (post). Say this explicitly; it's the one-sentence version of the whole block.
- **Level-order / BFS** is fundamentally different in mechanism, not just order — it needs a queue (finish this level completely before starting the next), not simple root-left-right recursion (Slide 33).

**Checkpoint (at 40 min)** — show of hands:
> *"Two of the three DFS orders visit the left subtree before touching the root. Which one visits the root FIRST, before either subtree?"*
> **Answer:** Pre-order — Root, Left, Right.

---

## ⚡ Activity 2 — Predict-the-Output: In-order vs. Pre-order vs. Post-order (40–48 min)

**Format:** Predict-the-Output · **Exposes:** the tendency to default to level-order (reading the diagram left-to-right, top-to-bottom) regardless of which specific rule was asked for.

**Setup line (say this):**
> *"Same seven-node tree every time — root 1, left child 2 with children 4 and 5, right child 3 with children 6 and 7. I name the rule, you give me the full sequence, using only the definition — Left/Root/Right in whatever order that rule says. No calculators, no guessing from the picture."*

Run the deck's own fully-worked dry run, one order at a time, taking a full-sequence prediction before each reveal:

- **In-order** (Left, Root, Right) → **4, 2, 5, 1, 6, 3, 7** (Slides 63-67)
- **Pre-order** (Root, Left, Right) → **1, 2, 4, 5, 3, 6, 7** (Slides 68-72)
- **Post-order** (Left, Right, Root) → **4, 5, 2, 6, 7, 3, 1** (Slides 73-78) <!-- placement: inferred — the deck-text extraction shows the final value as "10" (Slide 78: "4, 5, 2, 6, 7, 3, 10"), which cannot be correct since the tree's root value is 1, not 10, and post-order always ends on the root. Treat this as a transcription artifact and use 1; verify against the live slide before presenting. -->

**What students do:** Predict the full 7-value sequence out loud (or write it down) before each reveal; compare against the deck's worked build-up node by node.

**How to handle wrong answers:** The single most common miss is defaulting to **1, 2, 3, 4, 5, 6, 7** (level-order) no matter which rule was asked — call this out by name the first time it happens: *"That's level-order. Nobody asked for level-order yet. Re-check: does Left, Root, Right actually visit node 3 second?"* For In-order vs. Pre-order confusion specifically (both visit left first), the giveaway is WHEN the root appears — Pre-order prints it immediately, In-order only after the entire left subtree is done.

**Debrief line:**
> *"Same seven numbers, three completely different sequences, from the exact same tree. The order you visit left, root, and right isn't a detail — it IS the algorithm. Get it backwards and you don't get a wrong answer to the same traversal, you get a different traversal entirely."*

**Cut rule:** If running short, drop Post-order and run In-order vs. Pre-order only — those two are the most commonly confused pair (both start by going left) and carry the most diagnostic value.

---

## ⚡ Activity 3 — Level-Order Dry-Run Relay (48–54 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** treating BFS as "just another DFS order" instead of a fundamentally different, queue-based mechanism.

**Setup line (say this):**
> *"Same tree, one more time — but now I'm going row by row, not branch by branch. When I point at a level, that row calls out its values left to right, together, fast — like a queue, not like the recursive digging we just did."*

Run the deck's own level-by-level build-up (Slides 79-81) as a relay: root first, then the whole of level 1, then the whole of level 2.

- Level 0: **1**
- Level 1: **2, 3**
- Level 2: **4, 5, 6, 7**
- Full sequence: **1, 2, 3, 4, 5, 6, 7**

**What students do:** Assign rows of the room to levels if the class is large enough; each "level" calls out its values together, in order, when pointed at.

**How to handle wrong answers:** If a group jumps ahead depth-first (e.g. calls out "1, 2, 4" instead of waiting for the full level), stop and contrast directly with Activity 2: *"That's the DFS instinct again. BFS finishes the ENTIRE current level before dropping down one — nobody visits a grandchild before every child has been visited first."*

**Debrief line:**
> *"Level-order is the only one of the four traversals that needs a queue instead of straightforward recursion — and it's the one that matches how you'd naturally scan an org chart or a file browser's tree view, one row at a time."*

**Cut rule:** If running short, skip the row-by-row relay entirely and have the whole class chorus the full sequence once (1, 2, 3, 4, 5, 6, 7) against the slide — the queue-vs-recursion contrast can be stated verbally instead.

---

## Exit Ticket (54–60 min)

**Exit ticket** — on paper or in chat before anyone leaves:

> For the tree used all session (root 1; left child 2 with children 4, 5; right child 3 with children 6, 7), write the Pre-order traversal sequence. Then, in one sentence, say which traversal you'd reach for if you just wanted to check whether the value 60 exists somewhere in a tree — and why any order would actually work for that particular task.
> **Answers:** Pre-order = `1, 2, 4, 5, 3, 6, 7`. Any traversal order works for a pure existence check, because Data Retrieval (Slide 19-20) only needs every node visited at least once — the specific order only matters when the output's sequence itself is meaningful, or the tree has an ordering property (as future sessions on Binary Search Trees will introduce).

Scan responses on the way out. If many students can't articulate the second half, open the next session with a 2-minute recap distinguishing "traversal order matters for the output" from "traversal order doesn't matter for a plain existence check."

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Every traversal question's answer is "read the diagram left to right, top to bottom" (i.e., level-order) | Level-order matches how people naturally scan a picture | Activity 2 — naming the level-order default out loud the first time it appears, for a rule that wasn't asked for |
| In-order and Pre-order are basically the same because both "go left first" | Both orders do visit the left subtree before the right | Activity 2's explicit contrast on WHEN the root prints — immediately (Pre) vs. after the whole left subtree (In) |
| Traversal is only ever about printing/displaying values | Every worked example in the deck ends in a printed sequence | Restating Tree Modification (Slides 50-55) as an equally valid traversal use case — locating a node in order to change the tree, not just read it |
| BFS/Level-order can be written with the same simple root-left-right recursion as DFS | All four traversals are taught back-to-back and look structurally similar on slides | Activity 3's row-by-row relay, which physically cannot be done branch-by-branch |
| A constructor "obviously" initializes every field correctly just because it exists | Constructors look boilerplate and easy to skim | Activity 1 — one missing line (`left = nullptr`) is enough to break the guarantee the whole rest of the session depends on |

---

## Instructor Notes

- **The deck's own "Quiz Time!" marker (Slide 34) lines up closely with this plan's halfway quiz placement (30 of 60 min)** — this isn't an arbitrary course-wide convention colliding with the content, the deck itself treats this as a natural pause point, right after all four traversal types have been conceptually introduced and before the worked dry-run examples.
- **Verify Slide 78's final post-order value live before Activity 2.** The text extraction shows "...,3,10" where the mathematically correct value is "...,3,1" (post-order always ends on the root, and the root's value is 1, not 10). This is very likely a transcription artifact, not a deck error — but confirm against the actual slide before presenting it as fact.
- **The Data Retrieval worked example (Slides 39-49, searching for value 60) and the Tree Modification worked example (Slides 50-55, adding a right child to node 70) both contain extracted values that don't cleanly match the template tree** (10, 20, 30, 40, 50, 60, 70) — numbers like 35, 45, 95, and 55 appear in the raw extraction with no clear origin. Treat both examples at the conceptual level only ("traversal finds the target node, then optionally modifies it") in Slide Block A, and verify the exact on-screen walkthrough against the live slides before narrating specific intermediate steps.
- **The Node-class slides (5-16) show the same class in C++ then Python.** Decide before class which language to lead with based on this course's primary track; the second language is a ~1 minute "same idea, different syntax" pass, not a re-teach.
- **Pacing risk:** the four-traversal dry run (Activities 2-3) is where this session's real value sits — if Slide Block A's live code-typing runs long, protect Activities 2-3 by cutting per their stated cut rules rather than compressing the dry run itself.
