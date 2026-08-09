# Session 39 — Insertion, Deletion

**Duration** 60 min · **Topic** Linked List · **Prerequisite** Session 38 — Singly Linked List (node structure, traversal, search)
**Session type** Concept lecture

<!-- placement: inferred — duration set above the 45-min default because this session covers eight distinct operations (4 insertion positions + 4 deletion positions), each with its own approach, code, and complexity. Compressing to 45 min would force skipping at least two of the eight. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Insertion, Deletion | https://docs.google.com/presentation/d/1q6eByMnLCZNPIFu9IMBzrmpZbQ1GlJ4feddjOeakiTU/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. List the four insertion positions and four deletion positions covered for a singly linked list. *(REMEMBERING)*
2. Explain why inserting or deleting at the head is O(1) while at the tail is O(n) on a singly linked list. *(UNDERSTANDING)*
3. Implement insertion at the head, tail, Kth position, and before a node with value `x`. *(APPLYING)*
4. Implement deletion at the head, tail, Kth position, and of the node with value `x`. *(APPLYING)*
5. Justify the time complexity of each of the eight operations using pointer-traversal reasoning. *(ANALYZING)*
6. Identify the edge cases — empty list, single-node list, k out of bounds, value not found — that each operation must guard against. *(ANALYZING)* <!-- placement: inferred from the "Check if List is Empty" step repeated at the start of every Approach slide -->

---

## Warm-Up Poll — Retrieval on Session 38: Singly Linked List (0–7 min)

Say: *"Seven quick ones on last session's linked list basics before we build on top of them."*

**Q1.** What are the two things every node in a singly linked list stores?
`A` Data and a pointer to the previous node · `B` Data and a pointer to the next node · `C` Only data · `D` Two pointers, no data

**Q2.** Why can arrays be a poor fit when the amount of data is unknown or changes often?
`A` Arrays are always slower to read from · `B` Arrays have a fixed size once declared · `C` Arrays cannot store integers · `D` Arrays require pointers

**Q3.** In a singly linked list, what does the `next` pointer of the LAST node point to?
`A` The head node (circular) · `B` The previous node · `C` `null` · `D` Itself

**Q4.** *(MSQ — select all that are TRUE)*
`A` A doubly linked list allows bidirectional traversal · `B` A circular linked list's last node points back to the first node · `C` A singly linked list's last node points to `null` · `D` All three types require contiguous memory
→ *Read:* If many pick D, the core "why linked lists exist" idea from last session hasn't landed — put the non-contiguous-memory diagram back up for 30 seconds before moving on, since today's operations all assume nodes are scattered in memory.

**Q5.** What was the time complexity of building a linked list from an array of `n` elements (`arraytoLL`)?
`A` O(1) · `B` O(log n) · `C` O(n) · `D` O(n²)

**Q6.** What was the space complexity of the search function (`searchLL`) that checks whether a value `x` is present?
`A` O(n) · `B` O(1) · `C` O(log n) · `D` O(n²)
→ *Read:* If the class flips this with the O(n) time answer, that's the classic time-vs-space mixup — hold up one finger ("one pointer, that's it, no matter how far it searches") to reinforce O(1) space.

**Q7.** In the "Print Linked List" algorithm, when does the traversal loop stop?
`A` After a fixed number of steps · `B` When `temp` reaches `null` · `C` When it finds the target value · `D` It never stops

**Running it** — poll tool, ~45 s per question, project the distribution after each. Total 7 min including your reads.

---

## Hook (7–11 min)

Put this on the board:

> *"Array `[20, 30, 40, 50]`. I want to insert `5` at the front. In an array, what has to happen to every other element?"*

Let them say it: everything shifts right by one slot. Then draw the same four values as a linked list: `20 → 30 → 40 → 50 → NULL`. *"Now I insert 5 at the front. How many existing nodes move?"*

Answer: zero. *"Nobody moves. One new box, one new arrow, done. That's the entire subject of today — how cheap or expensive it is to insert or remove a node, depending on WHERE."*

---

## Slide Block A (11–22 min) — DELIVER SLIDES AS-IS

Covers: Insertion in a Linked List — the four types (head, tail, Kth position, before a node with value `x`) — each with problem statement, approach, C++ code, and complexity; closing with the Insertion Complexity Summary table.

**Beats to emphasise**

- **Insert at head** is the baseline: create node `n`, `n->next = head`, `head = n`. Three lines, O(1), no traversal — say explicitly that this is *as cheap as insertion ever gets*.
- **Insert at tail**: the expensive one *is* the traversal, not the insertion. You walk node-by-node until `temp->next == nullptr`, and only then do you attach — that walk is the entire O(n) cost. Flag the empty-list special case (`if head == nullptr`, the new node just becomes head).
- **Insert at Kth position**: this is `insert-at-head` (if `k == 0`) fused with `insert-at-tail`'s traversal style — walk to the `(k-1)`th node using a `count` variable, then splice in. The traversal stops at `count == k - 1`, **not** `count == k`. Underline that distinction on the board; it's the exact bug Activity 1 is built around.
- **Insert before a node with value `x`**: same shape again, but the stopping condition is `temp->next->data == x` (you must look *ahead* one node, because you need to hold the node *before* the target to relink it).
- Close with the **Complexity Summary table**: head O(1)/O(1), tail O(N)/O(1), Kth O(N)/O(1), before-x O(N)/O(1) — every insertion is O(1) *space* because only ever one new node is allocated; the time cost is entirely about how far you must walk to reach the insertion point.

**Checkpoint (at 22 min)** — cold-call one student:
> *"Insert-at-head is O(1) and insert-at-tail is O(n) on a singly linked list with only a head pointer — in one sentence, why the gap?"*
> **Answer:** Inserting at head just rewires one pointer at a spot you already have. Inserting at tail means walking the entire list first to even find the last node, because there's no shortcut to it — no separate tail pointer is kept.

---

## ⚡ Activity 1 — Spot the Bug: The Off-By-One in Insert-Kth (22–27 min)

**Format:** Spot the Bug · **Exposes:** the `k` vs. `k - 1` off-by-one, the single most common insertion bug, sitting inside the deck's own `insert_kth` traversal condition.

**Setup line (say this):**
> *"Here's `insert_kth` from the slides, except I changed exactly one character. List is `1 → 3 → 5 → 7 → 9`, I call `insert_kth(head, 2, 99)`. Trace where `99` actually lands — before I tell you what I changed."*

```cpp
Node* temp = head;
int count = 0;
while (temp != nullptr){
    if (count == k){          // was: count == k - 1
        Node* n = new Node(a);
        n->next = temp->next;
        temp->next = n;
        break;
    }
    temp = temp->next;
    count++;
}
```

**What students do:** Trace by hand (or at the board) where `99` lands versus where `k = 2` (0-indexed) should place it — right after `3`, before `5`.

**How it surfaces:** If someone says "it crashes" — it doesn't. It silently inserts one node too late (after `5` instead of after `3`), which is more dangerous than a crash because nothing flags it as wrong.

**Debrief line:**
> *"`count == k - 1` means stop one node early, so you insert AFTER the (k-1)th node, which lands the new node at position k. Change that one character and the bug doesn't crash — it quietly gives you the wrong answer. That's worse, and it's exactly the kind of bug you'll spend an hour hunting later if you don't get it right the first time."*

**Cut rule:** If running short, skip the hand-trace and just ask "does node 99 land before or after node 5?" as a show of hands, then reveal.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (32–42 min) — DELIVER SLIDES AS-IS

Covers: Deletion in a Linked List — the four types (head, tail, Kth position, node with value `x`) — each with problem statement, approach, C++ code, and complexity; closing with the Deletion Complexity Summary table.

**Beats to emphasise**

- **Delete head**: store `head` in `temp`, move `head = head->next`, delete `temp`, return the new `head`. O(1) — no traversal needed, mirror of insert-at-head.
- **Delete tail**: two edge cases before the real work — empty list, and single-node list (`head->next == nullptr`, meaning the head IS the tail). Only after both checks does it traverse to the *second-to-last* node, using `temp->next->next != nullptr` as the stopping condition — one node further ahead than `insert-at-tail`'s stopping condition, because deletion needs the node *before* the one being removed.
- **Delete Kth**: same `count == k - 1` traversal pattern as insertion, then `temp->next = temp->next->next` skips over (and deletes) the target node.
- **Delete node with value `x`**: check the head first (`head->data == x`), then look ahead with `temp->next->data == x` — same "look one ahead" shape as insert-before-x.
- Every deletion is O(1) **space** (a handful of pointers, nothing that scales); the time cost again tracks how far you must walk: head = O(1), everything else = O(n) except Kth which is technically O(k).

**Checkpoint (at 42 min)** — cold-call one student:
> *"Before you delete the tail, the approach says check if `head->next == NULL`. What breaks if you skip that check?"*
> **Answer:** If there's only one node, that node IS both head and tail. The code that walks to "the node before the last node" has nothing to walk to — `temp->next->next` would dereference a null pointer — so it crashes instead of correctly deleting the list's only node.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: Delete the Kth Node (42–50 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can correctly identify and relink `temp->next` when deleting from the middle of the list — the crux of `delete_kth`.

**Setup line (say this):**
> *"Board time. List is `1 → 3 → 5 → 7 → 9 → NULL`. We're deleting k = 2 (0-indexed — that's the node holding 5). One volunteer per node, draw your box and your `next` arrow. I'll call the moves, you draw."*

**What students do:** Five volunteers draw the chain. The class walks `temp` to `count == k - 1` (the node holding `3`). That volunteer draws the new arrow skipping over node `5`, and node `5` gets crossed out as "deleted."

**How it surfaces:** The common mistake is relinking `temp->next` to point at `temp->next` itself (a no-op) instead of `temp->next->next`. Stop and ask: *"If you only change what `temp` points to without skipping ahead, does anything still point at node 5?"* Walk them to seeing node 5 is still fully wired into the list.

**Debrief line:**
> *"One pointer changed — `temp->next` — and node 5 is gone from the list, even though it might still exist in memory until it's explicitly deleted. That's the whole trick: deletion in a linked list is rewiring, not erasing."*

**Cut rule:** If running short, drop the volunteer relay and sketch it yourself on the whiteboard while narrating the same two steps (find `count == k-1`, then skip one node ahead).

---

## ⚡ Activity 3 — Predict-the-Output: Chained Insert Then Delete (50–57 min)

**Format:** Predict-the-Output · **Exposes:** whether students can hold two operations in sequence without redrawing the whole list from scratch — the same skill multi-step DSA problems demand.

**Setup line (say this):**
> *"Starting list: `1 → 3 → 5 → 7 → 9 → NULL`. I run `insertAtHead(head, 0)`, then `deleteTail(head)` on the result. Before I show you anything — write down the final list."*

**What students do:** Write their predicted final list individually, then share out.

**How it surfaces:** The most common wrong answer forgets the insert happened first, and predicts deletion removes `9` from the *original* 5-node list — giving `3, 5, 7, 9` or similar instead of the correct `0, 1, 3, 5, 7`. Some will also flip which end `deleteTail` removes from — remind them `deleteTail` always removes from the end, regardless of what was just inserted at the front.

**Debrief line:**
> *"Insert happened first, so the list was 6 nodes long by the time delete-tail ran — it removes whichever node is last AFTER the insert, not before. Sequence matters. This is exactly how multi-step problems get misread under time pressure."*

**Cut rule:** If short on time, skip the individual write-down and run it as a single cold-call trace on the board instead.

---

## Exit Ticket (57–60 min)

**Exit ticket** — on paper before anyone leaves:

> Write the two lines of code that insert an already-created node `n` at the head of a non-empty list, starting from `head`.
> **Answer:** `n->next = head;` then `head = n;`

**Homework:** Re-attempt today's insertion and deletion dry runs from memory — write all eight functions (4 insert + 4 delete) from scratch without looking back at the slides, then check against the deck. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Inserting at the head of a linked list is O(n), "because you have to shift everything" | Habit carried over from array insertion | The Hook — drawing the linked-list version and counting how many *existing* nodes actually move (zero) |
| The traversal for `insert_kth` should stop at `count == k` | "Insert at position k" sounds like "stop when you reach index k" | Activity 1 — tracing exactly where the off-by-one lands the new node |
| `deleteTail` needs to check for an empty list, but not for a single-node list | The empty-list check is the obvious one; the single-node case looks like "just another case of the loop" | Slide Block B's checkpoint — showing that `temp->next->next` would dereference `null` on a one-node list |
| Deletion "erases" the node from memory automatically | The node visually disappears from the diagram once unlinked | Activity 2's debrief — the node is unreachable, not gone; explicit `delete` is what frees it |
| `head` changes after every insertion or deletion | Some operations (head-insert, head-delete) genuinely do change `head`, so students overgeneralise | Pointing out that `insertAtTail` and `deleteTail` both `return head` unchanged, except in their empty-list/single-node edge cases |

---

## Instructor Notes

- **Pacing risk:** Slide Block A has four sub-operations in 11 minutes — don't let `insert-before-x` run long just because it's the last one covered; it's structurally identical to `insert_kth` with a different stopping condition, say that explicitly and move on.
- **The deck's own slides mark natural quiz breaks** ("Quiz Time!" appears right after the Insertion summary table and again after the Deletion summary table) — this plan uses the first of those breaks as the Classroom Quiz slot; the second is a natural spot for a quick recap question if you're ahead of pace.
- **Activity 1 depends on Slide Block A's `count == k - 1` beat landing first** — if that beat got rushed, spend 30 extra seconds re-deriving it before running the activity, or the bug will look arbitrary rather than inevitable.
- **The Classroom Quiz has no fixed question set in this material** — pull 5-6 from the platform bank covering insertion/deletion complexities and edge-case handling before class starts.
- **Watch the clock at the Slide Block B → Activity 2 handoff** — this is the second-largest content block of the session; if you're behind, use Activity 2's cut rule immediately rather than trimming Slide Block B's content.
