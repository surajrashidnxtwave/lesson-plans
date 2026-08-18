# Session 38 — Singly Linked List

**Duration** 60 min · **Topic** Linked List · **Prerequisite** Arrays (fixed size, contiguous storage, indexing)
**Session type** Concept lecture

<!-- placement: inferred — duration set above the 45-min default because this session introduces a brand-new data structure AND covers four distinct algorithms (construct-from-array, print, length, search), each with its own approach + dry run + code + complexity. A single-concept 45-min session would force cutting one of the four; 60 min keeps all of them intact. -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Singly Linked List | https://docs.google.com/presentation/d/1MFFp2bxzh6l6-4LyxaEP2mdk3HiEy2fnB5rPU40ioRg/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the two drawbacks of arrays — fixed size and the contiguous-memory requirement — that motivate linked lists. *(REMEMBERING)*
2. Explain what a node is and how `data` + `next` together form a singly linked list. *(UNDERSTANDING)*
3. Differentiate singly, doubly, and circular linked lists by how their `next`/`prev` pointers are wired. *(ANALYZING)* <!-- placement: inferred from Key Takeaways slide 60 -->
4. Construct a singly linked list from an array by tracing and implementing the `arraytoLL` approach. *(APPLYING)*
5. Trace and implement the traverse-and-print, count-length, and search-for-value algorithms on a singly linked list. *(APPLYING)*
6. Determine the time and space complexity of each of the four operations covered this session. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval on Session 37: Bitwise XOR For a Given Range (0–7 min)

Say: *"Eight quick ones on last session's XOR problem before we move to something completely different. No penalty, just show me what stuck."*

**Q1.** What did `XOR(5, 6, 7, 8, 9, 10)` evaluate to in last session's dry run?
`A` 0 · `B` 11 · `C` 15 · `D` 6

**Q2.** What was the time complexity of the brute-force approach (loop, XOR-ing every number from `left` to `right`)?
`A` O(1) · `B` O(log n) · `C` O(right − left) · `D` O(right × left)

**Q3.** What was the space complexity of BOTH the brute-force and the optimal approach?
`A` O(n) · `B` O(1) · `C` O(log n) · `D` O(right − left)
→ *Read:* If a chunk of the class flips this to O(n), they're confusing "the loop runs n times" with "the loop uses n space." Point out: only the accumulator `ans` and the loop counter exist — nothing grows with the range.

**Q4.** In the optimal approach's helper, if `n % 4 == 2`, what does `xorOnetoN(n)` return?
`A` 0 · `B` 1 · `C` n · `D` n + 1

**Q5.** *(MSQ — select all that are TRUE about `xorOnetoN(n)`)*
`A` returns `n` when `n % 4 == 0` · `B` returns `1` when `n % 4 == 1` · `C` returns `0` when `n % 4 == 2` · `D` returns `0` when `n % 4 == 3`

**Q6.** The full optimal solution was `xorRange(left, right) = ?`
`A` `xorOnetoN(left) ^ xorOnetoN(right)` · `B` `xorOnetoN(right) ^ xorOnetoN(left - 1)` · `C` `xorOnetoN(right - left)` · `D` `xorOnetoN(right) + xorOnetoN(left)`
→ *Read:* If fewer than ~70% get this, that's the whole optimal approach not landing — it's the "compute cumulative-from-1, then cancel out the part you don't want" trick, which recurs constantly in DSA. Worth 30 seconds re-deriving on the board before you start today's new topic.

**Q7.** What was the time complexity of the OPTIMAL approach?
`A` O(right − left) · `B` O(log n) · `C` O(1) · `D` O(n)

**Q8.** True or False: the optimal approach avoids looping over the range entirely.
`A` True · `B` False

**Running it** — poll tool, ~50 s per question, project the distribution after each. Total 7 min including your reads.

---

## Hook (7–11 min)

Draw a 5-slot array on the board, boxes touching edge to edge, labelled `arr[0]` through `arr[4]`, with memory addresses underneath (`1024, 1028, 1032...` — same numbers as the deck).

Ask: *"I want to add a 6th element. Show of hands — does it just slide in next door?"*

Erase the "next door" space to show it's already someone else's memory. Then: *"This is the array's whole problem: fixed size, and it needs one unbroken block of memory. Today's data structure fixes both — by giving up the one thing arrays had for free: neighbours who are actually next to each other."*

---

## Slide Block A (11–20 min) — DELIVER SLIDES AS-IS

Covers: Drawbacks of Arrays (fixed size, contiguous memory) → Introduction to Linked List → Node structure (data + pointer) → Non-Contiguous Memory Allocation → Types of Linked List (singly, doubly, circular) → Head & Tail → Practical Example (Music Playlist) → Node class in C++ and Python.

**Beats to emphasise**

- **Fixed size vs. dynamic size** is the entire motivation — don't rush past the two array-drawback slides, they're the "why" for everything that follows.
- **Non-contiguous memory** (slide 11): nodes can live anywhere in memory; only the pointers wire them together. Point at the diagram's scattered addresses — this is the direct fix for the array's second drawback from the Hook.
- On **Types of Linked List**: the one differentiator that matters for exam-style questions is what the `next` (and `prev`, for doubly) pointers do at the two ends. Singly = last node's `next` is null. Doubly = both ends null, two-way pointers. Circular = last node points back to the first, no null anywhere.
- **Head and Tail** are structural terms, not data terms — head is "the node you start from," tail is "the node with nothing after it." Reinforce with the Music Playlist example (first song to play = head, last song = tail).
- Show both the **C++ Node class** (with its multiple constructors) and the **Python Node class** side by side — Python's `self.next = address` is the same idea with less ceremony.

**Checkpoint (at 20 min)** — cold-call one student:
> *"Give me one sentence on why a linked list doesn't need contiguous memory the way an array does."*
> **Answer:** Because each node only needs to know the *address* of the next node — the nodes themselves can live anywhere in memory, wired together by pointers instead of by sitting side by side.

---

## ⚡ Activity 1 — Real-World Callout (20–24 min)

**Format:** Real-World Callout · **Exposes:** that "dynamic size" and "non-contiguous memory" feel abstract until tied to something students already use — the deck's own Music Playlist example.

**Setup line (say this):**
> *"Think of the last playlist you added a song to, or removed a song from, on your phone. Shout out: roughly how many songs was it, and did you ever have to tell the app in advance how many songs you'd add?"*

**What students do:** Call out playlist sizes and app names (Spotify, YouTube Music, etc.). Quick tally on the board.

**How to handle it:** If someone insists "the app probably just uses an array, it's fine" — push once: *"Every single time you add one song, does the app rebuild the entire array from scratch?"* Let them land on why that doesn't scale — that's the Drawbacks-of-Arrays slide, now with a face on it.

**Debrief line:**
> *"That's exactly the linked-list use case from the slides — the playlist's head is the first song, the tail is the last, and adding a song is just wiring one more node. No resizing, no shifting, no wasted contiguous block sitting empty just in case."*

**Cut rule:** If running short, skip the tally, take 2 shout-outs, and go straight to the debrief line.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–38 min) — DELIVER SLIDES AS-IS

Covers: Practice Problem 1 — Construct a Linked List from an Array (`arraytoLL`): problem statement, approach, full dry run for `arr = [1, 3, 5, 7, 9]`, C++ and Python code, complexity analysis.

**Beats to emphasise**

- Name the three roles out loud every single time they appear in the dry run: **head** (fixed, never moves), **cur** (the pointer that's always at the "end so far," used to link the next node), **temp** (the brand-new node just created, about to be linked in).
- Walk the dry run's own sequence exactly as the slides show it: create node → link `cur->next = temp` → move `cur = temp` → repeat. This three-step rhythm is the pattern the class needs to internalise before Activity 2.
- On complexity: **O(n)** time because the loop runs once per array element; **O(n)** space because you allocate one new node per element — this is a rare case where an operation costs both O(n) time *and* O(n) space, worth calling out explicitly since most of today's later operations will be O(n) time but only O(1) space.

**Checkpoint (at 38 min)** — cold-call one student:
> *"In the `arraytoLL` dry run, what does the `cur` pointer do that the `head` pointer doesn't?"*
> **Answer:** `head` stays fixed at the first node forever, so you can always find your way back into the list. `cur` is the one that actually moves forward, getting re-linked to each new node as it's created — head never moves, cur always moves.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: Build the List (38–46 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can correctly track the head-vs-cur-vs-temp roles while pointer-chasing — the one skill the rest of the linked-list unit depends on.

**Setup line (say this):**
> *"I need 5 volunteers at the board, one per number in `arr = [2, 4, 6, 8, 10]`. Each of you IS a node — draw your own box with your data value inside. The rest of the class calls out what pointer to draw next, and where."*

**What students do:** Volunteer 1 draws `head` pointing at themselves (this is also `cur`). The class calls out the next step; volunteer 2 draws their box, the class decides whether `cur->next` should point to them, then `cur` moves. Repeat through node 5, ending with an arrow from the last node to `NULL`.

**How it surfaces:** If the class tries to move `cur` forward *before* wiring `cur->next` to the new node, stop and rewind: *"You just orphaned your new node — nothing points to it anymore. What has to happen first?"* The other common miss is forgetting the final `NULL` arrow — don't let the relay end without it.

**Debrief line:**
> *"Head never moved once, the whole relay — that's the one pointer callers need to find the list again. Everything else was `cur` doing the work and `temp` being temporary. If you can run this relay in your head, you can code `arraytoLL` from scratch."*

**Cut rule:** If running short, drop to 3 volunteers instead of 5 and skip the discussion of the final `NULL` arrow — keep the head-vs-cur-vs-temp distinction, that's the whole point.

---

## Slide Block C (46–50 min) — DELIVER SLIDES AS-IS

Covers: Practice Problems 2–4 — Print the Linked List (`printLL`), Count the Nodes (`lengthLL`), Search for a Value (`searchLL`) — approach, code, and complexity for each, plus the Key Takeaways recap slides.

**Beats to emphasise**

- All three share the same skeleton: start a temp pointer at `head`, loop `while (temp != nullptr)`, do one small thing per node, advance `temp = temp->next`. Say this explicitly — it's the same shape three times with a different "one small thing" in the middle (print / count++ / compare-to-target).
- `searchLL` is the one with a branch: it can `return true` and exit *early*, the moment it finds a match — the other two must always walk the full list.
- All three are **O(n) time**; all three are **O(1) space** — exactly one pointer (plus a counter for `lengthLL`) regardless of how long the list is. Contrast this with Slide Block B's `arraytoLL`, which was O(n) space.

**Checkpoint (at 50 min)** — show of hands:
> *"`printLL`, `lengthLL`, and `searchLL` are all O(n) time — but why is `lengthLL`'s O(n) unavoidable, while `searchLL`'s O(n) is only a worst case?"*
> **Answer:** `lengthLL` must visit every node no matter what, because you can't know the count until you hit `null`. `searchLL` can return early the instant it finds a match — it's only forced to walk the entire list when the value isn't there at all (or is the very last node).

---

## ⚡ Activity 3 — Spot the Bug (50–57 min)

**Format:** Spot the Bug · **Exposes:** that students copy the `while (temp != nullptr)` pattern without registering *why* the pointer-advance line inside the loop is what makes it eventually terminate.

**Setup line (say this):**
> *"This is `printLL` straight from the slides, except I removed exactly one line on purpose. Tell me what happens when you run it, and why — first correct explanation wins, not the first shout."*

```cpp
void printLL(Node* head){
    Node* temp = head;
    while (temp != nullptr){
        cout << temp->data << " ";
    }
}
```

**What students do:** 60 seconds silent, then hands up.

**How to handle wrong answers:** If they say "SyntaxError" — no, this compiles and runs fine, the bug only shows at runtime. If they say "it prints nothing" — no, it prints the first value... and keeps printing it. Push until someone says the words "infinite loop."

**Debrief line:**
> *"`temp` never moves, so the condition `temp != nullptr` never changes. This is the linked-list version of forgetting `i++` in a for-loop — except here it doesn't just skip an iteration, it hangs your program forever."*

**Cut rule:** If running long, skip the 60-second silent think, go straight to a show-of-hands vote on "infinite loop vs. crash vs. prints nothing," then debrief immediately.

---

## Exit Ticket (57–60 min)

**Exit ticket** — on paper before anyone leaves:

> Draw a 3-node singly linked list holding any three numbers of your choice, then write what `lengthLL(head)` would return for it.
> **Answer:** `3` — regardless of the values, the count is the number of nodes.

**Homework:** Re-attempt today's array-to-linked-list dry run from memory — build the list for `arr = [2, 4, 6, 8]` on paper, one node at a time, labelling `head`, `cur`, and `temp` at every step. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| A linked list's nodes sit next to each other in memory, like an array | The diagrams draw nodes left-to-right in a row | Slide 11's scattered-address diagram, reinforced in the Hook by erasing the array's "next door" slot |
| Traversal in a singly linked list can go backward if needed | Students haven't yet seen a doubly linked list to contrast against | Pointing out there is no `prev` field in the C++/Python Node class shown this session — only `next` exists |
| `searchLL` returns the position/index of the value | Arrays train students to think "search = index" | Re-reading the code: it returns `true`/`false` (a boolean), not an index |
| Building the list from an array is "just linking," so it should be O(1) | The per-step work (one link) looks trivial in isolation | Counting the loop iterations in the dry run — n elements, n iterations, so O(n) |
| Forgetting the `temp = temp->next` advance causes "a small bug," not a crash-level problem | School debugging experience is mostly about wrong *values*, not infinite execution | Activity 3 — let the infinite loop actually run (or trace it far enough) so it stops being hypothetical |

---

## Instructor Notes

- **Pacing risk:** Slide Block A has six sub-topics (drawbacks, intro, types, head/tail, real-world, node class) in 9 minutes — don't let "Types of Linked List" balloon; doubly and circular get one sentence each, they're previewed here and taught properly in later sessions.
- **Activity 2 is the load-bearing activity of this session.** If you must cut something else to protect its 8 minutes, do that — everything from Session 39 onward assumes students can pointer-chase confidently.
- **Have the head/cur/temp vocabulary on the board before Activity 2 starts** — writing it fresh mid-relay costs time you don't have.
- **The Classroom Quiz has no fixed question set in this material** — pull 5-6 from the platform bank covering drawbacks-of-arrays, node structure, and the four practice-problem complexities before class starts, don't build it live.
- **Search example numbers in the deck (slides 51-52) are visually garbled in the source extraction** — the dry-run diagrams show inconsistent list values between the "found" and "not found" examples. Rebuild your own clean example on the board (e.g., list `1, 3, 5, 7, 9`, search for `5` → true, search for `4` → false) rather than trying to reproduce the slide's numbers exactly. <!-- flagged for review, see final report -->
