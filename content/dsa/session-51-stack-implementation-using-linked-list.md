# Session 51 — Stack Implementation Using Linked List

**Duration** 60 min <!-- placement: inferred — mirrors Session 50's depth: full push/pop dry run, pseudocode, and dual-language code --> · **Topic** Stack & Queue — Linked-List-Based Stack · **Prerequisite** Session 50 — Stack Implementation Using Array
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Stack Implementation Using Linked List | https://docs.google.com/presentation/d/19LsdepePTa52TM4UqbL4G4ZMhJM1MigMvtSx_ozv9mo/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State that a linked-list stack's `top` pointer corresponds to the head of the list, initialised to `null`. *(REMEMBERING)*
2. Trace `push` (create node → link it to the current top → update top) and `pop` (advance top → discard the old head) in the correct order. *(APPLYING)*
3. Explain why a linked-list stack has no fixed-capacity overflow condition, and what it means to say it can still fail when memory runs out. *(UNDERSTANDING)*
4. Implement `push`, `pop`, `top`, `empty`, and `size` as O(1) pointer operations. *(APPLYING)*
5. Weigh a linked-list stack's dynamic sizing and no-overflow behaviour against its per-node pointer overhead, compared to the array-based stack from Session 50. *(EVALUATING)*

---

## Warm-Up Poll — Retrieval Practice on Session 50 (Array Stack) (0–6 min)

Say: *"Eight questions on the array stack before we rebuild the same idea a completely different way."*

**Q1.** In the array implementation, `top` starts at what value on an empty stack?
`A` 0 · `B` -1 · `C` capacity · `D` null

**Q2.** The correct order of operations for `push` in the array implementation is:
`A` Insert the value, then increment `top` · `B` Increment `top`, then insert the value · `C` Order doesn't matter · `D` Decrement `top`, then insert
→ *Read:* If this isn't unanimous B, the whole session's contrast with linked-list push order won't land — recap it before moving on.

**Q3.** Overflow in the array stack occurs when:
`A` `top == -1` and you call push · `B` `top == capacity - 1` and you call push · `C` The array is empty · `D` `top == capacity`

**Q4.** Underflow occurs when:
`A` `top == -1` and you call pop · `B` `top == capacity - 1` and you call pop · `C` The array is full · `D` `top == 0`

**Q5.** What is the time complexity of push, pop, top, empty, and size in the array implementation?
`A` O(n) for all · `B` O(1) for all · `C` O(log n) for all · `D` Varies by operation

**Q6 (MSQ — pick all correct).** Which of these are genuine disadvantages of an array-backed stack?
`A` Fixed capacity risks overflow · `B` Resizing the array is expensive (allocate + copy everything) · `C` Reading the top element is slow · `D` Memory can be wasted if the array is much bigger than what's actually stored
→ *Read:* Correct answers: A, B, D. If anyone picks C, that's the misconception to squash right now — direct indexing is precisely what an array stack is *good* at.

**Q7.** True or False: `pop()` on the array stack clears/zeroes the array slot it just removed.
`A` True · `B` False

**Q8.** `isFull()` in the array implementation is the single comparison:
`A` `top == 0` · `B` `top == -1` · `C` `top == capacity - 1` · `D` `top == capacity`

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Say: *"Last session ended with a complaint: the array stack has a hard ceiling — capacity. Today's question: what if there's no array at all?"*

Draw a single box on the board: `[data | next]`. Ask:

> *"If this is all I have — one value, and a pointer to the next one — where would 'top' even live?"*

Take a guess or two, then reveal: *"Top isn't an index anymore. Top **is** a pointer, and it always points at the most recently added node — the head of the list. No capacity, no array, no `top = top + 1`. Instead: build a new node, hand it the old top as its `next`, and make it the new top. Three steps, every time."*

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–19: Introduction, Node Structure, Initialize the Stack, Empty() Operation, Push dry run (10, 20, 30) -->
Covers: Introduction (top = head of the list) → Node structure (`data`, `next`) → Stack initialised with `top = null` → `empty()` operation (`top == null`) → Push dry run: `push(10)`, `push(20)`, `push(30)`.

**Beats to emphasise**

- **Top is a pointer now, not a number.** Say this contrast explicitly against last session — it's the single biggest mental shift of the day.
- **Push is always three steps, in this fixed order:** (1) create a new node holding the value, (2) set the new node's `next` to whatever `top` currently is, (3) update `top` to point at the new node. Narrate all three steps, in order, for every push in this block — the order is non-negotiable, because step 2 has to happen *before* step 3 overwrites `top`.
- Walk `push(10)` (top: null → node(10)), `push(20)` (node(20)→node(10), top moves to node(20)), `push(30)` (node(30)→node(20)→node(10), top moves to node(30)). By the third push, students should be able to predict the chain themselves.
- **`empty()`** is one check: is `top` `null`?

**Checkpoint (at 19 min)** — cold-call:
> *"After push(10), push(20), push(30) — what does `top` point to, and what does `top->next->next` point to?"*
> **Answer:** `top` points to the node holding 30. `top->next->next` is the node holding 10 (30 → 20 → 10 → null).

---

## ⚡ Activity 1 — Live Trace: "Three Steps, Every Push" (19–25 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can hold the *order* of push's three steps and pop's two steps in their head — this is the exact ordering Activity 2 will later show breaking.

**Setup line (say this):**
> *"Same idea as last session's relay, pointers instead of indices. I call an operation, you tell me what `top` points to *and* what `top->next` is, before I confirm."*

Run these **one at a time** (continuing the deck's own numeric example):

```
push(10)   → top → 10 → null
push(20)   → top → 20 → 10 → null
push(30)   → top → 30 → 20 → 10 → null
top()      → returns 30, chain unchanged
pop()      → Step 1: top = top->next (top now → 20)
           → Step 2: discard the old node holding 30
           → chain: top → 20 → 10 → null
push(40)   → top → 40 → 20 → 10 → null
push(50)   → top → 50 → 40 → 20 → 10 → null
```

**How it surfaces:** Before revealing each `pop()`, ask: *"Which happens first — moving `top`, or discarding the old node?"* The correct answer is always "move top first." Make students say the two pop steps in order out loud before you confirm the new chain.

**Common wrong answer:** students say pop "just removes the top node" without articulating that `top` must be reassigned *before* the old node can safely be discarded — this is exactly the bug Activity 2 exposes.

**Debrief line:**
> *"Every push is three moves — new node, link it to the old top, then take over as top. Every pop is the same three moves backwards — move top off the old node first, *then* clean it up. Get that order wrong and you either lose the list or point at deleted memory."*

**Cut rule:** If running short, drop the final two pushes (40, 50) — the increment-order lesson is already fully demonstrated by push(10)/(20)/(30) and the one pop.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (30–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 20–56: Top() Operation, continued Push/Pop dry run (40, 50, 60), full pop-down to empty, Empty() revisited, Advantages -->
Covers: `top()` (read-only) → continued push/pop dry run (up to 60) → full pop sequence back down to an empty stack → `empty()` revisited → **Advantages**: dynamic size, efficient memory utilisation, no overflow, easy insertions/deletions.

**Beats to emphasise**

- **`top()` never touches the pointer chain**, exactly like the array version never touched `top` on a read — same principle, different mechanism. Draw the parallel explicitly.
- Narrate the deck's continuing dry run (push 40, pop, top, push 50, pop, push 60, pop, pop, pop, pop) at a brisk pace — by now students should be predicting each `top` chain themselves before you reveal it.
- **Popping all the way to empty:** walk the final few pops explicitly down to `top = null`. This is the moment to say: *"Notice there was never a moment where a push could fail. There was no capacity to run out of."*
- **Advantages, read as a list students should be able to recite:** dynamic size (grows/shrinks freely), efficient memory use (only allocate what you use), no overflow condition, and push/pop are simple pointer rewrites with no shifting of other elements.

**Checkpoint (at 42 min)** — show of hands:
> *"True or false: a linked-list stack can never fail to push, under any circumstances."*
> **Answer:** False — it can't overflow the way an array does, but a push can still fail if the system is out of memory to allocate a new node. "No overflow" means no *fixed-capacity* overflow, not "infinite."

---

## ⚡ Activity 2 — Spot the Bug: "Delete Before You Move" (42–47 min)

**Format:** Spot the Bug · **Exposes:** the exact ordering bug Activity 1's debrief warned about — deleting the old top node before reassigning `top` away from it.

**Setup line (say this):**
> *"Here's a `pop()` with its two steps swapped. Tell me exactly what breaks, and why."*

Put this on screen:

```cpp
void pop() {
    if (empty()) return;
    delete top;          // BUG: top is deleted first
    top = top->next;     // top is now a dangling pointer — reading top->next is undefined behavior
}
```

**What students do:** 30 seconds silent, then hands up.

**Answer:** `delete top` frees the memory the node occupied. The very next line then tries to read `top->next` — but `top` no longer points to valid memory. This is **undefined behaviour**: it might crash, might silently return garbage, might appear to "work" during testing and fail later. The fix is to save `top->next` (or a temp pointer to the old node) *before* deleting.

**How it surfaces:** Ask: *"Why might this bug pass your test cases and still be wrong?"* Expect: undefined behaviour sometimes happens to produce the "right" answer by luck, which is worse than an obvious crash because it hides the bug.

**Debrief line:**
> *"This is the pointer version of last session's missing overflow guard — a one-line reordering that turns working code into a landmine. Always capture what you need from a node before you free it, never after."*

**Cut rule:** If running short, skip the "why might it pass tests" discussion and move straight from the bug identification to the debrief line.

---

## Slide Block C (47–55 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 57–79: Pseudocode (push/pop/top/empty/sizeOfStack), Complexity Analysis, C++ Code, Python Code, Key Takeaways -->
Covers: Pseudocode for `push`, `pop`, `top`, `empty`, `sizeOfStack` → Complexity table (all O(1)) → C++ implementation (`Node` + `Stack` classes) → Python implementation → Key Takeaways.

**Beats to emphasise**

- Read the `push` pseudocode aloud and map it directly onto Activity 1's three steps: `Node* temp = new Node(x)` (step 1), `temp->next = top` (step 2), `top = temp` (step 3, plus `size++`).
- Read the `pop` pseudocode aloud and map it onto Activity 2's fix: `Node* temp = top` (save first), `top = top->next` (move), `delete temp` (then clean up) — this is the *correct* order the buggy version violated.
- C++ and Python code are the same logic in two syntaxes — a fast walkthrough, not a re-teach.
- **Complexity table:** push, pop, top, empty all O(1) time and O(1) space — same headline result as the array version, achieved by completely different plumbing.

**Checkpoint (at 55 min)** — cold-call:
> *"In the correct pop pseudocode, why does `Node* temp = top` have to come before `top = top->next`?"*
> **Answer:** Because once `top` is reassigned, the only way to reach the old node (to delete it) is through a pointer saved beforehand — otherwise it's lost or, worse, deleted while still needed.

---

## ⚡ Activity 3 — Predict & Discuss: "Can This Ever Overflow?" (55–58 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** the lingering belief that "no overflow" in the Advantages slide means the structure is literally unbreakable.

**Setup line (say this):**
> *"The deck's Advantages slide says 'no overflow.' Thirty seconds — is that ever untrue? When would a push on this exact linked-list stack actually fail?"*

**What students do:** Discuss in pairs for 30 seconds, then two or three volunteers answer.

**Answer:** It's true there's no *fixed-capacity* overflow — there's no `capacity - 1` check anywhere in this implementation. But `new Node(x)` still asks the operating system for memory. If the system is genuinely out of memory, that allocation fails — which is a real-world "overflow" of a different kind (a system-level failure, not a logical one the stack code checks for).

**How it surfaces:** If students insist it truly cannot fail, ask: "What does `new` do if there's no memory left anywhere on the machine?" — steer them to the idea that "no overflow" is a claim about the *algorithm's* logic, not a guarantee about the *machine* it runs on.

**Debrief line:**
> *"'No overflow' is true at the level we're reasoning about — no arbitrary capacity limit baked into the code. It doesn't mean unlimited memory. That distinction matters every time a slide says a structure 'can't' fail."*

**Cut rule:** If running short, skip straight to the debrief line without the discussion — state the distinction directly.

---

## Exit Ticket (58–60 min)

> An empty linked-list stack (`top = null`). Draw or write out the chain after: `push(4)`, `push(9)`, `pop()`, `push(2)`.
> **Answer:** `push(4)` → `top → 4`. `push(9)` → `top → 9 → 4`. `pop()` → `top → 4` (9 is discarded). `push(2)` → `top → 2 → 4`.

Scan responses on the way out. If several students draw the chain in the wrong direction (oldest element on top), that's the "top is always the most recent" rule not sticking — reopen Session 52 with a 60-second recap.

**Homework:** re-draw the exit-ticket chain from memory, then extend it with two more operations of your choice and trace `top` by hand. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A linked-list stack can never fail under any circumstances | The Advantages slide literally says "No Overflow" | Activity 3 — distinguishing "no fixed-capacity overflow" from "infinite memory" |
| `pop()` just "removes the top node," order doesn't matter | Push/pop feel like single atomic actions in everyday language | Activity 2's Spot the Bug — showing the exact crash/undefined-behaviour caused by deleting before reassigning `top` |
| Push order is create → update top → link next | Students port over the array stack's "increment first" instinct without realising the object being updated is different here | Slide Block A's explicit 3-step narration, repeated for all three pushes in the dry run |
| `top->next` after several pushes points to the *oldest* element | "Next" sounds forward-moving, like it should lead deeper into the list in insertion order | Tracing the full chain in the checkpoint at minute 19 — showing `top->next->next` reaches backward through push history |
| A linked-list stack is strictly better than an array stack because it "never overflows" | Session frames overflow as a weakness of arrays without weighing the tradeoff | Slide Block B's Advantages beat, paired with an explicit reminder of the per-node pointer memory overhead arrays don't have |

---

## Instructor Notes

- **This session is a structural mirror of Session 50** — same beats (init → push dry run → pop dry run → failure conditions → pseudocode → code → key takeaways), same activity types, deliberately, so students can map the two implementations onto each other operation-by-operation rather than learning them as unrelated topics.
- **The three-step push / two-step pop ordering is the whole session.** If you only have time to drill one thing, drill this — it's tested in the Warm-Up carryover, Activity 1, Activity 2, and the checkpoint at minute 55.
- **Resist the urge to declare a "winner" between array and linked-list stacks.** The deck presents linked-list advantages without a matched disadvantages slide (unlike Session 50, which had both) — say explicitly in Slide Block B that the tradeoff is per-node memory overhead (each node stores a pointer in addition to the data) and generally worse cache locality than a contiguous array, even though there's no hard capacity ceiling.
- **Pacing risk:** the pop-to-empty dry run in Slide Block B (roughly 25 slides in the deck) can drag if narrated at full detail. Narrate the first 2-3 pops fully, then accelerate through the rest — students have already seen the pattern from Activity 1.
- **If you're behind by Activity 3, cut it per its cut rule** and move straight to the Exit Ticket — Activities 1 and 2 already carry the two load-bearing ideas (operation ordering, and what "no overflow" really means).
