# Session 41 — Circular Linked List

**Duration** 60 min · **Topic** Linked List — Circular Linked List · **Prerequisite** Doubly Linked List — traversal, insertion, deletion (Session 40)
**Session type** Concept lecture

<!-- placement: inferred — duration set to 60 min instead of the 45 min default because this deck covers six distinct operations (insert at beginning/end/position, delete first/last/specific node) each with its own dry run and code, plus traversal, search, and a full complexity/applications wrap-up — noticeably more ground than a single-concept 45-minute session -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Circular Linked List | https://docs.google.com/presentation/d/1DBElXcQ0Nd0rmmYHsmFgI3fd0pxSB-AmTr4_JgWEG30/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a circular linked list and distinguish circular singly from circular doubly linked lists. *(REMEMBERING)*
2. Explain why a circular list has no NULL anywhere and why its traversal needs a different termination condition than a plain linked list. *(UNDERSTANDING)*
3. Trace insertion at the beginning, end, and a specific position of a circular singly linked list by hand. *(APPLYING)*
4. Trace deletion of the first node, the last node, and a specific node of a circular singly linked list by hand. *(APPLYING)*
5. Analyze the time and space complexity of every insertion, deletion, traversal, and search operation on a circular linked list. *(ANALYZING)*
6. Evaluate when a circular linked list is the right structure to reach for, using its stated advantages and disadvantages. *(EVALUATING)*

<!-- placement: inferred — phrased from the deck's own Key Takeaways slides (89-93) and Complexity/Applications/Advantages/Disadvantages slides (84-88) -->

---

## Warm-Up Poll — Retrieval on Session 40 (0–7 min)

Say: *"Eight quick ones on last session's Doubly Linked List before we move on. No names, just show me the room."*

**Q1.** In a Doubly Linked List, each node stores data plus how many pointers?
`A` 0 · `B` 1 · `C` 2 · `D` 3
→ *Read:* If C isn't near-unanimous, redraw the `prev`/`data`/`next` node on the board before starting today's slides — today's node only has one pointer, and that contrast needs to be crisp.

**Q2.** What is the time complexity of inserting a new node at the *head* of a DLL?
`A` O(1) · `B` O(n) · `C` O(k) · `D` O(log n)

**Q3.** The deck's `insert_tail` function walks from `head` until it finds the last node, then attaches the new one there. What's its time complexity?
`A` O(1) · `B` O(n) · `C` O(k) · `D` O(log n)
→ *Read:* If many pick O(1) because "tail" sounds instant, flag it — that exact misconception resurfaces in today's Slide Block B when a circular list's "insert at end" also has to walk the whole list first.

**Q4.** When `delete_head` removes the head node of a DLL, besides moving `head` to `head->next`, what else must it update?
`A` Nothing else · `B` The new head's `prev` pointer, set to nullptr · `C` The old head's `next` pointer · `D` The tail pointer

**Q5.** In `delete_kth`, when the target node has both a `back` neighbour and a `front` neighbour (a true middle node), which pointer updates actually relink the list?
`A` `back->next = front` and `front->prev = back` · `B` `delete_head` and `delete_tail` are both called · `C` only `back->next = front` · `D` only `front->prev = back`

**Q6.** What is the time complexity of inserting a node before the k-th node in a DLL?
`A` O(1) · `B` O(n) · `C` O(k) · `D` O(k²)

**Q7.** *(MSQ — pick up to 2)* Per last session's own summary tables, which of these run in O(1) time on a DLL?
`A` Insert at head · `B` Insert at tail · `C` Delete at head · `D` Delete at k-th position
→ *Read:* If this is shaky, put last session's two summary tables back on screen for 30 seconds before Slide Block A — today's circular-list numbers only land as a contrast to these.

**Q8.** In the deck's "Music Playlist" real-world example, what does the `head` of the list represent?
`A` The last song queued · `B` The first song to play · `C` A randomly chosen song · `D` The song currently playing

**Running it** — poll tool, ~40 s per question, project the distribution after each. Total 7 min including your reads.

---

## Hook (7–10 min)

Say: *"A group of friends is playing a board game, sitting in a circle. Player 4 just finished their turn. Whose turn is next?"* Let them answer (Player 1, or whoever's physically next — the point is there's no "last player" who ends the game by finishing).

Then: *"That's today's entire session. Every linked list you've built so far — singly, doubly — ends. Somewhere, a pointer hits NULL and stops. Today's list doesn't. The last node points straight back to the first, and the game just keeps going."*

---

## Slide Block A — Introduction & Types (10–18 min) — DELIVER SLIDES AS-IS

Covers: What a circular linked list is → Circular Singly LL vs Circular Doubly LL → node template code (singly) → the three ways to insert, previewed → the running example list (10, 20, 30) used through every dry run that follows.

**Beats to emphasise**

- The one sentence that matters: **the last node's `next` points back to the first node, instead of NULL.** Everything else this session follows from that sentence.
- Contrast against last session directly: a DLL had two ends (head *and* tail) with NULL sitting at each. A circular singly list has only a `next` pointer per node — same shape as a plain singly list — but there is no NULL anywhere in it.
- A Circular Doubly LL also exists (both `prev` and `next`, with `last->next = first` **and** `first->prev = last`) — mention it, but flag explicitly that every dry run and every line of code from here on is the **singly** version. Don't let students expect `prev` pointers to show up in today's code.
- Point at the running example (10, 20, 30) and say it out loud: "remember these three boxes — every insertion dry run today starts from this exact list."

**Checkpoint (at 18 min)** — cold-call:
> *"In a circular singly linked list of 3 nodes, what does the third node's `next` pointer hold?"*
> **Answer:** The address of the first node (head) — never `nullptr`.

---

## ⚡ Activity 1 — Real-World Callout (18–23 min)

**Format:** Real-World Callout · **Exposes:** whether students can generalise "no beginning, no end, last connects back to first" beyond the node diagram they just saw.

**Setup line (say this):**
> *"Thirty seconds — think of one everyday system that has no real 'first' or 'last' turn, where once you reach the end you go right back to the start. Shout it out."*

**What students do:** Call out examples. You write them on the board — no more than 8. (Expect things like a music playlist on repeat, turn order in a board game, a merry-go-round, CPU round-robin scheduling if someone's technical.)

**How to handle wrong answers:** If someone names something linear — e.g. "a queue at a ticket counter" — push once: *"Does it loop back to the first person once the last one is served? No? Then that's linear, not circular. What's the loop-back?"*

**Debrief line:**
> *"Every one of those is a circular linked list in disguise — a node, or a person, or a turn, whose 'next' eventually points back to where you started. That's the entire definition, and there's no NULL in it anywhere. Hold onto your list — we'll check it against the textbook version near the end of the session."*

**Cut rule:** If running late, take 3 callouts instead of an open floor and skip the push-for-linear step. Do not cut the debrief line.

---

## Slide Block B — Insertion: Beginning, End, Specific Position (23–31 min) — DELIVER SLIDES AS-IS

Covers: Insertion at the beginning (dry run + code) → insertion at the end (dry run + code) → insertion at a specific position (dry run + code).

**Beats to emphasise**

- **Insert at beginning:** the new node's `next` points to the old head — that part's instant. But you still have to walk the *entire* list once to find the current last node and re-point *its* `next` to the new head. That traversal is the part students forget; say clearly that this makes it **O(n)**, not O(1) the way DLL head-insert was last session.
- **Insert at end:** walk from head until `temp->next == head` (the deck's own loop condition) — that node is the last one. Attach the new node there, then close the circle by pointing the new node's `next` back to head.
- **Insert at a specific position:** same idea as any singly list — walk `position - 1` steps and splice in — but flag that position 1 is special-cased by calling `insertAtBeginning` directly (the deck's own code) rather than looping into it.
- Run at least the "insert at beginning" C++ snippet live on screen; the walk-to-last-node loop is the one line worth typing out loud, word by word.

<!-- placement: inferred — the code shown on the "Insertion at the Beginning" slide appears to have two code panels merged by the raw extraction (an if/else block, followed by a second, seemingly redundant traversal block); the approach description above is taken from the deck's own numbered steps (Create node → set next → traverse to last node → update last node's next), not from that specific garbled snippet -->

**Checkpoint (at 31 min)** — show hands:
> *"Inserting at the beginning of a circular list — O(1) or O(n)?"*
> **Answer:** O(n). Attaching the new node is instant, but you must still walk the whole list to find the old last node and repoint it to the new head (deck's own complexity table, "Insertion At Beginning" — O(n) time, O(1) space).

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

*(31–36 min — roughly halfway through the session)*

---

## Slide Block C — Deletion: First, Last, Specific Node (36–44 min) — DELIVER SLIDES AS-IS

Covers: Deletion of the first node (dry run + code) → deletion of the last node (dry run + code) → deletion of a specific node (dry run + code).

**Beats to emphasise**

- **Delete first:** same shape as insert-at-beginning — you still must walk to the last node to repoint its `next` at the new head, so it's **O(n)**, not O(1). Contrast explicitly against last session: DLL's `delete_head` *was* O(1), because DLL nodes carry their own `prev` pointer — a circular singly list has no such shortcut.
- **Delete last:** walk until `temp->next->next == head` (the deck's own loop condition) — that "look two nodes ahead" condition is easy to get off-by-one on. Say it slowly, twice.
- **Delete a specific node:** the cleanest of the three, and the one the next activity dry-runs live — find `prev` (the node just before the target), redirect `prev->next` to `target->next`, delete `target`.
- All three deletion functions guard the single-node case (`head->next == head`) before doing anything else. Call this out as the edge case students will forget until their own code crashes on it.

**Checkpoint (at 44 min)** — cold-call:
> *"Why does every deletion function in this deck check `if (head->next == head)` before doing anything else?"*
> **Answer:** That's the one-node list — the node is its own `next`. Deleting it just sets `head` to `nullptr`; there are no neighbours to relink.

---

## ⚡ Activity 2 — Dry-Run Relay: Delete a Specific Node (44–50 min)

**Format:** Live Coding / Dry-Run Relay (whiteboard, pairs) · **Exposes:** whether students can hold a `prev` pointer through a circular structure without a NULL to anchor against — using the deck's own worked example: circular list `Head → 10 → 20 → 25 → 30 → (back to 10)`, deleting node `25`.

**Setup line (say this):**
> *"Grab a partner. Draw four boxes in a circle — 10, 20, 25, 30 — with the last arrow curving back to 10, and Head pointing at 10. Now walk your finger through deleting node 25. One of you narrates each step, the other draws the arrow. Three steps only."*

**What students do:** Step 1 — traverse from head, tracking `prev`, until `prev`'s `next` is the target (25); `prev` lands on 20. Step 2 — redirect `prev->next` to `target->next` (20's arrow now points to 30). Step 3 — erase node 25 from the drawing. Walk the room and spot-check 3–4 pairs' drawings.

**How it surfaces:** Two errors show up reliably. Most common: students stop the traversal one step too late and point `prev` at 25 itself instead of 20 (an off-by-one). Second: they forget the circular wrap and draw 30's arrow going to NULL instead of back to 10.

**Debrief line:**
> *"Notice you never once wrote NULL. You found `prev`, you re-pointed one arrow, you deleted one box. That's the entire deletion — circularity didn't make it harder, it just took away your usual anchor."*

**Cut rule:** If running short, run it once as a single dry-run on the board with the instructor drawing and the whole class calling out each step, instead of pair work. Keep the debrief line verbatim.

---

## ⚡ Activity 3 — Spot the Bug: Termination Condition (50–55 min)

**Format:** Spot the Bug · **Exposes:** the single most dangerous circular-list mistake — using a NULL check to stop traversal — which the deck's own Disadvantages slide names outright as "Infinite Loop Risk."

**Setup line (say this):**
> *"Here's a traversal function for our circular list. It compiles. It will not stop. Find out why before I run it."*

Show on screen:
```cpp
void display() {
    Node* temp = head;
    while (temp != nullptr) {      // <-- bug
        cout << temp->data;
        temp = temp->next;
    }
}
```

**What students do:** 30 seconds silent, then hands up with the fix.

**How it surfaces:** If someone says "add a NULL at the end of the list" — that's missing the point. Redirect: *"The list isn't broken. In a valid circular list, NULL never appears by design. The stopping condition is what's broken."*

**Debrief line:**
> *"Every traversal and search function in this deck uses `do { ... } while (temp != head)` — never a NULL check. In a circular list, NULL never comes. If your loop is waiting for it, you haven't written a bug you'll get an error for — you've written an infinite loop."*

**Cut rule:** If running out of time, skip the pair discussion — read the code aloud, cold-call one student for the fix, deliver the debrief line, move on.

---

## Slide Block D — Traversal, Search, Complexity, Applications, Key Takeaways (55–58 min) — DELIVER SLIDES AS-IS

Covers: Traversal → Searching → Complexity Analysis tables → Applications / Advantages / Disadvantages → Key Takeaways.

**Beats to emphasise**

- This block is mostly recap of what Blocks A–C already showed in code — move fast, deliver the slides as-is, don't re-derive anything.
- The one idea to land formally here: both `display()` and `search()` use `do { ... } while (temp != head)` — a **do-while**, not a `while` — because in a circular list you must process the head node *before* checking whether you've looped back to it. This is the fix from Activity 3; state it plainly.
- Applications slide: Round-Robin Scheduling, circular buffers/queues, multiplayer board games, audio/video playlists — check it against the list students built in Activity 1.
- Advantages/Disadvantages is a good "so when do I reach for this" close. Infinite Loop Risk has now come up twice (Activity 3, and again here) — that repetition is deliberate, don't shorten it.

**Checkpoint** — quick, no fixed minute:
> *"One line: what's the time complexity of `search()` on a circular linked list, and why can it never be better?"*
> **Answer:** O(n) — the target could be the very last node before you loop back to head, so worst case you touch every node once.

---

## Exit Ticket (58–60 min)

> On paper: draw a 3-node circular linked list (10 → 20 → 30 → back to 10) and mark exactly where `head` points and where node 30's `next` pointer points. Then answer: is there a NULL anywhere in your drawing?
> **Answers:** `head` points to 10; node 30's `next` points back to 10 (not NULL). No NULL anywhere — that absence *is* the definition of "circular."

Homework: re-attempt the dry run from memory. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A circular list must have a NULL somewhere, "just at the end" | Every prior list (singly, doubly) they've built ended in NULL | Block A checkpoint — the last node's `next` IS the head's address, never `nullptr` |
| Inserting/deleting at the "start" of a circular list is O(1), same as a DLL | Session 40's DLL `insert_head`/`delete_head` really were O(1) | Block B and Block C checkpoints — walk through why a circular singly list still needs a full traversal to find and repoint the last node |
| A `while (temp != NULL)` loop is a safe way to traverse any linked list | It works for every non-circular list they've built so far | Activity 3 — run it and watch it hang |
| Circular Doubly LL and Circular Singly LL are interchangeable in the code shown | Both were introduced back-to-back on the same slides | Flagged explicitly in Block A: every dry run and code sample after the node template is the singly version only |
| The `prev` pointer used in Activity 2's deletion is a stored field on the node | Last session's DLL really did store a `prev` field, and the name is reused here | Point out in Block C: this `prev` is just a local pointer the function keeps while walking; circular singly nodes only ever store `next` |

---

## Instructor Notes

- **Pacing risk:** Block D packs five topics into 3 minutes on purpose — it's pure recap of what's already been taught live in Blocks A–C plus Activity 3. Do not slow down to re-teach here, or the session overruns.
- **Why 60 minutes, not the usual 45** <!-- placement: inferred -->: this deck covers six distinct operations (3 insertions + 3 deletions), each with its own dry run and code, on top of traversal, search, and a full complexity/applications wrap-up — noticeably more ground than a single-concept session.
- Keep Session 40's DLL summary tables visible or sketched on a side board during Blocks B/C — the O(1) vs O(n) contrasts only land if students can see last session's numbers next to this session's.
- Have a pre-drawn circle template (or sticky notes) ready before Activity 2 — setting it up live costs minutes you don't have.
- <!-- placement: inferred --> The raw slide text for "Deletion of a Specific Node" and "Searching" carries a stray leftover title ("Deletion of the Last Node — Defining Class Node") on two slides in the source deck; the code shown on those slides is unambiguous (delete-by-value and search-by-value respectively), so the Slide Block descriptions above follow the code, not the mislabeled titles. Worth flagging to the content team.
