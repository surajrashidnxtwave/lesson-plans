# Session 47 — Intersection of Linked Lists

**Duration** 60 min · **Topic** Intersection of Two Singly Linked Lists · **Prerequisite** Linked list traversal & two-pointer basics (Session 46 — Merge Two Sorted Linked Lists) <!-- placement: inferred -->
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Intersection of Linked Lists | https://docs.google.com/presentation/d/1ZUJxVBoauXcUtxsWNqsSvgrgJE-oeeb_sAFOGYW1J10/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the intersection-of-two-linked-lists problem precisely: return the first shared node of two singly linked lists, or `null` if none exists. *(REMEMBERING)*
2. Explain why two nodes holding the **same value** are not necessarily the intersection point — only a shared **node (memory address)** counts. *(UNDERSTANDING)*
3. Trace and implement the brute-force approach: store list 1's node pointers in a map, then scan list 2 against that map. *(APPLYING)*
4. Trace and implement the length-difference approach: compute both lengths, advance the longer list's pointer by the difference, then walk both pointers together. *(APPLYING)*
5. Trace and implement the optimal two-pointer approach: walk both pointers one step at a time, redirecting each to the other list's head when it hits `null`, until they meet. *(APPLYING)*
6. Compare the three approaches on time and space complexity and justify why the optimal approach is preferred. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 46 (Merge Two Sorted Lists) (0–5 min)

Say: *"Quick recall check on yesterday's merge before we build on it. Answer solo, no discussion yet."*

**Q1.** In the merge-two-sorted-lists algorithm, why do we create a dummy node before merging begins?
`A` To store the final answer permanently · `B` To avoid special-casing the very first node inserted into the merged list · `C` It's required syntax · `D` To count the combined length

**Q2.** `temp1` and `temp2` point at the current nodes of List 1 and List 2. When `temp1->data <= temp2->data`, what happens?
`A` `cur->next = temp2`, move `temp2` · `B` `cur->next = temp1`, move `temp1` forward, then move `cur` forward · `C` Both pointers move forward together · `D` The two lists are swapped

**Q3.** Once `temp1` becomes `null` (list 1 is exhausted), what do we do with the rest of list 2?
`A` Discard it · `B` Attach it directly as `cur->next` — it's already sorted, no more comparisons needed · `C` Reverse it first · `D` Merge it node-by-node against nothing

**Q4.** *(MSQ — pick 2)* Which are true of the merge algorithm's complexity?
`A` Time complexity is O(M + N) · `B` Time complexity is O(M × N) · `C` Space complexity is O(1) extra (a handful of pointers) · `D` Space complexity is O(M + N) because a brand-new list is built
→ *Read:* If many pick D, that's the misconception to kill fast: no new nodes are ever created — only re-linked. That exact "re-link, don't recreate" habit is what today's Flatten-adjacent session-after-next reuses.

**Q5.** What does `mergeTwoLists` finally `return`?
`A` `temp1` · `B` `cur` · `C` `dummy` · `D` `dummy->next`
→ *Read:* If several pick "dummy," clarify on the spot: `dummy` is scaffolding, never part of the answer — the real list starts one node after it.

**Q6.** In Example 2 of that session, both lists contained the values `3` and `8`. In the merged output, what happened to those duplicate values?
`A` Only one copy of each survives · `B` Both copies survive, appearing back-to-back in the merged sequence · `C` The merge errors out on duplicates · `D` They get combined into a single node

**Q7.** Do we ever allocate a brand-new node to hold a data value while merging?
`A` Yes, one new node per value · `B` No — existing nodes from list 1 and list 2 are re-linked by pointer; only the dummy is newly created

**Running it** — poll tool, ~30 s per question, project results after each. Total 5 min including reads.

---

## Hook (5–7 min)

<!-- placement: inferred — no hook slide in deck; built directly from the two worked examples -->

Draw this on the board, no explanation yet:

```
List 1:  3 → 2 → 6 → 7 → 4 → NULL
List 2:  5 → 9 → 3 → 1 → 6 → 7 → 4 → NULL
```

Ask: *"Somewhere, these two lists become the same list. Not the same values — the same actual nodes, sharing memory from that point on. Where does that happen, and how would you get a computer to find it without just eyeballing the diagram?"*

Let a few guesses land — someone will say "look for node 6." Then: *"You're right that it's 6. But notice list 2 also has a `3` in it — same digit as list 1's `3`. Is that the intersection? No. That's the trap this entire session is built around: same value, different node. By the end of the hour you'll have three different ways to tell them apart, from the brute-force way to the one-line-of-genius way."*

---

## Slide Block A (7–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range from deck: Problem Statement, Examples 1 & 2, Brute Force approach, dry run, pseudocode, complexity, code -->

Covers: Problem Statement → Example 1 → Example 2 → Brute Force Approach → Dry Run → Pseudocode → Complexity Analysis → C++ Code.

**Beats to emphasise**

- Nail the exact contract from the Problem Statement: *"return the first intersection node if there is one; otherwise, return null."* Not the value at that node — the node itself.
- Example 1: `3→2→6→7→4` and `5→9→3→1→6→7→4`, merging at node 6. Example 2: `0→1→2→20→40` and `3→5→20→40`, merging at node 20. Both examples share a tail after the intersection — point that out explicitly, it's the whole geometry of the problem.
- The brute-force approach stores list 1's nodes in `map<Node*, int>` — **the key is the node pointer (its address), not the data value.** Say this out loud, twice. It is the single most important sentence in the block.
- Walk the dry run once at full narration speed (nodes 5, 9, 3, 1 miss the map; node 6 hits), then don't re-narrate every repeated slide — the mechanic is simple once shown.
- On the node with value `3` in list 2 during the dry run: the deck's own annotation says it plainly — *"3 is not in the map (same value, different nodes)."* Point at it.

**Checkpoint (at 19 min)** — cold-call:
> *"Why do we store list 1's nodes in a map instead of just comparing values as we scan both lists side by side?"*
> **Answer:** Because two different nodes on two different lists can hold the same value without being the intersection point. We need to detect the exact same node — same memory location — not just equal data. That's why the map is keyed on the pointer.

---

## ⚡ Activity 1 — Spot the Bug: Same Value, Different Node (19–24 min)

**Format:** Spot the Bug · **Exposes:** the value-equality-vs-node-identity confusion the deck itself flags mid-dry-run.

**Setup line (say this):**
> *"Map `m` now holds every node from list 1. I'm walking `temp2` through list 2 one node at a time. Every time I land on a node, you tell me: found or not found — and defend it using what's actually stored in the map, not the number on the node."*

**What students do:** Call the nodes of list 2 in order — `5, 9, 3, 1, 6` — before each one, take a quick show-of-hands vote (found / not found), then reveal.

**How to handle wrong answers:** The trap node is `3`. If several vote "found" when `temp2` lands on it, stop and ask: *"Found means the map contains this exact node object. Is list 2's node-with-value-3 the same object as list 1's node-with-value-3, or two separate nodes that happen to store the same integer?"* Walk to the board and point at the two separate boxes.

**Debrief line:**
> *"The map's key is a memory address, not a number. `3 == 3` tells you nothing here — it's `same_node == same_node` that matters. That's the whole reason brute force needs a map at all instead of just comparing values."*

**Cut rule:** If running short, skip straight to the `3` node and the `6` node — those are the two that matter (the trap and the answer) — and drop the earlier misses (`5`, `9`, `1`).

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (29–41 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range: Better Approach, dry run, pseudocode, complexity, code -->

Covers: Better Approach (length difference) → Dry Run → Pseudocode → Complexity Analysis → C++ Code.

**Beats to emphasise**

- Trade-off framing up front: *"No map this time. We pay for it with two full traversals instead of one."*
- The dry run computes `length(list1) = 5`, `length(list2) = 7`, `diff = |5 - 7| = 2`.
- The crux step: advance **the longer list's pointer** (`temp2`, list 2) forward by `diff = 2` nodes *before* starting the joint walk. Say explicitly why: it makes both pointers the same distance from their respective tails.
- After alignment, the comparison is `temp1 == temp2` — direct node-identity comparison, no map needed. This is the same "same node, not same value" idea from Block A, now solved differently.
- Complexity payoff: **O(M + N) time, O(1) space** — better than brute force's O(M) space, same time complexity class.

**Checkpoint (at 41 min)** — cold-call:
> *"List 1 has length 5, List 2 has length 7. Which pointer do we advance first, and by how many nodes, before the synchronized walk starts?"*
> **Answer:** Advance list 2's pointer (the longer list) by `diff = 2` nodes. Then move both pointers one step at a time together.

---

## ⚡ Activity 2 — Dry-Run Relay: Align and Walk (41–46 min)

**Format:** Live Dry-Run Relay (whiteboard) · **Exposes:** advancing the wrong list's pointer, and losing count of the remaining `diff` steps.

**Setup line (say this):**
> *"Three of you, up to the board. Student one draws List 1 (`3→2→6→7→4`) and List 2 (`5→9→3→1→6→7→4`) exactly as shown, and counts each length out loud. Student two says which pointer moves, and how many steps, before anything else happens. Student three moves both pointers one node at a time, calling out each comparison, until they land on the same node."*

**What students do:** Student 1 counts (`length = 5`, `length = 7`); student 2 states `diff = 2` and advances list 2's pointer two nodes; student 3 walks both pointers together (`3 & 3`→different, wait — deck's alignment lands pointers on `6 & 6` directly after the 2-step advance) calling matches until intersection at node 6.

**How to handle wrong answers:** If student 2 advances list 1's pointer (the shorter list) instead, stop the relay and ask the room: *"Which list has farther to go before it runs out? That's the one that needs the head start."*

**Debrief line:**
> *"Notice we never touched a map this whole time — just two counts and a head start. That's the entire trade: two extra passes to learn the lengths, in exchange for dropping the O(M) space cost."*

**Cut rule:** If short on time, state the lengths and `diff = 2` directly instead of having student 1 count live, and start the relay straight from the alignment step.

---

## Slide Block C (46–53 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range: Optimal Approach, dry run, pseudocode, complexity, code, Key Takeaways -->

Covers: Optimal Approach (two-pointer switch) → Dry Run → Pseudocode → Complexity Analysis → C++ Code → Key Takeaways.

**Beats to emphasise**

- The approach in one sentence: two pointers start at the two heads, move one step at a time, and whenever a pointer runs off the end of its own list, it redirects to the **head of the other list** and keeps going.
- Walk the dry run's switch moment explicitly: `temp1` hits `null` at the end of list 1 while `temp2` is still mid-list-2 → `temp1` redirects to the head of list 2. Later `temp2` hits `null` and redirects to the head of list 1. They then land on the same node (6) together.
- The insight to say out loud: switching lists **equalizes the total distance each pointer travels** (each ends up walking `M + N` nodes total) — so by the time they've both switched once, they're guaranteed to be the same distance from the intersection, without ever computing a length.
- Complexity: **O(M + N) time, O(1) space** — same complexity class as Block B, but in one pass with no separate length-computation step.
- Key Takeaways slide (deliver as-is): brute force → map; better → align by length; optimal → two pointers that switch lists. Final headline numbers: optimal is O(M + N) time, O(1) space.

**Checkpoint (at 53 min)** — cold-call:
> *"`temp1` just hit `null`, but `temp2` still has nodes left. What happens to `temp1` next?"*
> **Answer:** It's redirected to the head of List 2 (the *other* list) and keeps moving one step at a time — it does not stop, and it does not reset to its own head.

---

## ⚡ Activity 3 — Predict the Output: Does It Ever Stop? (53–57 min)

**Format:** Predict-the-Output · **Exposes:** the belief that the switching trick only works when the lists actually intersect, and uncertainty about what happens when they don't.

**Setup line (say this):**
> *"Same two-pointer, switch-on-null algorithm — but now imagine two completely separate linked lists that never intersect at all. Predict: does this loop ever end? If it does, what does it return?"*

**What students do:** Quick vote — "loops forever" vs. "ends and returns null" vs. "ends and returns garbage" — before you resolve it.

**How to handle wrong answers:** If "loops forever" wins the vote, trace it live: each pointer travels its own list, switches exactly once, then travels the other list. After `M + N` total steps each, both pointers hit `null` **at the same step** — `temp1 == temp2` becomes `null == null`, which is `true`, so the loop's exit condition fires and `null` is returned correctly.

**Debrief line:**
> *"The algorithm never needed to know in advance whether the lists intersect. The switch-and-meet trick handles 'yes' and 'no' with the exact same code — that's what makes it the optimal answer, not just a faster one."*

**Cut rule:** Skip the vote and state the answer directly, but keep the trace of *why* both pointers hit `null` on the same step — that's the point, not the trivia.

---

## Exit Ticket (57–60 min)

> In your own words: why does redirecting a pointer to the *other* list's head — instead of just stopping — make the optimal approach work without ever computing either list's length?
> **Answer:** Redirecting equalizes the total distance both pointers travel (each walks `M + N` nodes by the time it has switched once), so they arrive at the intersection point at the same step — no length calculation required.

Homework: re-attempt the dry run from memory. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Two nodes with the same value are the intersection | Value-based thinking is the default from every prior list algorithm | Activity 1 — walking the node-with-value-3 trap and pointing at two distinct memory locations |
| The map stores data values, not node addresses | `map<Node*, int>` reads unfamiliar; students assume the int is the payload | Stating explicitly in Slide Block A that the key is the pointer, the `0` is just a placeholder value |
| Once a pointer's list ends, the algorithm is done | Every prior traversal pattern this course has taught stops at `null` | Slide Block C's checkpoint — showing `temp1` redirected to List 2's head instead of stopping |
| The switch-trick approach only works if the lists actually intersect | No worked example in the deck shows the non-intersecting case | Activity 3 — tracing that both pointers hit `null` simultaneously and the loop exits cleanly |
| The brute-force map approach is "good enough," since it works | It's the first correct solution students see, so it feels final | Key Takeaways slide's side-by-side complexity comparison: O(M) space vs. O(1) for both later approaches |

---

## Instructor Notes

- **This session is dense: three complete approaches in 60 minutes.** Do not let Slide Block A's dry run (16+ slides of node-by-node map checks) run long — narrate the mechanic once in full, then move briskly through the repeated frames. The pacing budget assumes you do not re-explain the map lookup for every single node.
- **The non-intersecting edge case is not shown anywhere in the deck** — both worked examples (and all three approaches' dry runs) assume the lists do intersect, per the Problem Statement's "if there is one; otherwise return null." Activity 3 was built specifically to cover this gap; treat it as required, not optional, since it's the only place students see the `null`-return path. <!-- placement: inferred -->
- **Keep the "same value, different node" framing alive across all three blocks.** It's introduced in Block A, resolved differently in Block B (length alignment) and Block C (pointer switching) — naming it each time is what turns three separate algorithms into one coherent story about node identity.
- **The Classroom Quiz slot has no pre-built questions for this course** — pull 5–6 from the platform bank live; do not skip it, it's your only mid-session check that the map-vs-value distinction has actually landed before you move to Block B.
- **Have the three approaches' complexities on the board simultaneously by the end** (O((M+N)logM)/O(M), O(M+N)/O(1), O(M+N)/O(1)) — the Key Takeaways slide moves fast and students need the visual comparison to stick.
