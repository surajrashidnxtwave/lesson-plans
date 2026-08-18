# Session 42 — Reversals

**Duration** 45 min · **Topic** Linked List — Reversing a Singly Linked List · **Prerequisite** Linked list traversal & pointer manipulation, through Circular Linked List (Session 41)
**Session type** Concept lecture

<!-- placement: inferred — 45 min default kept, since this is a single problem with two approaches (unlike Session 41's six-operation breadth) -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Reversing a Linked List | https://docs.google.com/presentation/d/1j5mSD-AHioG60i9N5UzCNAIHsU4oRws3JDWTMfcQnsA/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given the head of a singly linked list, reverse the list and return the new head. *(REMEMBERING)*
2. Explain why the brute-force (stack) approach needs O(n) extra space, tracing how it rewrites node *data*, not links. *(UNDERSTANDING)*
3. Trace the optimal three-pointer (`prev`, `cur`, `front`) in-place reversal by hand on a 5-node list. *(APPLYING)*
4. Predict the state of `prev`, `cur`, and `front` at any point mid-traversal, given a starting list. *(APPLYING)*
5. Compare the time/space complexity of the brute-force and optimal approaches and justify why in-place reversal is preferred. *(ANALYZING / EVALUATING)*

<!-- placement: inferred — phrased from the deck's own Key Takeaways slides (45-46) -->

---

## Warm-Up Poll — Retrieval on Session 41 (0–7 min)

Say: *"Seven on last session's Circular Linked List before we start today. Show me the room."*

**Q1.** In a circular singly linked list, what does the last node's `next` pointer point to?
`A` `nullptr` · `B` The head (first node) · `C` Itself · `D` The second-to-last node

**Q2.** What is the time complexity of inserting a new node at the *beginning* of a circular singly linked list?
`A` O(1) · `B` O(n) · `C` O(k) · `D` O(log n)
→ *Read:* If many still say O(1), that's the DLL-vs-CLL contrast from last session not sticking. Give it one sentence before moving on — today's session doesn't need it, but next week's will.

**Q3.** Which loop condition does the deck's `display()` (traversal) function actually use to stop?
`A` `while (temp != NULL)` · `B` `do { ... } while (temp != head)` · `C` `while (temp->next != NULL)` · `D` `for (int i = 0; i < n; i++)`

**Q4.** Why would using `while (temp != NULL)` to traverse a circular linked list be a bug?
`A` It skips the first node · `B` It causes an infinite loop, since NULL never appears · `C` It stops one node too early · `D` It's not a bug — both work

**Q5.** To delete a specific (middle) node from a circular list, once you've found `prev`, what's the correct pointer update?
`A` `prev->next = target->next` · `B` `target->next = prev` · `C` `prev = target->next` · `D` `delete prev`

**Q6.** *(MSQ — pick up to 2)* Which of these did the deck list as disadvantages of a circular linked list?
`A` Infinite loop risk · `B` Cannot store data · `C` Hard debugging · `D` Requires contiguous memory

**Q7.** What real-world application did the deck give for a circular linked list in operating systems?
`A` Undo/redo stacks · `B` Round-robin scheduling · `C` Binary search · `D` File compression

**Running it** — poll tool, ~40 s per question, project the distribution after each. Total 7 min including your reads.

---

## Hook (7–10 min)

Line up 5 volunteers at the front, each one physically pointing at the next: 1 → 2 → 3 → 4 → 5, volunteer 5 pointing at nothing (NULL).

Ask: *"I want this line reversed — 5 pointing at 4, 4 at 3, and so on, ending with 1 pointing at nothing. Nobody moves position. Only pointing-arms change. What's the minimum number of arms that need to change direction?"*

Let them land on **five** — every single node's `next` has to flip, exactly once each.

Then: *"That's the whole session. Today you learn two ways to make five arms change direction: one that needs a helper holding all your old positions written down [gesture at a notepad — the stack], and one that needs nothing extra at all."*

---

## Slide Block A — Brute-Force Approach: Stack (10–18 min) — DELIVER SLIDES AS-IS

Covers: Problem statement (reverse `1→2→3→4→5→NULL` to `5→4→3→2→1→NULL`) → brute-force explanation → dry run pushing/popping a LIFO stack → pseudocode / C++ / Python code → complexity.

**Beats to emphasise**

- State the problem plainly first, straight off the deck's own example: given the head of a singly linked list, reverse it, return the new head.
- The brute force **never touches a single link**. It pushes every node's data onto a stack, then walks the list a *second* time, overwriting each node's `data` field with a pop from the stack. The nodes stay exactly where they are, in the same order — only the values inside them change. Say this out loud; everything else about this approach follows from it.
- Two full traversals (push loop + pop loop) → O(n) time. The stack holding all n values → O(n) extra space. Run the C++ or Python snippet live.

**Checkpoint (at 18 min)** — cold-call:
> *"After the brute-force reversal finishes, has a single `next` pointer in the list changed?"*
> **Answer:** No. The nodes are the same objects, in the same positions, in the same order — only each node's `data` field was overwritten from the stack.

---

## ⚡ Activity 1 — Predict-the-Output: Stack Dry Run (18–23 min)

**Format:** Predict-the-Output · **Exposes:** whether students actually track which value comes back out of a LIFO stack, using the deck's own dry-run list (`1, 2, 3, 4, 5` pushed in that order).

**Setup line (say this):**
> *"I'm pushing 1, 2, 3, 4, 5 onto a stack, in that order. Before I pop anything — on your fingers, show me: what's the very first value that comes back out?"*

**What students do:** Hold up fingers / call out an answer. Then pop one at a time on the board (5, then 4, 3, 2, 1), checking against the deck's own dry run.

**How to handle wrong answers:** If someone answers "1" (a FIFO/queue instinct), stop and physically demonstrate with 5 stacked books or hands: *"The last one down comes off first."* Re-ask before revealing the real order.

**Debrief line:**
> *"A stack always hands you back the last thing you gave it. That's the entire trick behind the brute-force reversal — push everything, then let the stack do the reversing for you, one pop at a time."*

**Cut rule:** If running short, skip the books demo, go straight to a show-of-hands vote, then reveal. Keep the debrief line verbatim.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

*(23–28 min — roughly halfway through the session)*

---

## Slide Block B — Optimal Approach: In-Place Reversal (28–36 min) — DELIVER SLIDES AS-IS

Covers: Optimal approach steps (three pointers: `cur`, `prev`, `front`) → dry run reversing `1→2→3→4→5` → pseudocode / C++ / Python code → complexity.

**Beats to emphasise**

- Three pointers, one job each: `prev` trails behind (starts at `nullptr`), `cur` is the node currently being flipped, `front` is a temporary bookmark so the rest of the list isn't lost the instant `cur->next` gets overwritten.
- The one line that does all the work is `cur->next = prev`. Say it, then show it flip on screen for node 1, node 2, node 3 — across the whole dry run.
- At the end of the loop, `cur` is `nullptr` and `prev` is sitting on the old last node (5) — that's why the function **returns `prev`**, not `head` and not `cur`.
- Zero extra data structures were allocated — this is the entire reason it's O(1) space against the brute force's O(n) stack. Say the contrast out loud; don't just point at the table.

**Checkpoint (at 36 min)** — show hands:
> *"At the moment the loop ends, `cur` is `nullptr`. What have we been returning as the new head, and why not `cur`?"*
> **Answer:** `prev` — it's sitting on the former last node, now the reversed list's first node. `cur` walked itself off the end and is `nullptr`.

---

## ⚡ Activity 2 — Dry-Run Relay: Three-Pointer Reversal (36–42 min)

**Format:** Live Coding / Dry-Run Relay (whiteboard, groups of 3) · **Exposes:** whether students can hold three moving pointers in their heads through a full pass, using the deck's own list `1 → 2 → 3 → 4 → 5 → NULL`.

**Setup line (say this):**
> *"Groups of three. Draw 1 → 2 → 3 → 4 → 5 → NULL on your sheet. One of you IS `prev`, one is `cur`, one is `front` — point at your node. On my go, move through exactly one iteration together, out loud, then freeze so I can check."*

**What students do:** Iteration by iteration — (1) `front = cur->next`; (2) re-point the arrow: `cur->next = prev`; (3) `prev = cur`; (4) `cur = front`. Repeat until `cur` runs off the end. Spot-check 2–3 groups per iteration as you walk the room.

**How it surfaces:** Two errors show up reliably. Most common: groups move `prev` and `cur` forward *before* re-pointing `cur->next`, which loses the arrow they were supposed to flip — say *"re-point first, then move — in that order, every time."* Second: groups forget to save `front` before overwriting `cur->next`, "losing" the rest of the list — ask them what's downstream of node 3 if they skip that step.

**Debrief line:**
> *"Five nodes, five flips, one pointer move each time — and nothing you didn't already have. That's the whole reason this is O(1) space: three pointers, no matter how long the list is."*

**Cut rule:** If running short, run it once as a whole-class relay with three volunteers at the board instead of small groups. Keep the debrief line verbatim.

---

## Exit Ticket (42–45 min)

> On paper: for the list `1 → 2 → 3 → NULL`, write the value of `prev`, `cur`, and `front` right after the **first** iteration of the optimal algorithm's loop body completes.
> **Answer:** `prev = 1` (now pointing to NULL as its `next`), `cur = 2`, `front = 3`.

Homework: re-attempt the dry run from memory. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Brute-force reversal rewires the pointers | "Reversal" sounds like it must flip links | Block A checkpoint — only `data` fields changed, confirmed by tracing it |
| The optimal algorithm returns `head` | Every function students have written so far returns the same variable it started with | Block B checkpoint — trace that `head` still points at the old first node; `prev` is the real new head |
| You can skip storing `front` and just write `cur = cur->next` after the flip | Looks equivalent, reads shorter | Activity 2 — have a group try it and watch the rest of the list disappear |
| The stack-based approach is "more correct" because it explicitly touches every value | Two loops feels more thorough than one | Block B's complexity contrast — O(n) space for nothing the in-place version doesn't already achieve in O(1) |

---

## Instructor Notes

- This is a single-problem session: the deck's own dry run (`1→2→3→4→5`) is deliberately the only example used throughout, including both activities. Do not introduce a second worked example — it costs time the plan doesn't have and adds nothing.
- **45 minutes fits comfortably** <!-- placement: inferred --> because unlike Session 41 there is exactly one problem with two approaches, not six operations to dry-run.
- Activity 2 is the centrepiece — protect its 6 minutes even if Block B's checkpoint runs long. Pointer-flipping only sticks once students have moved the arrows with their own hands.
- Keep the brute-force stack values (`1,2,3,4,5` pushed / `5,4,3,2,1` popped) visible on a side board through Block B — students will want to sanity-check the optimal result against it.
