# Session 49 — Intro of Stack

**Duration** 45 min <!-- placement: inferred — single-concept intro session, lighter than reference's 60-min day-one session --> · **Topic** Stack & Queue — Introduction to Stacks · **Prerequisite** Linked List block, specifically Session 48 — Flatten List (this session opens the Stack & Queue block)
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Intro of Stack | https://docs.google.com/presentation/d/1NzAoqpub2ZvP_kjvtJT7GZGt_UL6g7oyh9ET3haxAQg/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the LIFO (Last In, First Out) principle that defines a stack, in one sentence. *(REMEMBERING)*
2. Identify the "top" as the single end where a stack allows insertion and deletion. *(REMEMBERING)*
3. Explain why a real-world stack of plates behaves like the data structure, and use it to justify why push/pop only happen at one end. *(UNDERSTANDING)*
4. Distinguish a fixed-size stack from a dynamic-size stack, and state which failure condition (overflow or underflow) applies to each situation. *(ANALYZING)*
5. State the time complexity of push, pop, top, empty, and size — and explain why all five are O(1). *(APPLYING)* <!-- placement: inferred from "Operations of Stack: Recap" slide and Key Takeaways -->

---

## Warm-Up Poll — Retrieval Practice on Session 48 (Flatten List) (0–5 min)

Say: *"Before we leave linked lists behind for a while — eight quick questions on the last thing you built: Flatten a Linked List."*

**Q1.** In "Flatten a Linked List," each node has two pointers. What are they?
`A` next and prev · `B` next and down · `C` up and down · `D` left and right
→ *Read:* If this isn't unanimous B, stop — the entire problem hinges on this. `next` walks the main list, `down` walks into a node's own sorted sub-list.

**Q2.** The final flattened list is printed by following which pointer?
`A` next · `B` down · `C` both, alternating · `D` neither — a new list is built
→ *Read:* B. The problem statement is explicit about this — the output is read off the `down` chain, not `next`.

**Q3.** Every sub-linked list hanging off the main list (reachable via `down`) is guaranteed to be:
`A` unsorted · `B` sorted in descending order · `C` sorted in ascending order · `D` exactly the same length as the others

**Q4.** `mergeTwoLists()` in the flattening pseudocode is essentially:
`A` reversing a linked list · `B` the merge step from merge sort, but rewired to use `down` instead of `next` · `C` a binary search over the list · `D` a hashmap lookup
→ *Read:* If B doesn't land, redraw the two-pointer comparison (`temp1->data <= temp2->data`) on the board before moving on — everything else in the session builds on this being familiar.

**Q5.** What is the overall time complexity of flattening N sub-lists of average length M?
`A` O(N + M) · `B` O(NM) · `C` O(N²M) · `D` O(N log N)
→ *Read:* C. Each merge grows the "already merged" list, so total work is roughly 2M + 3M + ... + NM — a series that sums to ~N²M.

**Q6 (MSQ — pick both correct).** Which are true about the space complexity of `flattenLL`?
`A` O(1), because merging only rearranges existing pointers · `B` O(N), because new nodes are allocated for every merge · `C` O(N), because of recursion stack depth · `D` O(1) is correct — no extra data structure is built during merging
→ *Read:* Correct answers are A and D. If students pick B or C, they're assuming this works like array merging — it doesn't; nothing new is ever allocated.

**Q7.** In `mergeTwoLists`, what job does the `dummy` node do?
`A` Stores the final answer directly · `B` Acts as a placeholder before the head of the merged list so the very first "attach" doesn't need a special case · `C` Counts how many nodes were merged · `D` Nothing — it's dead code

**Q8.** True or False: flattening proceeds by merging sub-lists one at a time, left to right, so the "already merged" portion keeps growing as you move across the main list.
`A` True · `B` False

**Running it** — poll tool, ~30 s per question. Total 5 min including reads.

---

## Hook (5–8 min)

Say: *"For the last several sessions you've been chasing pointers through nested structures — up, down, next, merge, repeat. Today's data structure has exactly one legal move."*

Physically pick up a stack of 3-4 notebooks/books from your desk (or project the "Stack of Plates" slide before revealing it). Ask: *"I want to remove the notebook second from the bottom without touching anything else. Go."*

Let them tell you it's impossible without a mess. Then: *"Right. There's exactly one item you're allowed to touch — whatever's on top. That's the entire rule of a stack. No pointers to chase, no cases to handle — one end, one move. After the linked list block, this is going to feel like a breath of fresh air."*

---

## Slide Block A (8–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 3–11: Agenda, What is a Stack, Stack Structure, Top, Real-Life Examples, Operations Recap -->
Covers: Agenda → What is a Stack (LIFO) → Stack Structure (insertion/deletion only at `top`) → Top pointer → Real-Life Example (stack of plates) → Operations of Stack recap table.

**Beats to emphasise**

- **LIFO, said plainly:** "Last one in is the first one out." Use the deck's own push/pop illustration (element 1, 2, 3 going in, then 3 coming out first) — don't invent a new example yet, that's Activity 1's job.
- **One end, one name.** Insertion and deletion both happen at `top` — there is no "front" and "back" the way there will be for Queue in a few sessions. Flag this explicitly: students who've just done linked lists will want to reach for `head`/`tail` vocabulary; stop that here.
- **Plates, not abstractions.** The plates example (slide 10) is the one to linger on — "to get the plate at the bottom, you must remove every plate above it first." This is the sentence students should be able to repeat back.
- **Operations table (slide 11)** — read every row aloud: `push`, `pop`, `top`, `empty`, `size`, all O(1). Say explicitly: "Every single operation on a stack is instant, no matter how many elements are in it. Hold onto that — it's not true for every data structure you'll meet."

**Checkpoint (at 18 min)** — cold-call two students:
> *"In one sentence: why can't I pull the second-from-top plate out of a stack of plates?"*
> **Answer:** Because a stack only exposes the top element — everything below it is inaccessible until the elements above are removed first (LIFO).

---

## ⚡ Activity 1 — Real-World Callout: "Stacks Around You" (18–24 min)

**Format:** Real-World Callout · **Exposes:** whether students can generalise LIFO beyond the plates example the deck gave them — to software behaviour they already use daily.

**Setup line (say this):**
> *"Thirty seconds. Name one thing — tech or otherwise — where the *last* thing you did is the *first* thing that gets undone. Shout it out."*

**What students do:** Call out examples. Write up to 6 on the board.

**How it surfaces:** For 3 of the callouts, push once: *"Where's the top of that stack, and what does 'pop' mean there?"* Expect: browser back button (last page visited = first one you return to), Ctrl+Z / undo (last edit = first one undone), a stack of chairs, plates, trays in a cafeteria. If someone says "call stack" or "recursion," take it — it's correct and worth a 10-second nod, but don't detour into recursion now.

**If students give a queue example by mistake** (e.g., "people in a line," "a printer queue"): don't correct it as wrong — say *"Hold that one, that's actually the *opposite* structure, and we'll meet it properly in a few sessions. What makes it different from the plates?"* Let them arrive at "first in line is served first" themselves.

**Debrief line:**
> *"Every 'undo' button you've ever clicked is a stack. You've been using LIFO your whole digital life — today you start building it."*

**Cut rule:** If running late, take 3 callouts instead of 6 and skip the push-for-"where's-the-top" step. Do not cut the debrief line.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–37 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 12–17: Types of Stack, Stack Implementation preview, Key Takeaways -->
Covers: Types of Stack (Fixed-Size vs Dynamic-Size) → Stack Implementation preview (Array vs Linked List — not built yet) → Key Takeaways.

**Beats to emphasise**

- **Fixed-size stack:** set capacity. Pushing when full → **overflow error**. Popping when empty → **underflow error**. Both terms will reappear verbatim in the next two sessions' dry runs — get students saying them today.
- **Dynamic-size stack:** grows when full, shrinks when elements are removed — no overflow, but not free (something has to do the resizing work behind the scenes).
- **Implementation preview (slide 15)** is just a signpost — "There are two ways to build this: array, and linked list. Next two sessions, one each." Do not attempt to build either today; that's the whole point of not over-explaining this slide.
- **Key Takeaways (16–17)** — read as a checklist: LIFO, top pointer, push adds to top, pop removes from top, top/peek reads without removing, isEmpty, isFull, fixed vs dynamic and their failure modes.

**Checkpoint (at 37 min)** — show of hands:
> *"A fixed-size stack of capacity 5 already holds 5 elements. I call push(9). Overflow or underflow?"*
> **Answer:** Overflow — the stack is full and has no room for a 6th element.

---

## ⚡ Activity 2 — Live Trace: "Call the Operations" (37–43 min)

**Format:** Live Coding / Dry-Run Relay (verbal, no code needed) · **Exposes:** whether students can hold stack state in their head across a sequence of operations — the exact skill the next two sessions' array/linked-list dry runs will demand.

**Setup line (say this):**
> *"I'm going to call out a sequence of stack operations, one at a time. After each one, I want a show of hands for 'what's on top right now' — and I'll cold-call someone to say the full stack, bottom to top."*

Call these out **one at a time**, pausing for a hands-up prediction before revealing:

```
push(5)      → stack: [5]              top = 5
push(3)      → stack: [5, 3]           top = 3
push(8)      → stack: [5, 3, 8]        top = 8
pop()        → stack: [5, 3]           top = 3   (removed 8)
push(1)      → stack: [5, 3, 1]        top = 1
top()        → returns 1, stack unchanged
pop()        → stack: [5, 3]           top = 3   (removed 1)
pop()        → stack: [5]              top = 5   (removed 3)
```

**How it surfaces:** Cold-call a different student after each line to state the stack bottom-to-top. If a student states it top-to-bottom instead (a common inversion), don't mark it wrong outright — ask "bottom-to-top or top-to-bottom?" and have them re-say it your way once, so the convention is explicit for the room.

**Common wrong answer:** students forget `top()` doesn't remove anything and, after a `top()` call, report the stack as one element shorter. Catch this live by immediately following any `top()` with "so what's the stack now — did anything change?"

**Debrief line:**
> *"Notice I never once had to think about anything except the top. That's the whole engine. When you see arrays and linked lists doing this in the next two sessions, the *logic* won't be new to you — only the plumbing will be."*

**Cut rule:** If running short, drop to 5 operations instead of 8 — keep at least one `push`, `pop`, and `top()` in sequence so all three behaviours are demonstrated.

---

## Exit Ticket (43–45 min)

> In one line: what is the one operation you can always perform on a stack in O(1) time regardless of how many elements it holds, and why is that possible?
> **Answer:** Any of push/pop/top/empty/size — all O(1) — because a stack only ever needs to look at or modify the `top`, never traverse the rest of the structure.

Scan responses on the way out. If several students name a non-O(1) reason (e.g., "because computers are fast"), that's your signal to re-open next session with a 60-second recap of *why* top-only access is what makes it constant time.

**Homework:** re-attempt today's live-trace operation sequence from memory, on paper, before the next session. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A stack has a "front" and "back" like the linked lists they just studied | Fresh off the Linked List block, where head/tail vocabulary dominated | Explicitly naming, in Slide Block A, that a stack has exactly one active end: `top` |
| You can access/remove any element in a stack, not just the top | Arrays and lists they've used elsewhere allow indexed access | The plates example — physically demonstrating that reaching the 2nd-from-top plate requires removing the top one first |
| `top()` removes the element, same as `pop()` | The two words feel similar and “top” sounds like an action | Activity 2 — immediately re-asking "did anything change?" right after every `top()` call |
| A dynamic-size stack can never fail | "Dynamic" sounds unlimited | Note explicitly: it grows/shrinks in software terms, but is still bounded by real memory — overflow becomes vanishingly rare, not impossible |
| Overflow and underflow are the same failure, just worded differently | Both are "stack broke" to a first-time listener | The checkpoint question at minute 37 — forcing a full/empty distinction on a concrete capacity-5 example |

---

## Instructor Notes

- **This is the lightest session in the block.** No pseudocode, no code, no numeric dry run appears in this deck — that's deliberate on the platform's part; array and linked-list implementations (with their own dry runs) are the next two sessions. Do not import code from Session 50/51 to "get ahead" — it will blur the boundary and cost you time you don't have.
- **Say out loud that implementation is coming.** Slide 15 (Array vs Linked List) is a preview, not a lesson. If you over-explain it, you'll steal thunder from Session 50 and run over time here. One sentence, move on.
- **The plates example is your anchor for the whole session.** Refer back to it in the Hook, Slide Block A, and the Exit Ticket answer if students are stuck — repetition of the same concrete image is what makes LIFO stick before the abstraction (push/pop) is introduced properly.
- **Pacing risk:** Activity 2's live trace can run long if you let every student state the full stack. Cap it at 4 cold-calls across the 8 operations; use quick hands-up checks for the rest.
- **Warm-up poll note:** this is the last "retrieval on Linked List" warm-up — from Session 50 onward, every warm-up looks back one session within this Stack & Queue block. Say this explicitly if a student asks why today's poll felt disconnected from stacks.
