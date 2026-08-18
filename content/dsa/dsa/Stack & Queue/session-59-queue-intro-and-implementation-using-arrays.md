# Session 59 — Introduction to Queue & Implementation Using Arrays

**Duration** 45 min <!-- placement: inferred — new data structure introduction plus one implementation, standard length for this block --> · **Topic** Stack & Queue — Queue Introduction and Array Implementation · **Prerequisite** Session 58 — Largest Rectangle in Histogram
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Introduction to Queue (Implementation Using Array) | https://docs.google.com/presentation/d/1bqZqsiOqiO6NrptQdMTMV9yDvDqa8yq0PhbuKlzTtG4/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a queue as a First-In-First-Out (FIFO) data structure, contrasting it explicitly with a stack's LIFO behaviour. *(REMEMBERING)*
2. Explain why an array-based queue needs both a `front` and a `back` pointer, rather than just one, and why naive shifting on every `pop` would be wasteful. *(UNDERSTANDING)*
3. Trace `push` and `pop` operations on a fixed-size circular array, including the modulo wraparound that reuses freed slots. *(APPLYING)*
4. Identify the queue's fixed-capacity limitation and explain why that motivates a linked-list-based implementation, covered next session. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 58 (Largest Rectangle in Histogram) (0–6 min)

Say: *"Six quick ones on histograms, then we leave stacks behind for one session and meet an entirely new structure."*

**Q1.** A rectangle anchored at a given bar's height can extend as far as:
`A` The edges of the array, always · `B` The nearest shorter bar on each side · `C` The nearest taller bar on each side · `D` Exactly one bar in each direction

**Q2.** NSE (Next Smaller Element) for a bar is computed by scanning:
`A` Left to right · `B` Right to left · `C` From the tallest bar outward · `D` In sorted order

**Q3.** In the one-pass optimal approach, a bar's area is computed:
`A` At the very start · `B` At the very end, for all bars at once · `C` The moment it's popped from the stack · `D` Only if it's the tallest bar

**Q4.** If the stack empties during a pop in the one-pass approach, the rectangle's width is:
`A` Zero · `B` Undefined · `C` The current index · `D` Always 1
→ *Read:* C. If this misses, restate it in one line — today's problem (Queue) is unrelated in mechanism, but "read the state carefully before assuming an edge case is broken" is exactly the habit that prevents today's most common bug too.

**Q5.** What data structure powers every approach in the Largest Rectangle problem?
`A` A queue · `B` A stack · `C` A hash map · `D` A binary tree

**Q6 (MSQ — pick all correct).** Which are true of the Largest Rectangle problem?
`A` Both approaches give the same final answer · `B` The two-pass approach uses two separate stack scans · `C` It requires sorting the bars first · `D` Every bar's rectangle width depends on its neighbours' heights

**Running it** — poll tool, ~25 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"You're standing in a line at a ticket counter. Who gets served first — the person who joined the line first, or the person standing closest to the counter right now?"*

Let students answer (first joined). Then:

> *"That's a queue. Whoever arrived first leaves first — First In, First Out, FIFO. It's the exact opposite discipline from every stack you've built for the last eight sessions, where the *most recently* added thing came out first. Today we build this from scratch, starting with the simplest possible version: a plain array."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–8: Introduction to Queue, FIFO principle, Array Initialization, front/back pointer setup -->
Covers: Queue definition (FIFO, linear data structure) → array-based setup: a fixed-size array, with `front` and `back` pointers both initialised to `-1` to signal "empty."

**Beats to emphasise**

- Say the FIFO/LIFO contrast out loud, explicitly, once: "A stack's `top` is both where you add and where you remove. A queue splits that into two roles — `back` is where you add, `front` is where you remove. That single design choice is the entire idea."
- `front = -1, back = -1` is a sentinel meaning "nothing here yet" — not a real index into the array. Flag this now; it resurfaces at every empty/single-element edge case.
- This session uses a **fixed-size** array on purpose — name the tension up front: "what happens when the array is full but there's still room at the *front*, because things have already been popped from there?" — that tension is exactly what the dry run resolves next.

**Checkpoint (at 17 min)** — cold-call:
> *"Why does a queue need two pointers, `front` and `back`, when a stack only ever needed one, `top`?"*
> **Answer:** A queue adds at one end and removes from the *other* end — two distinct positions need tracking. A stack adds and removes from the *same* end, so one pointer covers both.

---

## Slide Block B (17–25 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 9–38: push/pop dry run on capacity-5 array with circular wraparound, Pseudocode, Advantages/Disadvantages, Complexity -->
Covers: Full dry run — `push(1), push(2), push(3), push(4)`, `front()`, `pop(), pop()`, `push(5), push(6)`, more pops — on a capacity-5 array, including the moment `back` wraps around from index 4 back to index 0 using modulo.

**Beats to emphasise**

- Narrate the ordinary pushes first: `push(1)` sets `front=0, back=0` (first element is special-cased). `push(2), push(3), push(4)` each just advance `back` by one. Array now `[1,2,3,4,_]`, `front=0, back=3`.
- After two pops (`front` advances to index 2, then 3), there are now **two free slots at the front** of the array (indices 0 and 1) even though `back` is sitting at index 3. **This is the moment worth slowing down for:** `push(5)` uses index 4 (still within bounds), but `push(6)` needs a slot, and the only free ones are indices 0 and 1 — behind `front` positionally, but logically still "next." That's what `back = (back + 1) % capacity` buys: `back` wraps from 4 to `0`.
- State plainly: without the modulo wraparound, this array would report "full" after just one round of pushes and pops, even with free slots sitting right there — a huge waste. This is why it's called a **circular** array.

**Checkpoint (at 25 min)** — show hands:
> *"After popping enough elements that `front` has moved past index 0 and 1, and `back` is about to overflow past the last index — who says `back` just stays stuck at the end? Who says it wraps around?"*
> **Answer:** It wraps around, via `back = (back + 1) % capacity` — that's the entire trick that makes a fixed array behave like a circular buffer.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Predict the Output: "Trace the Circular Wraparound" (30–36 min)

**Format:** Predict-the-Output / Live Trace · **Exposes:** whether students can track `front`, `back`, and `size` through a full wraparound cycle themselves — the single trickiest mechanical detail in this session.

**Setup line (say this):**
> *"Capacity-4 array, starts empty. Sequence: `push(A), push(B), push(C), pop(), pop(), push(D), push(E)`. After each operation, tell me `front`, `back`, and what's actually stored, before I confirm."*

Run **one operation at a time**:

```
push(A) → front=0, back=0.                arr: [A, _, _, _]
push(B) → back=1.                         arr: [A, B, _, _]
push(C) → back=2.                         arr: [A, B, C, _]
pop()   → front=1 (A removed logically).  arr: [A, B, C, _]  (A still physically there, just unreachable)
pop()   → front=2 (B removed logically).  arr: [A, B, C, _]
push(D) → back=3.                         arr: [A, B, C, D]
push(E) → back=(3+1)%4=0 → wraps!         arr: [E, B, C, D]  (E overwrites A's old slot)
```

**How it surfaces:** At `push(E)`, ask before revealing: *"Is the array full at this point? `back` is about to go past index 3 — what happens?"* Correct: it's *not* full — `front` is sitting at index 2, meaning slots 0 and 1 are free — so `back` wraps to `0` and reuses that freed slot, rather than reporting overflow.

**Debrief line:**
> *"The values at indices 0 and 1 never got erased — `pop()` just moved `front` past them. `push(E)` is the operation that actually overwrites what's there. Physical array position and logical queue position are two different things, and that gap is exactly what the modulo arithmetic bridges."*

**Cut rule:** If running short, do only the `push(D)` and `push(E)` steps — the wraparound is the entire point; the earlier pushes and pops are just setup.

---

## ⚡ Activity 2 — Spot the Bug: "Full or Empty?" (36–41 min)

**Format:** Spot the Bug · **Exposes:** the classic array-queue ambiguity — `front == back` can mean either "completely empty" or "exactly one element," and conflating the two is the most common real bug in this implementation.

**Setup line (say this):**
> *"Three queue states, each showing `front` and `back` at the same index. For each: is the queue empty, or does it have exactly one element? How would code even tell the difference?"*

```
1.  front = -1, back = -1
2.  front = 2,  back = 2   (after a fresh push(x) as the very first element)
3.  front = 2,  back = 2   (after several push/pop cycles have looped back around)
```

**What students do:** 45 seconds silent, then hands up.

**Answers**

| # | State | How to tell |
|---|---|---|
| 1 | Empty | `-1, -1` is the dedicated sentinel — this is the *only* unambiguous empty signal in this design |
| 2 | One element | `front == back` at a real index, with a `size` counter confirming `size = 1` |
| 3 | One element | Same as #2 — `front == back` alone can't distinguish "just started" from "wrapped back around"; only an explicit `size` variable (or capacity check) resolves it |

**How it surfaces:** Push students to say explicitly *why* `front == back` is ambiguous on its own — it's true both when there's exactly one element and, in some naive implementations, when the queue is completely empty. This deck's design sidesteps the ambiguity with the dedicated `-1, -1` sentinel plus an explicit `size` counter — flag that as a deliberate design choice, not an accident.

**Debrief line:**
> *"`front == back` alone never tells you the whole story. This implementation solves it two ways at once: a `-1, -1` sentinel for genuinely empty, and a `size` counter for everything else. Skip either one and you'll misreport empty as full, or vice versa."*

**Cut rule:** If running short, do state 1 vs. state 2 only — that pairing alone establishes the sentinel-vs-real-index distinction; state 3 is a reinforcement.

---

## Exit Ticket (41–45 min)

> A capacity-3 array queue currently has `front=1, back=1, size=1` (one element, at index 1). You call `push(x)`. What is `back` afterward, and why?
> **Answer:** `back = (1 + 1) % 3 = 2`. No wraparound needed yet — index 2 is still within bounds and unused.

**Homework:** trace `push(1), push(2), push(3), pop(), push(4), push(5)` on a capacity-3 array by hand, tracking `front`, `back`, and array contents at every step. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| `pop()` physically removes/clears the array slot | Mental model carried over from thinking of arrays as "boxes that empty out" | Activity 1 — showing `A` and `B` still physically present after their pops, simply unreachable because `front` moved past them |
| `front == back` always means the queue is empty | Natural first guess, since both stacks (`top`) and simpler structures often use a single-pointer emptiness check | Activity 2 — distinguishing the `-1, -1` sentinel (truly empty) from `front == back` at a real index (exactly one element) |
| Once `back` reaches the last index, the queue is full, permanently | Feels like running off the end of an array is a hard stop | Activity 1's wraparound step — `back` reuses freed slots via modulo, so "reaching the end" isn't the same as "being full" |
| A circular array queue can grow without limit | Confusing this session's fixed-capacity array with next session's linked-list version | Explicit callout in Slide Block A: this implementation has a **fixed** capacity; unbounded growth is exactly what motivates Session 60 |
| The modulo wraparound is a special case that only applies sometimes | It only becomes *visible* in a dry run once, so it can look like an exception rather than the rule | State plainly: `(index + 1) % capacity` runs on *every single* push and pop, every time — it just happens to equal `index + 1` until the array boundary is actually reached |

---

## Instructor Notes

- **This session flips the entire mental model students have built for eight sessions of stacks.** Say the FIFO/LIFO contrast explicitly and often — it's easy for "just use a stack" muscle memory to intrude on the first few examples.
- **Activity 2 is the load-bearing activity** — the empty-vs-one-element ambiguity is the single most common real bug when students implement this later. Do not cut it; cut Activity 1 to its stated cut rule first if behind.
- **Have a physical or drawn circular array ready before class** (five boxes in a ring, or five boxes with an arrow looping the last back to the first) — the wraparound is far easier to land visually than verbally.
- **Bridge to Session 60 explicitly at the close:** "Every problem today came from one root cause — a *fixed-size* array. Next session removes that constraint entirely by switching to a linked list." This framing makes the next session's motivation obvious rather than arbitrary.
