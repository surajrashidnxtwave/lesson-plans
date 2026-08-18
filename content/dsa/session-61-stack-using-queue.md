# Session 61 — Stack Using Queue

**Duration** 45 min <!-- placement: inferred — single approach but a genuinely counter-intuitive rotation trick, standard length for this block --> · **Topic** Stack & Queue — Implementation: Stack Using Queue · **Prerequisite** Session 60 — Queue Implementation Using Linked List
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Implementation Stack Using Queue | https://docs.google.com/presentation/d/1dbh5JIZD5r6Rw__F5MxnwIBMkkLmeZI-ExoISq4M6mQ/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the constraint of this problem: build a stack's `push`/`pop`/`top` behaviour using only a queue's `enqueue`/`dequeue` operations. *(REMEMBERING)*
2. Explain the rotation trick — after enqueueing a new element, rotate the entire rest of the queue behind it so the newest element sits at the front. *(UNDERSTANDING)*
3. Trace `push`, `pop`, and `top` on a single-queue stack simulation across a full sequence of operations. *(APPLYING)*
4. Analyse the resulting time complexity trade-off: `push` becomes O(n), while `pop` and `top` stay O(1) — and explain *why* the cost had to move somewhere. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 60 (Queue: Linked List Implementation) (0–6 min)

Say: *"Six on the linked-list queue, then we do something a little backwards — build a stack out of a queue."*

**Q1.** In the linked-list queue, `front` and `back` are:
`A` Array indices · `B` Node pointers · `C` Both required to be integers · `D` Not needed at all

**Q2.** When the last remaining node is popped from a linked-list queue, which pointers must be reset?
`A` Just `front` · `B` Just `back` · `C` Both `front` and `back` · `D` Neither — they reset automatically

**Q3.** The main advantage of a linked-list queue over an array-based one is:
`A` Faster `push` · `B` No fixed capacity limit · `C` Supports random access · `D` Uses less memory always
→ *Read:* B. If this misses, restate it in one line — today's session builds a *different* structure entirely on top of a queue, so the queue's own properties need to be rock solid first.

**Q4.** All core operations on a linked-list queue (`push`, `pop`, `front`) run in:
`A` O(1) · `B` O(n) · `C` O(log n) · `D` O(n²)

**Q5.** A queue follows which discipline?
`A` LIFO · `B` FIFO · `C` Random order · `D` Sorted order

**Q6 (MSQ — pick all correct).** Which are true of the linked-list queue?
`A` Nodes are allocated on demand · `B` It has no fixed capacity · `C` `push` and `pop` involve pointer reassignment, not array arithmetic · `D` It requires a modulo operation somewhere

**Running it** — poll tool, ~25 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"You have a queue — enqueue at the back, dequeue from the front. That's the only tool you're allowed to use. Can you make it behave like a stack — last in, first out?"*

Let students think about it for a moment; most will assume this needs a completely different structure. Then:

> *"You can — with a single queue, and one clever trick played at push time. It's a strange constraint to work under, and that's exactly the point of this session: understanding both structures well enough to fake one using only the other. It costs you something, though — and figuring out *what* it costs, and *why*, is the real lesson today."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–10: Introduction, Basic Operations, Approach -->
Covers: Introduction (simulate a stack's behaviour using a queue's `enqueue`/`dequeue`) → Basic Operations (`push`, `pop`, `top`, `empty`, `size`) → Approach: use a single queue; on `push`, insert the new element, then rotate the queue so the newest element moves to the front.

**Beats to emphasise**

- State the goal precisely: "the *interface* looks like a stack — `push`, `pop`, `top` — but the *only* primitive operations available underneath are `enqueue` and `dequeue`."
- **Say the rotation idea as one sentence, this is the whole session:** "every time you push, immediately rotate everything that was already in the queue around to behind the new element — so whatever you just pushed ends up at the very front, exactly where a stack's `pop` and `top` expect to find it."
- Preview the cost trade-off without resolving it yet: "the front of the queue always holds the top of the stack, which makes `pop` and `top` trivially O(1) — but that rotation on every single `push` isn't free. What does it cost?"

**Checkpoint (at 17 min)** — cold-call:
> *"After every `push`, where does the newly pushed element end up sitting in the queue?"*
> **Answer:** At the front — the rotation moves it there specifically so `pop()` and `top()` can just read the queue's front directly, with no searching.

---

## Slide Block B (17–25 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 11–36: Dry Run on push(10,20,30,40), pop(), top(), pop(), top(); Pseudocode, Complexity, Code -->
Covers: Full dry run of `push(10), push(20), push(30), push(40), pop(), top(), pop(), top()` → pseudocode (`push` enqueues then rotates `n` times) → complexity (`push`: O(N); `pop`, `top`, `empty`, `size`: O(1)) → C++ code.

**Beats to emphasise**

- Narrate the rotation count exactly as the deck does: `push(10)` into an empty queue — 0 rotations needed. `push(20)` — queue had 1 element, so rotate once: enqueue `20`, then move `10` to the back. Queue becomes `[20, 10]`. `push(30)` — queue had 2 elements, rotate *twice*: enqueue `30` → `[20, 10, 30]`; move `20` to back → `[10, 30, 20]`; move `10` to back → `[30, 20, 10]`.
- **Say explicitly: rotation count equals the queue's size *before* the new element was added** — not after. This is the detail students most often get off-by-one on.
- Walk `pop()` and `top()` as trivial reads: `pop()` just dequeues the front (`40`, then `30`); `top()` just peeks the front without removing it — no rotation involved in either.

**Checkpoint (at 25 min)** — cold-call:
> *"When we called `push(40)` with three elements already in the queue, how many times did we rotate — and why that many?"*
> **Answer:** Three times — one for each element that was already in the queue before `40` was added; each rotation moves one "older" element from front to back, until `40` reaches the front.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Rotate It Yourself" (30–36 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can execute the rotation count and mechanics themselves on a fresh sequence, rather than having only watched the deck's example.

**Setup line (say this):**
> *"Fresh queue. Sequence: `push(5), push(15), push(25)`. After each push, tell me the rotation count and the resulting queue order, front to back, before I confirm."*

Run **one push at a time**:

```
push(5)  → queue empty → 0 rotations → enqueue 5.                     Queue: [5]
push(15) → 1 element already there → rotate once:
           enqueue 15 → [5, 15]; move 5 to back → [15, 5].            Queue: [15, 5]
push(25) → 2 elements already there → rotate twice:
           enqueue 25 → [15, 5, 25];
           move 15 to back → [5, 25, 15];
           move 5 to back → [25, 15, 5].                              Queue: [25, 15, 5]
```

**How it surfaces:** At `push(25)`, ask before revealing: *"How many rotations, and what's the very first one?"* Correct: two rotations, and the first one moves `15` (not `5`) to the back — rotation always operates on whatever is currently at the *front*, in order, one at a time.

**Debrief line:**
> *"Front to back, always: `[25, 15, 5]` — most recently pushed at the front, oldest at the back. That's a stack's order, LIFO, built entirely from a FIFO structure plus one rotation step per push."*

**Cut rule:** If running short, do just `push(15)` — a single rotation is enough to demonstrate the mechanism; `push(25)`'s two-rotation step is confirmation, not new information.

---

## ⚡ Activity 2 — Predict & Discuss: "Where Did the Cost Go?" (36–41 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students understand the complexity trade-off as a *conservation* of work, not a free win — the actual insight behind this whole session.

**Setup line (say this):**
> *"A native stack — the kind you built nine sessions ago — has `push`, `pop`, and `top` all at O(1). This queue-based version has `pop` and `top` at O(1) too. So where did the cost go? It has to be somewhere — nothing here is actually free."*

**What students do:** Discuss for a minute, then share out.

**Answer:** The cost moved entirely into `push`, which is now O(N) — every single push rotates however many elements were already there. A native stack's `push` is O(1); this version trades that away specifically so that `pop` and `top` can stay O(1), matching a native stack's behaviour on those two operations.

**How it surfaces:** Ask a follow-up: *"If your program does mostly pushes and very few pops, is this a good trade?"* Push toward: no — you'd be paying the O(N) cost repeatedly for little benefit. *"If your program does one big burst of pushes, then mostly pops and tops?"* Better trade — the expensive part happens once, then everything else is cheap.

**Debrief line:**
> *"Every 'simulate X using Y' trick you'll ever see works this way — something gets cheaper, and something else absorbs that cost. The real skill isn't memorising the trick, it's being able to say exactly where the bill went."*

**Cut rule:** If running short, skip the "mostly pushes vs. mostly pops" follow-up discussion and just state the O(N) push / O(1) pop-and-top trade-off directly, then move to the exit ticket.

---

## Exit Ticket (41–45 min)

> Starting from an empty queue-based stack, you call `push(1), push(2), push(3)`. How many total rotation steps happened across all three pushes?
> **Answer:** `0 + 1 + 2 = 3` total rotations — each push rotates a number of times equal to however many elements existed before it.

**Homework:** trace `push(7), push(14), push(21), pop(), push(28), top()` and report the queue's contents after each step. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The rotation count is based on the queue's size *after* the new element is added | Off-by-one instinct when counting "how many things are in here now" | Activity 1's `push(25)` step — explicitly counting 2 rotations for 2 *pre-existing* elements, not 3 |
| `pop()` and `top()` also need some kind of rotation | Since `push` involves rotation, it feels like symmetry would demand the same from every operation | Slide Block B — `pop` and `top` are direct reads/removals of the front, no rotation involved at all |
| This trade-off is a "trick" with no real cost, since the interface still looks like O(1) stack operations | The `pop`/`top` O(1) result is what's usually highlighted, and it's easy to stop looking once that's confirmed | Activity 2 — explicitly locating the O(N) cost inside `push`, where it's less visible unless directly asked about |
| A native stack (array or linked-list based) is strictly worse than this approach, since this one also achieves O(1) pop/top | Surface-level comparison stops at matching complexities on two operations | State plainly: a native stack has *all three* operations at O(1); this is strictly worse overall — it's a constraint-following exercise, not an optimisation |
| Rotating "the whole queue" means every element moves more than once per push | The phrase "rotate the queue" sounds like a repeated, ongoing process rather than a fixed one-time pass | Activity 1 — each rotation step moves exactly one element from front to back, and the loop runs exactly as many times as there were pre-existing elements, then stops |

---

## Instructor Notes

- **This session's payoff is Activity 2, not the mechanical trace.** Students can often execute the rotation dry run correctly while still missing *why* it's interesting. Protect time for the discussion — cut Activity 1 to its stated cut rule first if behind.
- **Say "Stack Using Queue" and "Queue Using Stack" (next session) out loud, distinctly, more than once.** They are mirror-image problems with easily confused names — flagging this explicitly now saves confusion at the start of Session 62.
- **Have the rotation dry run's queue states written out before class** (as in Activity 1) — narrating rotation counts live without a prepared reference is easy to fumble mid-explanation.
- **Bridge to Session 62 explicitly at the close:** "Today we built a Stack out of a Queue. Next session, we do the reverse — build a Queue out of Stacks — and you'll see the exact same push-vs-pop cost trade-off show up again, just pointed in the opposite direction."
