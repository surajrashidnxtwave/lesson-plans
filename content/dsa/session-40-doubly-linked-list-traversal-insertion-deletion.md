# Session 40 — Doubly Linked List - Traversal, Insertion, Deletion

**Duration** 60 min · **Topic** Linked List · **Prerequisite** Session 39 — Insertion, Deletion (singly linked list)
**Session type** Concept lecture

<!-- placement: inferred — duration set above the 45-min default for the same reason as the two sessions before it: this session covers DLL construction plus three insertion positions plus three deletion positions, each with its own approach, dry-run diagram, code, and complexity. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Doubly Linked List - Traversal, Insertion, Deletion | https://docs.google.com/presentation/d/1CtyEsYixyAXlaQ-wZUaqsxFZx4XQF1JPPIJ18LZ9YKg/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Describe how a doubly linked list node differs from a singly linked list node — it carries both `prev` and `next`. *(UNDERSTANDING)*
2. Construct a doubly linked list from an array, correctly linking both `prev` and `next` at every step. *(APPLYING)*
3. Implement insertion at the head, tail, and Kth position of a doubly linked list. *(APPLYING)*
4. Implement deletion at the head, tail, and Kth position of a doubly linked list, correctly handling the head/tail boundary cases. *(APPLYING)*
5. Compare the time complexity of each DLL operation against its singly-linked-list counterpart from Session 39. *(ANALYZING)* <!-- placement: inferred — the deck presents each DLL complexity independently; the head-to-head comparison against Session 39 is scaffolding added for retrieval and contrast -->
6. Explain why every DLL insertion or deletion must update two pointers instead of one. *(UNDERSTANDING)*

---

## Warm-Up Poll — Retrieval on Session 39: Insertion, Deletion (0–7 min)

Say: *"Seven quick ones on last session's insertion and deletion before we add a second pointer to every node today."*

**Q1.** What is the time complexity of inserting a new node at the HEAD of a singly linked list?
`A` O(1) · `B` O(n) · `C` O(log n) · `D` O(k)

**Q2.** What is the time complexity of inserting a new node at the TAIL of a singly linked list, given only the head pointer?
`A` O(1) · `B` O(n) · `C` O(log n) · `D` O(k)
→ *Read:* If many say O(1), they're forgetting you must walk the entire list to even find the last node, since there's no separate tail pointer kept. Flag this hard — the same "no shortcut to the far end" idea is exactly what today's Doubly Linked List session is about to complicate.

**Q3.** To delete the node at the Kth position, what must you first find?
`A` The Kth node itself, directly · `B` The (K−1)th node — the one just before it · `C` The tail node · `D` The head node

**Q4.** *(MSQ — select all that have O(1) time complexity in a singly linked list)*
`A` Insert at head · `B` Delete at head · `C` Insert at tail · `D` Delete at tail

**Q5.** When deleting the node with a given value `x`, what happens if `x` isn't found anywhere in the list?
`A` The program crashes · `B` The list is returned unchanged, after traversing to the end · `C` The head is deleted anyway · `D` Infinite loop

**Q6.** What must you check BEFORE attempting to delete the tail of a list that might have just one node?
`A` Whether `head->next` is null (single-node list) · `B` Whether `head->data` equals `x` · `C` Whether `k` equals 0 · `D` Nothing — the same code always works
→ *Read:* This exact "does the node have a neighbour on both sides?" question is the seed of nearly every null-pointer bug students will write in today's Doubly Linked List deletion code. Call it out explicitly before Slide Block C.

**Q7.** What was the space complexity of every insertion and deletion operation from last session — head, tail, Kth, or by value?
`A` O(n) · `B` O(log n) · `C` O(1) · `D` It depends on k

**Running it** — poll tool, ~45 s per question, project the distribution after each. Total 7 min including your reads.

---

## Hook (7–11 min)

Draw a 3-node singly linked list on the board: `10 → 20 → 30 → NULL`. Point at node `20`. Ask: *"I'm standing at node 20. Give me the value of the node before me."*

Let the silence land — there's no way to answer without re-traversing from `head`. Then: *"In everything you've built for two sessions, going backward means starting over from the front. Today's data structure gives every node a second arrow, pointing back the way it came — at the cost of one extra pointer to keep correct, every single time you insert or delete."*

---

## Slide Block A (11–19 min) — DELIVER SLIDES AS-IS

Covers: Doubly Linked List introduction — node structure (`prev`, `data`, `next`) → Head and Tail → C++ Node class (three-argument constructor) → Convert an Array into a DLL (`arrayToDLL`): problem statement, approach, code, complexity.

**Beats to emphasise**

- The node picture is the whole idea: **`prev` ← `data` → `next`**. Every node except the head has a real `prev`; every node except the tail has a real `next`. Both ends are `NULL` on their outward side.
- Walk `arrayToDLL`'s approach exactly as staged: create `head` from `arr[0]`, then for each remaining element create `temp`, set `temp->prev = cur` **and** `cur->next = temp`, then move `cur = temp`. Two links per step, not one — say this out loud every time.
- Complexity: **O(n) time** (one pass, same shape as `arraytoLL` from Session 38) and **O(n) space** (one new node per element) — identical complexity to the singly-linked-list version; the only thing that changed is *how much wiring* happens per node, not how many times the loop runs.
- Reuse the deck's own **Music Playlist** framing if time allows — head = first song, tail = last song, same as Session 38, now with the ability to skip back to the previous track.

**Checkpoint (at 19 min)** — cold-call one student:
> *"When you build a doubly linked list from an array, why do you need `temp->prev = cur` in addition to `cur->next = temp`?"*
> **Answer:** A DLL node needs both directions wired. `cur->next` lets you walk forward from `cur` to `temp`, but without `temp->prev = cur` there's no way to walk backward from `temp` to `cur` — skip it, and you've quietly built a singly linked list with an unused second pointer.

---

## ⚡ Activity 1 — Live Coding / Dry-Run Relay: Build the DLL, Both Pointers (19–24 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** the single new failure mode a doubly linked list introduces — wiring `next` and forgetting `prev`.

**Setup line (say this):**
> *"Same relay you know from linked lists, one new rule. `arr = [10, 20, 30, 40]`. Four volunteers, one box each. This time, every arrow needs a partner — for every `next` arrow you draw, the `prev` arrow going the other way gets drawn immediately after, before we move to the next node."*

**What students do:** Volunteer 1 is `head` (`prev = NULL`). Volunteer 2 draws the `next` arrow from node 1 to node 2, and must immediately draw the `prev` arrow from node 2 back to node 1 before the class moves to volunteer 3. Continue through node 4, whose `next` is `NULL`.

**How it surfaces:** If the class rushes ahead and draws all four `next` arrows first, stop everything: *"Count the `prev` arrows on the board right now."* Let them see the gap themselves before you let them fix it — that's the entire point of the activity.

**Debrief line:**
> *"Every insertion or deletion you do on a DLL for the rest of this session has this exact shape — one `next` fix, one `prev` fix. Forget the second one, and you've built a singly linked list wearing a doubly linked list's clothes."*

**Cut rule:** If running short, drop to 3 nodes instead of 4, but do not skip the "count the `prev` arrows" trap — that's the whole teaching point.

---

## Slide Block B (24–33 min) — DELIVER SLIDES AS-IS

Covers: Insertion in a Doubly Linked List — the three types (head, tail, before the Kth node) — each with problem statement, dry-run diagram, C++ code, and complexity; closing with the Insertion Complexity Summary table.

**Beats to emphasise**

- **Insert at head**: create the new node with `next = head`, then the one DLL-specific extra step — `head->prev = temp` — before making `temp` the new head. Still O(1); the only change from Session 39's singly-linked-list version is that one extra pointer fix.
- **Insert at tail**: walk from `head` until `tail->next == nullptr` (same O(n) walk as Session 39's `insertAtTail`), then link both directions: `temp->prev = tail` and `tail->next = temp`.
- **Insert before the Kth node**: traverse to the node currently *at* position `k` (`temp`), grab its `prev` as `back`, then splice the new node in between — `back->next = n`, `temp->prev = n`, plus the new node's own `n->next = temp` and `n->prev = back`. Four pointer updates total; walk through each one on the board rather than skipping to the finished diagram.
- Close with the **Complexity Summary**: head O(1)/O(1), tail O(N)/O(1), Kth position O(k)/O(1) — same time-complexity shape as Session 39's singly-linked-list insertions, but every single one now touches two pointers per link instead of one.

**Checkpoint (at 33 min)** — show of hands:
> *"Insert-at-head in a DLL is still O(1), same as a singly linked list — what's the one extra step a DLL insert-at-head needs that a singly linked list doesn't?"*
> **Answer:** You must also set the old head's `prev` pointer to point back at the new node (`head->prev = temp`). A singly linked list has no `prev` to fix, so this is the one genuinely new step.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block C (38–48 min) — DELIVER SLIDES AS-IS

Covers: Deletion in a Doubly Linked List — the three types (head, tail, Kth position) — each with problem statement, dry-run diagram, C++ code, and complexity; closing with the Deletion Complexity Summary table.

**Beats to emphasise**

- **Delete head**: handle empty-list and single-node cases first, then `head = head->next` followed by the DLL-specific extra step, `head->prev = nullptr`, before deleting the old head. O(1).
- **Delete tail**: same empty/single-node guards, then walk to the second-to-last node, set both `temp->next = nullptr` and `tail->prev = nullptr` before deleting the old tail. O(n).
- **Delete Kth node**: this is the one with real branching complexity — walk this one slowly. After finding `temp` at position `k`, save `back = temp->prev` and `front = temp->next`, then branch three ways: **both null** (only node in the list — delete and return `nullptr`), **`back` null** (`temp` is the head — hand off to `delete_head`), **`front` null** (`temp` is the tail — hand off to `delete_tail`), and only in the **true middle case** does the general relink formula run: `back->next = front; front->prev = back`.
- Emphasise *why* the branching exists: the general formula assumes both neighbours are real nodes. Head and tail are exactly the two positions where that assumption breaks.
- Close with the **Complexity Summary**: head O(1)/O(1), tail O(N)/O(1), Kth O(k)/O(1) — identical shape to insertion.

**Checkpoint (at 48 min)** — cold-call one student:
> *"In `delete_kth`, why does the code branch into three separate cases instead of using one general formula?"*
> **Answer:** The general formula `back->next = front; front->prev = back` assumes both neighbours exist. If the node being deleted is the head, `back` is null; if it's the tail, `front` is null; if it's the only node, both are null. Each of those would crash the general formula, so they're handed off to `delete_head` / `delete_tail` instead of forcing one formula to cover every case.

---

## ⚡ Activity 2 — Spot the Bug: Delete-Kth Without Boundary Checks (48–53 min)

**Format:** Spot the Bug · **Exposes:** the null-pointer crash that happens when the general "relink neighbours" logic runs on a node at the head or tail without the deck's own boundary branching.

**Setup line (say this):**
> *"This is `delete_kth`, except I deleted the three boundary `if` branches and left only the general case. Tell me exactly which position — head, middle, or tail — makes this crash, and why."*

```cpp
// deck's boundary branches removed:
// if (back == nullptr && front == nullptr) { delete temp; return nullptr; }
// else if (back == nullptr) return delete_head(head);
// else if (front == nullptr) return delete_tail(head);
Node* back = temp->prev;
Node* front = temp->next;
back->next = front;
front->prev = back;
delete temp;
return head;
```

**What students do:** Trace `k = 0` (deleting the head) mentally: `back = temp->prev` is `nullptr` because the head has no previous node, so `back->next` dereferences a null pointer on the very next line.

**How it surfaces:** If someone says "it just doesn't delete anything" — no, it crashes. A null-pointer dereference in C++ is not a silent no-op.

**Debrief line:**
> *"The general relink formula only works when the node being deleted has neighbours on both sides. The head and tail are exactly the nodes that don't — which is why the real code hands those two cases off to `delete_head` and `delete_tail` instead of trying to force one formula to cover everything."*

**Cut rule:** If running short, skip tracing `k = 0` by hand and just ask "which position breaks this — head, middle, or tail?" as a show of hands, then reveal why.

---

## ⚡ Activity 3 — Predict-the-Output: The Stale Pointer (53–57 min)

**Format:** Predict-the-Output · **Exposes:** whether the `prev`/`next` distinction from Activity 1 has actually stuck by the end of the session.

**Setup line (say this):**
> *"Doubly linked list: `NULL ← 10 ⇄ 20 ⇄ 30 → NULL`. I delete the head. One line, everyone: what does node 20's `prev` pointer equal now?"*

**What students do:** Call out the answer — `NULL` — a fast, low-stakes closing check.

**How it surfaces:** If someone says "10" (the old head), that's the exact stale-pointer bug from the misconceptions table — point at it directly: *"10 was just deleted. You cannot point at deleted memory."*

**Debrief line:**
> *"That's the one line every DLL deletion has to get right — the new boundary node's outward-facing pointer must become `NULL`, not just keep pointing at whatever used to be there."*

**Cut rule:** If out of time, cut this activity entirely and fold the same question into the Exit Ticket instead.

---

## Exit Ticket (57–60 min)

**Exit ticket** — on paper before anyone leaves:

> A doubly linked list is `10 ⇄ 20 ⇄ 30`, with `NULL` on both outer ends. You insert a new node with value `5` before the old head. What is `head->prev` immediately after the insertion?
> **Answer:** It is no longer `NULL` — it now points at the new node holding `5` (and `5`'s own `prev` is `NULL`, since `5` is now the true head).

**Homework:** Re-attempt today's doubly linked list construction and Kth-position insertion/deletion dry runs from memory, drawing both the `prev` and `next` arrows at every single step. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Updating `next` is enough; `prev` will "just follow along" | Two sessions of singly-linked-list muscle memory, where only `next` ever mattered | Activity 1's "count the `prev` arrows" trap |
| DLL insertion/deletion at the head costs more than a singly linked list's, since there's an extra pointer | Reasonable-sounding intuition that "more pointers = more complexity class" | Slide Block B's checkpoint — it's still O(1), just one more *constant-time* line, not a new order of growth |
| `delete_kth` can use the same general relink formula regardless of position | The formula looks clean and "the same" for every node in the middle | Activity 2 — running the general formula on `k = 0` and hitting the null-pointer crash directly |
| After deleting a node, the neighbour's old pointer to it will resolve to `NULL` automatically | Deleted nodes disappear from the diagram, so it *looks* automatic | Activity 3 — forcing the explicit statement that the new boundary node's pointer must be *set* to `NULL`, not assumed |
| A DLL gives O(1) access to the tail because "you have two pointers now" | Confusing "bidirectional traversal" with "instant access to both ends" | Slide Block B — walking through `insert_tail`/`delete_tail` and showing the traversal from `head` is still required; two-way links do not create a stored tail reference |

---

## Instructor Notes

- **Pacing risk:** Slide Block C (Deletion) is the densest single block in this session because of `delete_kth`'s three-way branching — do not rush it to protect Activity 2's time; if the branching logic isn't clear here, Activity 2 will look arbitrary rather than inevitable.
- **The deck places its own "Quiz Time!" break right after the Insertion summary table, before Deletion begins** — this plan uses that exact boundary for the Classroom Quiz slot, which also keeps it close to the session's true halfway point.
- **The Classroom Quiz has no fixed question set in this material** — pull 5-6 from the platform bank covering DLL node structure, insertion/deletion complexities, and the head/tail boundary cases before class starts.
- **Activity 1's "both arrows" discipline is worth over-investing in early** — every later activity and checkpoint in this session assumes students already flinch at a lone `next` arrow with no `prev` partner.
- **This is the third linked-list session in a row** — if energy is flagging, lean on the direct Session 39 comparisons built into the Learning Objectives and Slide Blocks (same complexity shapes, one new pointer) rather than introducing new framing from scratch.
