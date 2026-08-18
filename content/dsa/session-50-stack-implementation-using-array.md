# Session 50 — Stack Implementation Using Array

**Duration** 60 min <!-- placement: inferred — first implementation session; two full dry runs (push to overflow, pop to underflow) plus pseudocode and dual-language code walkthrough don't fit comfortably in 45 --> · **Topic** Stack & Queue — Array-Based Stack · **Prerequisite** Session 49 — Intro of Stack
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Stack Implementation Using Array | https://docs.google.com/presentation/d/1yXTQK1E67BYC8PsZRrCkNhzsfsYxdd47Qa4-4N9_pG8/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State that an array-based stack needs a fixed-size array plus a `top` index initialised to `-1`. *(REMEMBERING)*
2. Trace `push` and `pop` on an array stack, correctly updating `top` *before* inserting and *after* removing. *(APPLYING)*
3. Identify the overflow condition (`top == capacity - 1` on push) and the underflow condition (`top == -1` on pop) from code or a described scenario. *(ANALYZING)*
4. Implement `push`, `pop`, `top`, `empty`, `full`, and `size` as O(1) array-index operations. *(APPLYING)*
5. Weigh the advantages (direct O(1) access, simple to implement) against the disadvantages (fixed capacity, wasted memory, expensive resize) of an array-backed stack. *(EVALUATING)*

---

## Warm-Up Poll — Retrieval Practice on Session 49 (Intro of Stack) (0–6 min)

Say: *"Seven questions on stacks before we build one for real."*

**Q1.** What ordering principle does a stack follow?
`A` FIFO · `B` LIFO · `C` Random · `D` Priority-based

**Q2.** Insertion and deletion in a stack both happen at:
`A` The front · `B` The back · `C` The top · `D` Anywhere you like
→ *Read:* If anyone says "the back," they're blending Session 49's stack with the queue they haven't met yet. Flag it and move on — Queue arrives at Session 59.

**Q3.** Which real-world example did we use to explain LIFO?
`A` A queue at a ticket counter · `B` A stack of plates · `C` A deck of shuffled cards · `D` A phone book

**Q4.** A **dynamic-size** stack, compared to a fixed-size one:
`A` Has a set capacity that never changes · `B` Automatically grows when full and can shrink when elements are removed · `C` Is always slower · `D` Cannot underflow

**Q5.** Pushing onto a fixed-size stack that is already full causes:
`A` Underflow · `B` Overflow · `C` A silent no-op, no error · `D` The stack to auto-resize

**Q6.** Popping from an empty stack causes:
`A` Underflow · `B` Overflow · `C` A silent no-op, no error · `D` The stack to auto-resize
→ *Read:* Q5 and Q6 are the two terms today's session leans on hardest — if the split isn't near-unanimous, put both words on the board before moving on.

**Q7 (MSQ — pick all correct).** Which stack operations run in O(1) time?
`A` push · `B` pop · `C` top · `D` size
→ *Read:* Correct answer is all four. This is the whole promise of today's implementation — if the array design breaks this, we've done something wrong.

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Put a plain array on the board: 5 boxes, indices 0–4, all empty. Ask:

> *"An array has no built-in idea of 'top.' If all I give you is this array, how do *you* know which box holds the top of the stack?"*

Take 2-3 guesses (someone will say "the last box that isn't empty," someone else may say "check every box"). Then:

> *"Both of those work, but both are wasteful — you'd be scanning the array every single time. Today's entire implementation is one clever trick: keep a single extra number, called `top`, that always tells you exactly where the top is. Update that one number correctly, and every operation becomes instant. Get it wrong by one, and you'll either overwrite good data or read garbage. That's the whole session."*

---

## Slide Block A (9–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–15: Stack Introduction, Array Initialization, Top Pointer Initialization, Push Operation dry run (10, 20, 30) -->
Covers: Array-backed stack introduction → Array initialisation (size 5, empty) → `top` initialised to `-1` → Push dry run: `push(10)`, `push(20)`, `push(30)`.

**Beats to emphasise**

- **`top = -1` means empty, not "index -1 has something."** Say this explicitly — it's a sentinel value, not a real index.
- **The push sequence is always increment-then-insert, in that order.** `top = top + 1`, *then* `arr[top] = x`. Say the order out loud every single time you narrate a push in this block — this is the exact rule Activity 1 will test.
- Narrate `push(10)`: top goes from `-1` to `0`, `arr[0] = 10`. Then `push(20)`: top `0 → 1`, `arr[1] = 20`. Then `push(30)`: top `1 → 2`, `arr[2] = 30`. Three reps is enough for the rule to land before you ask students to do it themselves.

**Checkpoint (at 19 min)** — cold-call:
> *"Capacity-5 array, currently holds [10, 20, 30] with top = 2. I call push(99). What are the two things that happen, in order?"*
> **Answer:** `top` becomes 3 first, *then* `arr[3] = 99`.

---

## ⚡ Activity 1 — Live Trace: "Increment, Then Insert" (19–25 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students have internalised increment-before-insert on push, and decrement-after-inspect on pop — the exact off-by-one gap that produces real bugs in the code slides later in this session.

**Setup line (say this):**
> *"Same capacity-5 array, starting empty, top = -1. I call an operation, you tell me the new value of `top` *before* you tell me what's stored where — say it in that order every time."*

Call these out **one at a time**, taking an answer before confirming (this continues the deck's own numeric example):

```
push(10)   → top: -1 → 0,  arr[0] = 10
push(20)   → top: 0 → 1,   arr[1] = 20
push(30)   → top: 1 → 2,   arr[2] = 30
top()      → returns arr[2] = 30, top unchanged (still 2)
pop()      → top: 2 → 1,   (30 is discarded — arr[2] is now stale, ignore it)
pop()      → top: 1 → 0,   (20 is discarded)
push(40)   → top: 0 → 1,   arr[1] = 40
push(50)   → top: 1 → 2,   arr[2] = 50
```

**How it surfaces:** After the two `pop()` calls, ask: *"Is the old value still physically sitting in the array at index 2 and index 1?"* Correct answer: yes, but it's irrelevant — `top` says it doesn't exist anymore. This is the detail students miss and it matters for the overflow discussion coming up.

**Common wrong answer:** students say `pop()` "clears" or "deletes" the array slot. Correct it by pointing out the pseudocode never zeroes anything out — only `top` moves.

**Debrief line:**
> *"The array never gets cleaned up. `top` is the only source of truth for what's 'really' in the stack. Everything below and including `top` is live; everything above it is garbage nobody reads."*

**Cut rule:** If running short, drop the two `push` calls at the end (40, 50) — the increment/decrement rule is already demonstrated by the first six operations.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (30–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 16–55: Top Operation, continued Push dry run (40, 50, 60, 70), Overflow Condition, isFull, Pop dry run to empty, isEmpty, Underflow Condition -->
Covers: `top()` operation (read-only) → continued push dry run up to capacity → **Overflow condition** (`push(80)` on a full capacity-5 stack) → `isFull` → full pop dry run down to `top = -1` → **Underflow condition** → `isEmpty`.

**Beats to emphasise**

- **`top()` never touches `top`.** It only reads `arr[top]`. Contrast this explicitly against `pop()`, which both reads *and* decrements — this pairs directly with Activity 1's debrief.
- **Overflow, precisely:** the deck pushes 10, 20, 40, 60, 70 into a capacity-5 stack (already at `top = 4`), then attempts `push(80)`. Narrate: "top is already at index 4, the last valid index — there is nowhere to increment to." This is the exact scenario Activity 2 will hand back to students.
- **`isFull`** is one comparison: `top == capacity - 1`. Say it as a sentence, not just a formula: "full means the top pointer has reached the last valid index."
- **Underflow, precisely:** popping when `top == -1` — there's nothing to decrement from. Narrate the full pop-down sequence from the deck (five pops in a row until the stack is empty) without skipping steps — the rhythm of "check, decrement, done" repeating five times is what makes the rule automatic.
- **`isEmpty`** is `top == -1` — the mirror image of `isFull`. Put both conditions on the board side by side.

**Checkpoint (at 42 min)** — show of hands:
> *"Capacity-5 stack, top = 4 (full). I call `isFull()` then immediately `push(100)`. What does each call return/do?"*
> **Answer:** `isFull()` returns `true`. `push(100)` fails — overflow, `top` does not change, `100` is never written into the array.

---

## ⚡ Activity 2 — Predict-the-Output: "Will It Overflow?" (42–48 min)

**Format:** Predict-the-Output · **Exposes:** whether students can tell overflow apart from a normal push using nothing but the current value of `top` and the capacity — taken directly from the deck's own overflow example.

**Setup line (say this):**
> *"Capacity-5 stack. I'm going to describe its current state, then propose an operation. Before I tell you what happens, thumbs up for 'succeeds,' thumbs down for 'overflow.'"*

Run these **one at a time** (all lifted from the deck's own dry run):

1. Stack holds `[10, 20, 40, 60]`, `top = 3`. Propose: `push(70)`. → **Succeeds** (`top` becomes 4, the last valid index).
2. Stack holds `[10, 20, 40, 60, 70]`, `top = 4`. Propose: `push(80)`. → **Overflow.** `top` is already at `capacity - 1 = 4`; there's no room.
3. Stack holds `[10, 20, 40, 60, 70]`, `top = 4`. Propose: `pop()`. → **Succeeds** (`top` becomes 3, `70` is discarded).
4. Stack holds `[]`, `top = -1`. Propose: `pop()`. → **Underflow.** Nothing to remove.

**How it surfaces:** For case 2, ask someone to state *why* in one sentence before you confirm — "top is at the last index, capacity 5 means valid indices are 0 to 4, there's no index 5." Case 4 gets the same treatment for underflow.

**Common wrong answer:** students confuse `top = capacity - 1` (full) with `top = capacity` (which would be an actual out-of-bounds index and never legitimately occurs if the guard works). Clarify: the guard's entire job is to make sure `top` never reaches `capacity`.

**Debrief line:**
> *"Overflow and underflow are just the same guard, mirrored: one checks you're not past the top of the array, the other checks you're not past the bottom of the stack's contents. Both are a single `if` statement — you'll see that in the pseudocode in a minute."*

**Cut rule:** If running short, run cases 2 and 4 only — those are the two failure modes; cases 1 and 3 are the "normal" cases already covered in Slide Block A/Activity 1.

---

## Slide Block C (48–55 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 58–77: Pseudocode (push/pop/top/empty/full/size), Complexity Analysis, C++ Code, Python Code, Advantages, Disadvantages, Key Takeaways -->
Covers: Pseudocode for `push`, `pop`, `topElement`, `empty`, `full`, `size` → Complexity table (all O(1) time and space) → C++ implementation → Python implementation → Advantages / Disadvantages → Key Takeaways.

**Beats to emphasise**

- Read the guard clauses in the pseudocode aloud exactly as written: `push` opens with `if (top == capacity - 1) { return }` — **the guard comes first**, before any increment. Same for `pop`: `if (top == -1) { return }` before any decrement. This ordering is the difference between a safe implementation and the corrupted one in Activity 3.
- The C++ and Python code (slides 64–75) are the same logic in two syntaxes — don't re-teach the algorithm, just point out the direct line-for-line correspondence to the pseudocode already covered.
- **Advantages:** O(1) direct access via indexing, genuinely simple to implement.
- **Disadvantages:** fixed capacity risks overflow; resizing an array (when you eventually need to) means allocating a new array and copying everything over — expensive; and a stack that never fills up wastes the unused capacity the whole time.

**Checkpoint (at 55 min)** — cold-call:
> *"Name one advantage and one disadvantage of implementing a stack with an array, in your own words."*
> **Answer (any reasonable phrasing):** Advantage — O(1) direct access, simple. Disadvantage — fixed capacity causes overflow, or resizing is costly.

---

## ⚡ Activity 3 — Spot the Bug: "The Missing Guard" (55–58 min)

**Format:** Spot the Bug · **Exposes:** whether students understand *why* the overflow/underflow guards exist, by seeing what breaks without them — grounded directly in the deck's own `push`/`pop` pseudocode with the guard clause deleted.

**Setup line (say this):**
> *"Here's `push`, with one line deleted. Tell me what goes wrong, and give me a concrete case where it breaks."*

Put this on screen:

```python
def push(self, num):
    self.top += 1
    self.arr[self.top] = num
    # (the capacity check that used to be here is gone)
```

**What students do:** 30 seconds silent, then hands up.

**Answer:** With a capacity-5 array (valid indices 0–4) already full (`top = 4`), calling `push(6th value)` increments `top` to `5` and then attempts `self.arr[5] = num` — an out-of-bounds write. In Python this raises an `IndexError`; in a language like C++ without bounds checking, it can silently corrupt adjacent memory instead of failing loudly.

**How it surfaces:** Ask, "Which is worse — Python's crash, or C++'s silent corruption?" Most will correctly say silent corruption is worse — you don't even get an error to tell you something went wrong.

**Debrief line:**
> *"The guard clause isn't decoration — without it, overflow doesn't just fail politely, it can write past the end of your array and corrupt whatever's sitting in memory next to it. Always check capacity before you touch the array, never after."*

**Cut rule:** If running short, skip the "which is worse" discussion and go straight from the bug identification to the debrief line.

---

## Exit Ticket (58–60 min)

> Capacity-3 stack, empty (`top = -1`). Write out `top`'s value after each of these calls, in order: `push(5)`, `push(9)`, `push(2)`, `push(7)`.
> **Answer:** `0, 1, 2, ` — the fourth `push(7)` **overflows** and `top` stays at 2 (capacity is 3, valid indices 0–2, already full).

Scan responses on the way out. If several students write `top = 3` for the last call, that's the overflow guard not sticking — reopen Session 51 with a 60-second recap of the guard-before-increment rule.

**Homework:** re-attempt the exit-ticket sequence from memory, then extend it with two more pushes and a pop, and trace `top` by hand. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| `pop()` clears/zeroes the array slot it removed | "Removing" sounds like it should erase data | Activity 1's debrief — pointing out the old value is still physically there, just unreachable because `top` moved past it |
| Push inserts first, then increments `top` | The English sentence "push adds an element" sounds like the add happens before any bookkeeping | Narrating every push in Slide Block A as "increment, *then* insert," in that fixed order, every single time |
| Overflow means the array itself is out of memory | "Overflow" sounds like a memory-level failure | Precisely defining it as `top == capacity - 1` — a logical full stack, not a hardware limit |
| `isFull()`/`isEmpty()` change the stack's state | Function calls with "is" in the name still feel like actions to new programmers | Point out they only read `top` and return a boolean — nothing is written |
| A missing overflow guard just "does nothing" safely | Students assume unchecked code degrades gracefully | Activity 3 — showing the concrete out-of-bounds write and its two very different failure behaviours across languages |

---

## Instructor Notes

- **This is a code-heavy, dry-run-heavy session — protect the pacing.** The deck spends roughly 50 of its ~79 slides on step-by-step push/pop animations. Deliver them at a brisk narrate-don't-linger pace; the two activities exist precisely so students internalise the pattern actively instead of watching 50 near-identical slides passively.
- **Increment-before-insert and check-before-decrement are the two rules that matter.** Everything else in this session — overflow, underflow, isFull, isEmpty — is a restatement of one of these two rules from a different angle. If you only have time to drill one thing, drill this.
- **Don't let the C++/Python code slides turn into a syntax lesson.** They're there to show the pseudocode translates directly into working code in two languages — a 90-second walkthrough, not a re-teach.
- **60 minutes is tight with 3 activities.** If you're behind by the time you reach Activity 3, cut it entirely per its cut rule guidance and move straight to the Exit Ticket — Activities 1 and 2 already cover the load-bearing concepts (increment/decrement discipline, overflow/underflow recognition).
- **Set up next session's contrast now.** Closing line for this session, if time allows: "Everything that made this fast — direct indexing — is also what makes it inflexible. Next session, same data structure, opposite tradeoffs: a linked list."
