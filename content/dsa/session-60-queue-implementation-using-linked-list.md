# Session 60 — Queue Implementation Using Linked List

**Duration** 40 min <!-- placement: inferred — shortest deck in the Queue pair (47 slides), directly builds on Session 59's concepts without a fixed-capacity complication --> · **Topic** Stack & Queue — Queue Implementation Using Linked List · **Prerequisite** Session 59 — Introduction to Queue & Implementation Using Arrays
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Queue Implementation using Linked List | https://docs.google.com/presentation/d/1J_6bbrNsBsWexmlP9v4tbCwRsT1gveOzPKhhQ73aCr0/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Explain why a linked-list-based queue has no fixed capacity, directly resolving last session's array limitation. *(REMEMBERING)*
2. Describe how `front` and `back` become node pointers rather than array indices, and why both still need explicit tracking. *(UNDERSTANDING)*
3. Trace `push` and `pop` operations on a linked-list queue, including the single-node special case where both `front` and `back` must be updated together. *(APPLYING)*
4. Compare the linked-list implementation against the array implementation — what's gained (no capacity limit), what's identical (all operations still O(1)). *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 59 (Queue: Array Implementation) (0–5 min)

Say: *"Six quick ones on the array-based queue before we swap the array out entirely."*

**Q1.** A queue follows which principle?
`A` LIFO · `B` FIFO · `C` Random access · `D` Priority order

**Q2.** In the array-based queue, `front == back` at a real index (not `-1, -1`) means:
`A` The queue is always empty · `B` The queue has exactly one element · `C` The queue is always full · `D` This state is impossible

**Q3.** The modulo wraparound (`back = (back + 1) % capacity`) exists to:
`A` Sort the elements · `B` Reuse freed slots at the front once the array's physical end is reached · `C` Double the array's size · `D` Detect errors

**Q4.** What is the biggest limitation of the array-based queue?
`A` It's slower than a linked list for `push` · `B` It has a fixed maximum capacity · `C` It can't track a `front` pointer · `D` It doesn't support `pop`
→ *Read:* B. If this misses, restate it in one line — it's the entire motivation for today's session, not a footnote.

**Q5.** `push`, `pop`, and `front` on the array-based queue all run in:
`A` O(1) · `B` O(n) · `C` O(n²) · `D` O(log n)

**Q6 (MSQ — pick all correct).** Which are true of the array-based queue from last session?
`A` It uses two pointers, `front` and `back` · `B` It needs a `-1, -1` sentinel for the empty state · `C` It can grow without any upper bound · `D` `pop()` only moves `front`, it doesn't physically clear the slot

**Running it** — poll tool, ~25 s per question. Total 5 min including reads.

---

## Hook (5–8 min)

Ask: *"A shared office printer has a print queue. Someone can send a 200-page print job at 2 AM, and someone else can send one job every minute all day. Should that queue ever say 'sorry, full, try again later'?"*

Let students answer (no — a print queue shouldn't have an arbitrary cap). Then:

> *"Last session's array-based queue had exactly that flaw — a fixed capacity, chosen up front, that could genuinely run out even with plenty of memory left on the machine. Today's fix is almost embarrassingly simple: swap the array for a linked list. Same FIFO behaviour, same O(1) operations, no capacity ceiling at all."*

---

## Slide Block A (8–15 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–6: Node Structure, front/back pointers -->
Covers: Node structure (`data` + `next` pointer) → `front` and `back` become **node pointers**, not array indices, both initialised to `null` for an empty queue.

**Beats to emphasise**

- Say the direct mapping explicitly: "everything from last session still applies — you still need a `front` for removal and a `back` for insertion. The only thing that changed is *what kind of thing* `front` and `back` point to: a node, not a slot number."
- No capacity, no modulo, no wraparound — say this as a relief, not a new complexity: "the entire reason last session needed circular index arithmetic simply doesn't exist here, because nodes are allocated on demand."
- `front = null, back = null` is this session's version of last session's `-1, -1` sentinel — same purpose, different representation.

**Checkpoint (at 15 min)** — cold-call:
> *"What replaces the array index arithmetic from last session's `push` and `pop`?"*
> **Answer:** Nothing needs replacing with arithmetic at all — `push` just links a new node after `back` and moves `back` to it; `pop` just moves `front` to `front->next`. No modulo, no capacity check.

---

## Slide Block B (15–21 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 7–28: push/pop dry run, Pseudocode, Complexity Analysis -->
Covers: Full dry run — `push(1), push(2), push(3), push(4)`, then `pop()`, `front()`, several more `pop()`s down to empty — using node links throughout → pseudocode for `push`/`pop`/`front`/`back`/`size`/`empty` → complexity (all O(1)).

**Beats to emphasise**

- Narrate `push(1)` as the special case: queue was empty (`front = back = null`), so the new node becomes *both* `front` and `back` at once. Every push after that just does `back->next = newNode; back = newNode`.
- Narrate the pop sequence down to the **last** element as the other special case: when `pop()` empties the queue entirely (`size` reaches `0`), *both* `front` and `back` must be reset to `null` — updating only `front` would leave `back` dangling, pointing at a deleted node.
- Complexity: every operation is O(1) — no loops, no scans, just pointer reassignment.

**Checkpoint (at 21 min)** — cold-call:
> *"When the very last element is popped, why do we need to set *both* `front` and `back` to null, instead of just `front`?"*
> **Answer:** If only `front` is reset, `back` still points at the node that was just deleted — a dangling pointer. With zero elements left, both pointers need to agree the queue is empty.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Push and Pop by Pointer" (26–32 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can track `front` and `back` as they move between actual nodes, rather than array indices — the one genuinely new mental model this session introduces.

**Setup line (say this):**
> *"Empty queue. Sequence: `push(A)`, `push(B)`, `push(C)`, `pop()`, `pop()`. After each step, tell me what `front` and `back` point to — before I confirm."*

Run **one operation at a time**:

```
push(A) → queue was empty → front = back = node(A).             front→A←back
push(B) → back->next = node(B); back = node(B).                 front→A→B←back
push(C) → back->next = node(C); back = node(C).                 front→A→B→C←back
pop()   → front = front->next = node(B).                        front→B→C←back
pop()   → front = front->next = node(C). Now front == back == node(C).   front→C←back
```

**How it surfaces:** After the second `pop()`, ask before revealing: *"`front` and `back` now point at the same node — does that mean the queue is empty?"* Correct: no — it means exactly one element remains (`C`), the same "front equals back, one element" case from last session's Activity 2, just expressed as pointers instead of indices.

**Debrief line:**
> *"Same underlying situations as last session — 'one element left' and 'completely empty' are still two different states that happen to look similar. The representation changed from indices to pointers; the logic you have to get right didn't."*

**Cut rule:** If running short, do just `push(A)` and the second `pop()` — one shows the empty-queue special case, the other shows the one-element state.

---

## ⚡ Activity 2 — Spot the Bug: "The Missing Reset" (32–37 min)

**Format:** Spot the Bug · **Exposes:** the single most common real bug in this implementation — forgetting to reset `back` to `null` when the last element is popped, leaving it dangling.

**Setup line (say this):**
> *"Here's a `pop()` implementation with a bug. Find it."*

```
pop() {
    if (size == 0) { print("Queue is empty"); return }
    Node* temp = front
    front = front->next
    delete temp
    size--
}
```

**What students do:** 45 seconds silent, then hands up.

**Answer:** This version *always* just does `front = front->next`, even when popping the very last element. When `front` was the only node, `front->next` is `null`, so `front` correctly becomes `null` — but `back` was never touched, and still points at the now-deleted node. `back` is left dangling.

**How it surfaces:** Ask: *"If I call `push(x)` right after this buggy `pop()` empties the queue, what happens?"* Walk through it: `push` checks `if (size == 0)` to decide whether to set `front = back = newNode` — but `size` was correctly decremented to `0`, so this particular bug is masked *this time*. Then ask: *"What if `push`'s empty-check used `back == null` instead of `size == 0`?"* Now it breaks — `back` isn't null, so `push` thinks the queue isn't empty and tries to do `back->next = newNode` on a dangling pointer.

**Debrief line:**
> *"The fix: when `size` reaches `0` after a pop, explicitly set both `front = null` and `back = null`. Don't rely on one pointer's state to imply the other's — say it directly, every time."*

**Cut rule:** If running short, state the bug and its fix directly rather than running the "what if push used `back == null`" extension — the core lesson (reset both pointers together) still lands.

---

## Exit Ticket (37–40 min)

> A linked-list queue has exactly one node. You call `pop()`. What must happen to `front` and `back`, and why both?
> **Answer:** Both must be set to `null`. If only `front` is reset, `back` still points at the deleted node — a dangling pointer that will corrupt the next `push`.

**Homework:** trace `push(10), push(20), pop(), push(30), pop(), pop()` on a linked-list queue by hand, drawing `front`/`back` after every step. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Only `front` needs updating when popping the last element | The pop operation is mentally centred on `front`, since that's the end being removed | Activity 2 — showing `back` left dangling if it isn't explicitly reset alongside `front` |
| `front == back` always means the queue is empty | Direct carryover from arrays, where index equality can feel like an "empty" signal | Activity 1's second `pop()` — `front == back` here means exactly one node remains, not zero |
| The linked-list queue is fundamentally a different algorithm from the array queue | New pointer-based syntax makes the operations look unfamiliar | Slide Block A's direct mapping — same FIFO behaviour, same two-pointer design, only the storage mechanism changed |
| A linked-list queue can never run out of space | Technically bounded only by system memory, but students sometimes take "unbounded" too literally | Brief, honest caveat: it's not capacity-*checked* like the array version, but it isn't infinite — it can still fail if memory is genuinely exhausted |
| `push` needs a special case check every time, not just when the queue starts empty | The array version's modulo logic ran on every push, priming an expectation of per-call special-casing | State plainly: after the very first push, every subsequent push is the identical two-line operation — no ongoing special case |

---

## Instructor Notes

- **This is a fast, mechanically simple session once the array-based mental model has been set aside** — the FIFO logic doesn't change at all, only the storage. Don't over-explain; lean on the direct one-to-one mapping from Session 59.
- **Activity 2 is the load-bearing activity** — the dangling-`back` bug is the single most common real-world bug in a hand-rolled linked-list queue. Do not cut it; cut Activity 1 to its stated cut rule first if behind.
- **If students are shaky on linked-list pointer mechanics generally** (not queue-specific, just pointers), this session will expose that gap fast — a 60-second refresher on "a pointer holds an address, not a copy of the data" may be worth inserting before Slide Block A if the room seems unsure.
- **This closes out the Queue pair.** If time allows at the end, state explicitly which one an instructor would actually reach for in practice: the linked-list version, almost always — the fixed-capacity array version is mainly a teaching stepping stone toward appreciating why the linked-list version is better.
