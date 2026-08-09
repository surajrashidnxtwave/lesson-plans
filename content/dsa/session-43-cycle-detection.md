# Session 43 — Cycle Detection

**Duration** 45 min <!-- placement: inferred — standard length; two approaches (hash-set + Floyd's), each with a full dry run, matches this block's typical pacing --> · **Topic** Linked List — Cycle Detection · **Prerequisite** Session 42 — Reversals
**Session type** Concept lecture

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Cycle Detection | https://docs.google.com/presentation/d/1OWc0tSGS1Viiq3SMKUQ6Wy01O2hWn_-W-RMFtmCJ0d0/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the problem: given a singly linked list, determine whether it contains a cycle (some node points back to a previous node). *(REMEMBERING)*
2. Explain the brute-force approach — traverse the list, tracking every visited node's address in a set, and detect a cycle the moment a node repeats. *(UNDERSTANDING)*
3. Trace Floyd's Tortoise-and-Hare approach: two pointers, `slow` moving one step and `fast` moving two steps per iteration, meeting if and only if a cycle exists. *(APPLYING)*
4. Explain *why* `slow` and `fast` are guaranteed to meet inside a cycle — the fast pointer gains one step on the slow pointer every iteration, so it cannot "jump over" it. *(ANALYZING)*
5. Contrast the brute force's `O(n)` time / `O(n)` space against Floyd's `O(n)` time / `O(1)` space, and state precisely what the two-pointer trick eliminates. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 42 (Reversals) (0–6 min)

Say: *"Six on yesterday's list-reversal techniques, then we take on a genuinely new question: not 'reverse it,' but 'does it even end?'"*

**Q1.** Reversing a singly linked list in place requires tracking how many pointers per node, at minimum?
`A` One · `B` Two (previous and next) · `C` Three · `D` None — it can be done with no extra pointers

**Q2.** What is the time complexity of an iterative linked-list reversal?
`A` `O(1)` · `B` `O(n)` · `C` `O(n log n)` · `D` `O(n²)`
→ **Answer:** B.

**Q3.** What is the space complexity of an *iterative* reversal, versus a *recursive* one?
`A` Both `O(1)` · `B` Iterative `O(1)`, recursive `O(n)` (call stack) · `C` Both `O(n)` · `D` Iterative `O(n)`, recursive `O(1)`
→ **Answer:** B.

**Q4.** After fully reversing a singly linked list, what happens to the original head node's `next` pointer?
`A` It stays unchanged · `B` It becomes `null`, since the original head is now the tail · `C` It points to itself · `D` It's deleted

**Q5.** True or False: reversal requires re-allocating new nodes.
`A` True · `B` False — pointers are re-wired in place, no new nodes are created
→ **Answer:** B.

**Q6 (MSQ — pick all correct).** Which are true about traversing a singly linked list?
`A` You can only move forward, never backward, without an extra structure · `B` `next` on the last node is normally `null` · `C` A `null` next pointer guarantees the list has no cycle-related complications · `D` Traversal cost is `O(n)` for a list of `n` nodes

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Ask: *"If I hand you the head of a linked list and just say 'traverse it and print every value until you hit null' — is there any way that could go wrong, even though the instruction sounds completely safe?"*

Let students think — most won't immediately see it. Then:

> *"What if the list never actually reaches `null`, because some node further down points back to an earlier node, looping forever? Your traversal would run infinitely, and you'd have no way to know it, from the inside, without a way to detect that repetition. That's today's entire problem: before you trust *any* traversal, how do you know it will actually end?"*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–28: Problem Statement, Examples, Brute Force Approach, full Dry Run, Pseudocode, Complexity, Code -->
Covers: problem statement (detect whether a singly linked list contains a cycle; a cycle occurs when a node points to a previous node, forming a loop) → Example 1 (list with a loop → `True`) → Example 2 (list ending in `null` → `False`) → brute force: traverse the list, tracking every visited node's address in a set; if a node is found already in the set, a cycle exists → full dry run on an 8-node list where the last node loops back to an earlier node: traverse adding each node's address to the set one at a time, until the traversal reaches a node whose address is already present — cycle detected → pseudocode → complexity (`O(n)` time — each node visited at most once; `O(n)` space — the set stores every visited node's address) → C++/Python code.

**Beats to emphasise**

- State the brute force in one line: *"walk the list, and remember every node's address you've seen — the moment you're asked to visit an address you've already recorded, you've found the loop."*
- **Say explicitly why this uses node *addresses*, not node *values*:** two different nodes can hold the same value, so comparing values could produce a false cycle detection. The set must track node identity — which address was visited — not what number sits inside it.
- Complexity: `O(n)` time because each real node is visited exactly once before either a cycle is found or the list legitimately ends; `O(n)` space because the set can grow to hold every node in the list before a cycle (if any) is found.

**Checkpoint (at 18 min)** — cold-call:
> *"Why does the brute-force approach check node *addresses* in the set, rather than the *values* stored in each node?"*
> **Answer:** Different nodes can legitimately hold identical values without any cycle existing. Only comparing addresses (node identity) correctly distinguishes "I've revisited this exact node" from "I've seen this same number before at a different node."

---

## Slide Block B (18–27 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 29–45: Optimal Solution (Floyd's Hare-Tortoise), Approach, full Dry Run, Pseudocode, Complexity, Code -->
Covers: optimal approach — Floyd's Cycle Detection Algorithm (Tortoise and Hare): initialize two pointers, `slow` and `fast`, both at `head`; move `slow` one step and `fast` two steps per iteration; if they ever point to the same node, a cycle exists; if `fast` (or `fast->next`) reaches `null`, no cycle exists → full dry run on the same 8-node looping list, tracking `slow` and `fast`'s positions step by step until they land on the identical node → pseudocode → complexity (`O(n)` time — the two pointers meet within at most `n` steps if a cycle exists; `O(1)` space — only two pointer variables, no set or map) → C++/Python code.

**Beats to emphasise**

- **Say the mechanism as one sentence, this is the whole session:** *"one pointer moves at normal speed, a second pointer moves twice as fast — if there's a loop, the fast pointer will eventually lap the slow one and land on the exact same node; if there's no loop, the fast pointer simply falls off the end first."*
- **Say explicitly why they're guaranteed to meet, not just likely to:** once both pointers are inside the cycle, the *gap* between them (in nodes) shrinks by exactly one every iteration, because `fast` gains one extra step on `slow` each time. A shrinking integer gap that starts positive must eventually hit zero — it can't skip over zero, since the maximum step-size difference per iteration is exactly one.
- Contrast directly against Slide Block A: **zero extra data structures** — no set, no map, just two pointer variables — which is exactly why space drops from `O(n)` to `O(1)`.

**Checkpoint (at 27 min)** — cold-call:
> *"Why can't the fast pointer 'jump over' the slow pointer and miss it entirely, once both are inside the cycle?"*
> **Answer:** The gap between them shrinks by exactly one node per iteration (fast gains 2 steps, slow gains 1, net gain of 1 toward closing the gap). A gap that decreases by exactly one each step, starting from some positive integer, must pass through every smaller value on its way down — including zero — so it can never skip past the meeting point.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "Slow and Fast, Step by Step" (32–38 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can track two independently-moving pointers themselves, rather than only having watched the deck's single worked example.

**Setup line (say this):**
> *"Six-node list: `A → B → C → D → E → F`, and `F` points back to `C`. Starting both `slow` and `fast` at `A`. After each iteration, tell me where each pointer is — before I confirm."*

Run **one iteration at a time**:

```
Start:        slow=A, fast=A
Iteration 1:  slow=B, fast=C
Iteration 2:  slow=C, fast=E
Iteration 3:  slow=D, fast=C   (fast wrapped: E → F → C)
Iteration 4:  slow=E, fast=E   → MEET — cycle detected
```

**How it surfaces:** At Iteration 3, ask before revealing: *"Fast was at `E` — where does it go after two more steps, given `F` loops back to `C`?"* Correct: `E → F`, then `F → C` (following the loop-back), landing at `C` — not falling off the list, since `F`'s `next` is `C`, not `null`.

**Debrief line:**
> *"They meet at `E` on iteration 4 — inside the cycle, exactly as guaranteed. Notice fast doesn't need to 'know' where the cycle is; it just keeps moving twice as fast as slow, and the meeting happens naturally once both are looping."*

**Cut rule:** If running short, do only iterations 1–2, then state the final meeting point directly — the mechanism is demonstrated either way.

---

## ⚡ Activity 2 — Predict & Discuss: "What If There's No Cycle?" (38–43 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students understand the algorithm's *other* branch — correctly recognizing termination — not just the cycle-found case.

**Setup line (say this):**
> *"Five-node list, no cycle: `A → B → C → D → E → null`. Trace `slow` and `fast` together. What happens, and how does the algorithm know to return `false`?"*

**What students do:** Discuss for a minute, then trace together: `slow=A,fast=A` → `slow=B,fast=C` → `slow=C,fast=E` → next step, `fast` needs to move from `E`, but `E->next` is `null` — the loop condition `fast != null && fast->next != null` fails, so the loop exits and the algorithm returns `false`.

**How it surfaces:** Ask a follow-up: *"Why does the loop check both `fast != null` AND `fast->next != null`, instead of just one of them?"* Push toward: `fast` moves two steps per iteration, so before advancing it, the algorithm must confirm *both* the current node and the very next node exist — otherwise `fast->next->next` could dereference a `null` pointer and crash.

**Debrief line:**
> *"A two-pointer trick that moves at different speeds has to be equally careful about *both* ways it can end — meeting (cycle found) and running off the end (no cycle) — and the loop condition has to protect against crashing while checking for the second case."*

**Cut rule:** If running short, state the termination condition and its reasoning directly, skipping the open trace.

---

## Exit Ticket (43–45 min)

> Four-node list: `W → X → Y → Z`, and `Z` points back to `X`. Trace `slow` and `fast` from `W` and state which node they meet at.
> **Answer:** `slow=W,fast=W` → iter 1: `slow=X, fast=Y` → iter 2: `slow=Y, fast=X` (fast moves `Y→Z`, then `Z→X` via the loop-back) → iter 3: `slow=Z, fast=Z` (slow moves `Y→Z`; fast moves `X→Y→Z`) → **meet at `Z`.** <!-- placement: inferred exit-ticket list, built as a minimal 4-node cycle to keep the trace short while still requiring the loop-back wraparound step -->

**Homework:** trace Floyd's algorithm on a 7-node list `P→Q→R→S→T→U→V` where `V` points back to `R`, reporting the meeting node. <!-- placement: inferred — no homework/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The brute-force set should track node *values*, not addresses | Values are what students usually compare when checking "have I seen this before" | Slide Block A's checkpoint — two distinct nodes can share a value with no cycle present; only address identity is safe |
| The fast pointer might "jump over" the slow pointer and miss meeting it | Feels intuitively possible since fast moves twice as far each step | Slide Block B — the gap-shrinks-by-exactly-one argument, showing the gap can't skip past zero |
| Floyd's algorithm needs to know where the cycle starts before it can detect one | The two-pointer trick feels like it should require some upfront cycle information | State plainly: the algorithm needs no advance knowledge of the cycle at all — it discovers the meeting purely from the relative speed difference, which works regardless of where the loop begins or how long it is |
| The loop condition only needs to check `fast != null` | `slow` never risks going out of bounds, so it's natural to assume the same laxity applies to `fast` | Activity 2 — showing `fast->next` must also be checked, since `fast` looks two nodes ahead each iteration and could otherwise dereference `null` |
| This problem is solved the same way as searching for a duplicate value in an array | Both involve "have I seen this before" | Contrast explicitly: an array search has no notion of "pointers moving at different speeds" — that mechanism is unique to the two-pointer optimization here, made possible specifically because linked-list traversal is sequential and directional |

---

## Instructor Notes

- **The gap-shrinks-by-one argument in Slide Block B is the session's real payoff — protect it even under time pressure.** Students who can explain *why* the pointers must meet (not just that they do) have understood Floyd's algorithm; students who only memorize "slow moves 1, fast moves 2" have not.
- **Watch for the `fast->next != null` omission specifically** — it's the most common subtle bug students will write when asked to implement this from scratch, and Activity 2 exists to pre-empt it.
- **Classroom Quiz placement is a placeholder** — no MCQ bank exists in the source material for this course. Pull 5–6 questions live from the platform bank.
- **Bridge to Session 44 explicitly at the close:** "Today answered yes-or-no: does a cycle exist? Next session asks a harder follow-up — if it does, exactly how long is the loop? Same two pointers, one more phase added on."
