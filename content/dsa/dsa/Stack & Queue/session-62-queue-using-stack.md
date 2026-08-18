# Session 62 — Queue Using Stack (Two Stacks)

**Duration** 50 min <!-- placement: inferred — two full approaches with an explicit trade-off comparison, plus this session's naming risk against Session 61 needs extra time, not less --> · **Topic** Stack & Queue — Implementation: Queue Using Two Stacks · **Prerequisite** Session 61 — Stack Using Queue
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Queue using two stacks | https://docs.google.com/presentation/d/1uYLbuFpzop3rCEcTbGBz38-ebPHFkdv9UhXjyTyXXBo/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the constraint of this problem — build a queue's FIFO behaviour using only two stacks — and explicitly distinguish it from Session 61's mirror-image problem. *(REMEMBERING)*
2. Explain both approaches: Approach 1 (transfer between stacks on every `push`) and Approach 2 (transfer lazily, only when needed for `pop`/`front`). *(UNDERSTANDING)*
3. Trace both approaches on the same operation sequence and confirm they produce identical results. *(APPLYING)*
4. Analyse the complexity trade-off in each approach, and decide which one suits a push-heavy workload versus a pop/front-heavy workload. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 61 (Stack Using Queue) (0–6 min)

Say: *"Six on Stack Using Queue — and pay close attention to the names today, because this session is its mirror image."*

**Q1.** In Session 61's Stack-using-Queue, what happens on every `push`?
`A` Nothing extra — direct enqueue · `B` The queue rotates so the new element reaches the front · `C` The queue is sorted · `D` Two queues are merged

**Q2.** In that implementation, `pop()` and `top()` run in:
`A` O(1) · `B` O(N) · `C` O(N²) · `D` O(log N)
→ *Read:* A. If this misses, restate it in one line — today's problem does the *same kind* of trade-off analysis, just with the costly and cheap operations swapped.

**Q3.** In that implementation, `push()` runs in:
`A` O(1) · `B` O(N) · `C` O(N²) · `D` O(log N)

**Q4.** The number of rotations on a given `push` equals:
`A` A fixed constant · `B` The number of elements already in the queue before this push · `C` The total capacity · `D` Always zero

**Q5.** The core lesson of Session 61 was:
`A` Queues are strictly better than stacks · `B` Simulating one structure with another moves the cost somewhere, it doesn't remove it · `C` Rotation is always free · `D` Stacks can't be simulated at all

**Q6 (MSQ — pick all correct).** Which are true of Session 61's approach?
`A` It uses exactly one queue · `B` The most recently pushed element ends up at the front of the queue · `C` It requires two separate queues · `D` `pop` and `top` never need to rotate

**Running it** — poll tool, ~25 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Say: *"Last session, one queue pretending to be a stack. Today, exactly the reverse: two stacks pretending to be a queue. Same spirit, opposite direction — and I want you to notice, by the end of today, that the exact same trade-off shows up again, just pointed the other way."*

Ask: *"With only `push` and `pop` available on a stack — LIFO — how would you ever get FIFO order out of it, using just stacks?"*

Let a guess or two land (often: "reverse it somehow"). Then:

> *"'Reverse it' is exactly right, and that's literally what a second stack does — popping everything off one stack and pushing it onto another flips the order. The only real decision left is *when* you do that flip: every single push, or only when you actually need to read the front? That single decision is the whole session."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 3–8: Introduction, Basic Operations, Approach 1 -->
Covers: Introduction (simulate FIFO queue behaviour using two LIFO stacks) → Basic Operations (`push`, `pop`, `front`, `back`, `size`, `empty`) → Approach 1: on every `push`, transfer all of `stk1` into `stk2`, push the new element onto `stk1`, then transfer everything back from `stk2` into `stk1`.

**Beats to emphasise**

- Say the two-stack roles plainly: "one stack briefly becomes a scratch space during the shuffle — neither stack is permanently 'the queue,' the roles swap temporarily on every operation in this approach."
- **State the mechanism as one sentence:** "moving everything to `stk2` and back, around the new element, re-sorts `stk1` so the oldest element ends up on top every single time — ready for an O(1) `pop` or `front`."
- Preview honestly: "this makes every `push` expensive — proportional to however many elements are already there — precisely so that `pop` and `front` stay cheap. Sound familiar?" (Bridge explicitly to Session 61's mirrored trade-off.)

**Checkpoint (at 17 min)** — cold-call:
> *"In Approach 1, after a `push` finishes, which element sits on top of `stk1` — the newest or the oldest?"*
> **Answer:** The oldest — the double-transfer specifically re-sorts the stack so the front of the queue is always immediately accessible.

---

## Slide Block B1 (17–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 9–34: Dry Run of push(1), push(2), push(3), front(), pop(), push(4); Pseudocode, Complexity, Code -->
Covers: Full dry run of `push(1), push(2), push(3), front(), pop(), push(4)` using Approach 1, showing the transfer-shuffle on every push → pseudocode → complexity (`push`: O(N); `pop`, `front`: O(1); `size`, `empty`: O(1)) → code.

**Beats to emphasise**

- Narrate `push(2)` exactly as the deck does: `stk1 = [1]`. Move `1` to `stk2` → `stk2 = [1]`. Push `2` onto `stk1` → `stk1 = [2]`. Move everything back from `stk2` to `stk1` → `stk1 = [2, 1]`, with `1` now on top.
- Continue to `push(3)`: move `2, 1` to `stk2` (in that pop order) → `stk2 = [2, 1]` becomes... walk the actual order carefully on the board, since this is where students lose track fastest. End state: `stk1 = [3, 2, 1]` top-to-bottom order such that `1` (the oldest) is on top.
- `front()` and `pop()` are then trivial: just read or remove `stk1`'s top, which the shuffling guaranteed is always the oldest element.

**Checkpoint (at 24 min)** — cold-call:
> *"Why does `front()` never need to touch `stk2` at all in this approach?"*
> **Answer:** Because `push`'s double-transfer already re-sorted `stk1` so the oldest (front) element is always sitting right on top — `front()` and `pop()` just read it directly, with no shuffling of their own.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Predict the Output: "Trace Approach 1 Yourself" (29–35 min)

**Format:** Predict-the-Output / Live Trace · **Exposes:** whether students can execute the double-transfer shuffle themselves on a fresh sequence — the one genuinely fiddly mechanical step in this session.

**Setup line (say this):**
> *"Fresh queue, Approach 1. Sequence: `push(A), push(B)`. After each push, tell me the full contents of `stk1`, top to bottom, before I confirm."*

Run **one push at a time**:

```
push(A) → stk1 empty → no transfer needed → push A directly.        stk1 (top→bottom): [A]
push(B) → move A to stk2 → stk2: [A].
          push B onto stk1 → stk1: [B].
          move A back from stk2 → stk1: [B, A]  (A now on top).     stk1 (top→bottom): [A, B]
```

**How it surfaces:** Ask before the final reveal: *"After both transfers, which one is on top of `stk1` — `A` or `B`?"* Correct: `A` — it was pushed first, so it's the "oldest," and the double-transfer's entire job is making sure the oldest ends up on top, ready for `front()`/`pop()`.

**Debrief line:**
> *"Two pushes, and already `A` — the first thing in — is sitting right where a queue's front needs to be. That's the shuffle working exactly as designed, at the cost of moving things twice on every single push."*

**Cut rule:** If running short, do only `push(B)` — it's the step with an actual shuffle; `push(A)` is a trivial base case.

---

## Slide Block B2 (35–42 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 35–63: Approach 2 (lazy transfer), Dry Run, Pseudocode, Complexity, Code, Note comparing both approaches -->
Covers: Approach 2 — `push` always goes straight onto `stk1`, no shuffling. `pop`/`front` check `stk2` first; if `stk2` is empty, transfer *everything* from `stk1` into `stk2` (reversing order), then operate on `stk2`'s top. If `stk2` already has elements, use it directly — no transfer needed.

**Beats to emphasise**

- Contrast directly with Approach 1: "Approach 1 pays the shuffle cost on every push, always. Approach 2 pays a *bigger* shuffle, but only sometimes — the moment `stk2` runs dry."
- Walk the deck's dry run: `push(1), push(2), push(3)` all go straight onto `stk1` — no work at all. Then `front()`: `stk2` is empty, so transfer everything from `stk1` (`3, 2, 1` pop order) into `stk2`, which reverses it to `[1, 2, 3]` bottom to top with `1` on top. `front()` then reads `1` directly.
- **Point at the deck's own explicit trade-off note:** "if you expect more `push` operations, favour Approach 2 (pushes stay cheap, individually). If you expect more `pop`/`front` operations, favour Approach 1 (those stay cheap, individually)." Neither approach is universally better — it depends on the workload.

**Checkpoint (at 42 min)** — cold-call:
> *"In Approach 2, once `stk2` has elements in it, does a second `front()` call right after the first one need to transfer anything again?"*
> **Answer:** No — as long as `stk2` still has elements, `front()`/`pop()` just read directly from it; the expensive transfer only happens again once `stk2` has been fully drained.

---

## ⚡ Activity 2 — Predict & Discuss: "Push-Heavy or Pop-Heavy?" (42–47 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students can reason about which approach fits a given workload, rather than treating "Approach 2" as a strictly better upgrade over "Approach 1."

**Setup line (say this):**
> *"Two scenarios. For each, which approach — 1 or 2 — would you pick, and why? One: a logging system that pushes thousands of events per second but only occasionally reads the oldest one. Two: a task scheduler that pushes one task, then immediately processes (`pop`s) it, over and over."*

**What students do:** Discuss in pairs for a minute, then share out.

**Answer:** Scenario 1 (push-heavy) → **Approach 2**: pushes stay O(1) individually; the expensive transfer only happens on the occasional read, and it's amortised across however many pushes preceded it. Scenario 2 (roughly balanced push/pop, alternating) → either approach behaves similarly here, but Approach 1's guaranteed O(1) `pop`/`front` is more predictable if reads are frequent and interleaved tightly with pushes.

**How it surfaces:** Push students past "just pick Approach 2, it sounds more efficient" — ask directly: *"If every single push is immediately followed by a pop, does Approach 2 still avoid doing the expensive transfer often?"* Walk through it: no — if `stk2` empties every time (because you `pop` right after each `push`), the transfer happens almost every time too, so Approach 2 loses its advantage in that specific pattern.

**Debrief line:**
> *"Neither approach is 'the right one' in general — they're two honest answers to two different questions about what your workload actually looks like. That's true of nearly every data-structure trade-off you'll meet from here on."*

**Cut rule:** If running short, cover only Scenario 1 and skip the interleaved-push-pop follow-up — the core lesson (workload shape determines the right approach) still lands from one scenario.

---

## Exit Ticket (47–50 min)

> In one sentence each: which operation is expensive in Approach 1, and which is expensive in Approach 2?
> **Answer:** Approach 1: `push` is expensive (O(N), double-transfer every time). Approach 2: `pop`/`front` are expensive *only when `stk2` is empty* (O(N) then, O(1) otherwise).

**Also confirm out loud, as a naming check:** *"Quick gut check — Session 61 built a ___ using a ___. Today we built a ___ using ___."* (Answers: a Stack, using a Queue; a Queue, using two Stacks.)

**Homework:** trace Approach 2 on `push(A), push(B), pop(), push(C), front(), pop(), pop()` by hand, tracking both stacks throughout. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Session 61 (Stack Using Queue) and Session 62 (Queue Using Stack) are the same problem with different names | Both involve simulating one structure with the opposite structure, and the names are easy to skim past | The Exit Ticket's explicit naming check, plus restating both problems' directions clearly in the Hook |
| Approach 2 is strictly better than Approach 1 since it "delays" the expensive work | "Delaying" sounds like avoiding, not just postponing | Activity 2 — the interleaved push/pop scenario, where Approach 2's delay buys nothing if `stk2` empties every time anyway |
| In Approach 2, the transfer from `stk1` to `stk2` happens on every `pop`/`front` call | Natural to assume "the expensive step" runs every time an expensive-sounding operation is called | Slide Block B2's checkpoint — the transfer only happens when `stk2` is empty; repeated calls afterward are O(1) |
| Both approaches need to shuffle *both* stacks back and forth every single time | Approach 1's double-transfer (there and back) primes this expectation | Contrast directly: Approach 2's transfer only ever goes one direction, `stk1 → stk2`, and only when needed |
| Choosing between Approach 1 and Approach 2 is arbitrary since both are "O(N) somewhere" | Surface-level reading stops once both are confirmed to have an O(N) operation somewhere | Activity 2 — the workload-shape reasoning is the actual decision criterion, not just spotting that an O(N) exists |

---

## Instructor Notes

- **The single biggest risk this session is students conflating it with Session 61.** Say the distinction out loud at least three times across the session: in the Hook, at a natural midpoint, and explicitly in the Exit Ticket. This is worth the repetition.
- **Activity 2 is the load-bearing activity, more than the mechanical trace.** The workload-dependent trade-off is the actual point of covering two approaches at all; don't let it get cut if time is short — cut Activity 1 to its stated cut rule first.
- **Walk Approach 1's dry run carefully, on the board, not just narrated.** The double-transfer (there and back) is genuinely easy to lose track of verbally; write out `stk1` and `stk2`'s contents at every single sub-step during Slide Block B1.
- **This is the final session of the Stack & Queue block.** If time allows, close with a one-line retrospective: two sessions ago, four different problems all reduced to "maintain one invariant with a stack" (Monotonic Stack → Next Greater Element → Asteroid Collision → Largest Rectangle); the last two sessions were the reverse move — using one structure to *simulate* the other entirely, and paying for it somewhere specific each time.
