# Session 48 — Flatten a Linked List

**Duration** 45 min · **Topic** Flattening a Multi-Level (Down-Pointer) Linked List · **Prerequisite** Merge Two Sorted Linked Lists (Session 46) — this session directly reuses that merge logic, adapted to the `down` pointer <!-- placement: inferred -->
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Flatten a Linked List | https://docs.google.com/presentation/d/1XUP_2McMkTDtrUTQ300_trC8QlaPsoe-vbzJZqHQAeM/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the flatten-a-linked-list problem: each node has a `next` pointer and a `down` pointer to a sorted sub-linked list; return one single-level list, sorted, using `down` links. *(REMEMBERING)*
2. Explain why `flattenLL` can reuse the `mergeTwoLists` logic from Session 46 almost unchanged — only the pointer name changes, from `next` to `down`. *(UNDERSTANDING)*
3. Trace and implement the iterative approach: repeatedly merge the growing flattened list with the next horizontal sub-list until none remain. *(APPLYING)*
4. Trace the `flattenLL` driver function's base case and explain what it returns when there is only one sub-list to flatten. *(APPLYING)*
5. Derive why the time complexity is O(N²M) even though each individual merge is a simple two-pointer walk. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 47 (Intersection of Linked Lists) (0–5 min)

Say: *"Recall check on yesterday's intersection problem before we reuse a piece of it today."*

**Q1.** In the brute-force intersection approach, what gets stored as the **key** in the map while scanning list 1?
`A` The data value of each node · `B` The node pointer itself (its memory address) · `C` The index position of the node · `D` Both value and position
→ *Read:* If many pick A, that's the value-vs-identity confusion resurfacing. Spend 30 seconds re-stating "same value, different node" before Slide Block A today — flattening also depends on comparing and re-linking real nodes, not values.

**Q2.** List 1 and list 2 both contained a node with value `3` at different positions. Was that node reported as the intersection?
`A` Yes, since `3 == 3` · `B` No — it's a different node object even though the data matches

**Q3.** *(MSQ — pick 2)* Which are true of the "better approach" (align by length difference)?
`A` It needs no extra map or hash structure · `B` Its space complexity is O(1) · `C` It runs in O((M+N) log M) time, same as brute force · `D` You advance the *longer* list's pointer by the length difference before the joint walk begins

**Q4.** In the optimal two-pointer approach, when a pointer reaches the end (`null`) of its own list, what happens to it?
`A` The algorithm stops and returns `null` immediately · `B` It's redirected to the head of the *other* list and keeps moving · `C` It resets to the head of its own list · `D` It's discarded; only one pointer continues
→ *Read:* If this is weak, recap it before today's Approach slide — "redirect a pointer instead of stopping it" is the exact habit students need for the merge-based flattening logic coming up.

**Q5.** What is the time and space complexity of the optimal (two-pointer switch) approach?
`A` O(M+N) time, O(M) space · `B` O((M+N) log M) time, O(1) space · `C` O(M+N) time, O(1) space · `D` O(M×N) time, O(1) space

**Q6.** True or False: if the two lists do not intersect at all, the optimal two-pointer algorithm loops forever.
`A` True — it never terminates · `B` False — both pointers hit `null` at the same step and the loop exits, returning `null`

**Q7.** Across all three approaches from that session, what stayed exactly the same regardless of technique used?
`A` The final answer returned (same intersection node, or `null`) · `B` The time complexity · `C` The space complexity · `D` The number of passes made over the lists

**Running it** — poll tool, ~30 s per question, project results after each. Total 5 min including reads.

---

## Hook (5–7 min)

<!-- placement: inferred — no hook slide in deck; built directly from Example 1's diagram -->

Draw this on the board (values only, skip drawing every arrow):

```
Head → 5
        ↓
        2 → 8
        ↓
        1 → 7 → 9 → 10
        ↓
        4 → 14
        ↓
        3 → 6 → 15
```

Say: *"Five separate sorted lists, stacked. Each one hangs off the node above it through a `down` pointer instead of `next`. Your job: turn this into one sorted list, using only `down` links, no `next`. Before I show you the trick — does this remind you of anything from yesterday?"*

Let someone land on "it's like merging two lists, just... more of them." Confirm: *"Exactly. Today's entire algorithm is Session 46's `mergeTwoLists`, called over and over, walking `down` instead of `next`."*

---

## Slide Block A (7–19 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range: Problem Statement, Examples 1 & 2, Approach, Dry Run part 1 (merge A&B, then with C) -->

Covers: Problem Statement → Example 1 → Example 2 → Approach → Dry Run (label sub-lists A–E; merge A & B; merge result with C).

**Beats to emphasise**

- Problem contract: each node has a `next` *and* a `down`; each `down`-chain is already sorted ascending; output must use `down` pointers only, single level, fully sorted. Example 1's answer: `1→2→3→4→5→6→7→8→9→10→14→15`.
- Label the five horizontal sub-lists exactly as the deck does — **A: 5, B: 2→8, C: 1→7→9→10, D: 4→14, E: 3→6→15** — this labelling is what the whole dry run hangs on, put it on the board and leave it up.
- The Approach, stated plainly: start with the first two sub-lists, merge them (comparing values, linking via `down`); take that merged result and merge it with the *next* sub-list; repeat until none remain.
- Step 1 dry run (merge A & B): compare `5 & 2` → `2` smaller; compare `5 & 8` → `5` smaller; attach remaining `8`. Result: `2→5→8`.
- Step 2 dry run (merge result with C): the growing list `2→5→8` merges against `1→7→9→10` one comparison at a time, producing `1→2→5→7→8→9→10`.
- Say explicitly: *"This is `mergeTwoLists` from yesterday. Same comparisons, same dummy-node trick — only `->next` became `->down`."*

**Checkpoint (at 19 min)** — cold-call:
> *"We just merged sub-lists A and B into `2→5→8`. What do we merge that result with next — and do we ever go back and re-merge A or B individually again?"*
> **Answer:** We merge the growing list next against sub-list C. A and B are never touched again individually — the merged result simply keeps absorbing one more sub-list at a time.

---

## ⚡ Activity 1 — Dry-Run Relay: Merge A & B by Hand (19–23 min)

**Format:** Live Dry-Run Relay (whiteboard) · **Exposes:** treating this as a brand-new algorithm instead of recognizing it as yesterday's merge with a renamed pointer.

**Setup line (say this):**
> *"Board time. Draw List A — just the node `5`. Draw List B — `2` then `8`. Merge them exactly the way you merged two sorted lists last session — smaller value first — except every link you draw is a `down` arrow, not a `next` arrow. Call out each comparison before you draw it."*

**What students do:** One student compares `5` vs `2` (`2` wins, goes first), then `5` vs `8` (`5` wins, goes next), then attaches the remaining `8`. Final: `2→5→8`.

**How to handle wrong answers:** If a student instinctively draws `next` arrows out of habit, stop and ask: *"What pointer did the problem statement tell us to use for the output?"* Have them redraw with `down`.

**Debrief line:**
> *"Nothing about the comparisons changed from yesterday. The only new idea in this entire session is: do this merge more than once, feeding the result back in as one side of the next merge."*

**Cut rule:** If short on time, narrate the three comparisons verbally instead of having a student draw them, but keep the "which pointer" correction if it comes up.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (28–36 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide range: Dry Run part 2 (merge with D, then E), Pseudocode (mergeTwoLists adapted + flattenLL driver), Complexity, Code, Key Takeaways -->

Covers: Dry Run (merge growing list with D, then with E → final flattened output) → Pseudocode (`mergeTwoLists` with `down`, and the `flattenLL` driver loop) → Complexity Analysis → C++ Code → Key Takeaways.

**Beats to emphasise**

- Step 3 (merge with D: `4→14`): growing list `1→2→5→7→8→9→10` absorbs `4` (lands between `2` and `5`) and `14` (lands after `10`) → `1→2→4→5→7→8→9→10→14`.
- Step 4 (merge with E: `3→6→15`): `3` lands between `2` and `4`; `6` lands between `5` and `7`; `15` lands after `14` → final: `1→2→3→4→5→6→7→8→9→10→14→15`, matching Example 1's stated output exactly.
- `flattenLL` is the **driver**: `temp1 = head`, `temp2 = temp1->next`; if `temp2` is null there's only one sub-list, so return `head` as-is (already flattened). Otherwise, loop: save `next = temp2->next`, `temp1 = mergeTwoLists(temp1, temp2)`, `temp2 = next` — repeat until sub-lists run out.
- Complexity is the one genuinely new idea today: merging costs `O(2M)`, then `O(3M)`, then `O(4M)`, … up to `O(NM)` — the sum `2+3+...+N` is roughly `N²`, giving **O(N²M) time**. Space stays **O(1)** — only pointers are rearranged, no new nodes.
- Key Takeaways (deliver as-is): `flattenLL` merges sub-lists one at a time via `down`; `mergeTwoLists` does the actual comparing; complexity is O(N²M) time, O(1) space.

**Checkpoint (at 36 min)** — cold-call:
> *"Suppose `flattenLL` is called on a list where the head node has no other horizontal sub-lists at all — just one chain hanging off it. What does `temp2` equal, and what does the function return?"*
> **Answer:** `temp2 = temp1->next` is `null` (there's nothing beside the first sub-list), so the `if (!temp2) return head` line fires immediately and the original head is returned untouched — it was already flattened.

---

## ⚡ Activity 2 — Spot the Bug: The Missing Base Case (36–41 min)

**Format:** Spot the Bug (null-pointer edge case) · **Exposes:** null-pointer blindness — assuming the merge loop always has something to merge with.

**Setup line (say this):**
> *"Here's `flattenLL`. Someone hands you a linked list with exactly one node, and that node has no `down` chain and no other sub-list beside it — just one bare node. Trace the function line by line with me: what does `temp2` become, and does the code crash, loop, or return cleanly?"*

```
flattenLL(head) {
  if (!head) return NULL
  Node temp1 = head
  Node temp2 = temp1->next
  if (!temp2) { return head }
  ...
}
```

**What students do:** Trace it aloud — `temp1 = head`; `temp2 = temp1->next` → `null`, since there's no second sub-list; the `if (!temp2)` check catches this and returns `head` directly, never entering the merge loop.

**How to handle wrong answers:** If someone predicts a crash or infinite loop, point to the exact line `if (!temp2) { return head }` and ask them to say out loud, in English, what condition it's guarding against.

**Debrief line:**
> *"That one `if` line is the difference between working code and a null-pointer crash on the very first list you test it on. Always ask: what does this function do when there's only one of something, not two?"*

**Cut rule:** If short on time, state the trace answer directly instead of eliciting it, but keep the closing line about checking the "only one of something" case — it's the transferable lesson.

---

## ⚡ Activity 3 — Predict the Output: Where Does 3 Land? (41–44 min)

**Format:** Predict-the-Output · **Exposes:** losing track of *where* a new value gets woven into an already-long merged list, and why that cost keeps growing.

**Setup line (say this):**
> *"Growing list right now: `1, 2, 4, 5, 7, 8, 9, 10, 14`. Next sub-list to merge in is `3, 6, 15`. Before I show the comparisons — where does `3` land? Between which two numbers?"*

**What students do:** Call out a position; correct answer is between `2` and `4`.

**How to handle wrong answers:** If someone places `3` before `1` or after `4`, walk the comparisons live: `1 & 3` → `1` smaller, `2 & 3` → `2` smaller, `4 & 3` → `3` smaller now, insert here.

**Debrief line:**
> *"Every single value from every new sub-list has to be compared against the *entire* growing list to find its slot. The list only gets longer as we go — that's exactly why the cost is `2M + 3M + ... + NM`, not just `N × M`."*

**Cut rule:** If short on time, skip the vote and state the answer directly, but keep the debrief line — it's the bridge straight into the O(N²M) complexity just covered in Slide Block B.

---

## Exit Ticket (44–45 min)

> In one sentence: why is `flattenLL`'s time complexity O(N²M) instead of just O(N×M), even though each individual merge step is a simple two-pointer walk?
> **Answer:** Because the merged list keeps growing with every step — the first merge costs `2M`, the next costs `3M`, then `4M`, and so on up to `NM`; that growing series (`2+3+...+N`) sums to roughly `N²`, not a flat `N`.

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| This is a brand-new algorithm, unrelated to yesterday's merge | The `down` pointer and multi-list framing look unfamiliar | Activity 1 — showing the merge of A & B is byte-for-byte yesterday's `mergeTwoLists`, renamed pointer |
| `flattenLL` will always have two sub-lists to merge | No worked example shows the single-sub-list case | Activity 2 — tracing the `if (!temp2) return head` base case explicitly |
| Merging in sub-list D or E means re-comparing against A and B individually | Students picture five separate merges, not one growing chain | Slide Block A's checkpoint — stressing the merged result is never taken apart again |
| Time complexity should be O(N × M), matching "N sub-lists of length M" | Feels like a simple loop over N sub-lists | Activity 3 plus Slide Block B's complexity beat — the growing list makes each merge step more expensive than the last |
| A new node is created for every value during merging | "Building a new list" sounds like allocation | Slide Block B — stating explicitly that only pointers (`down` links) are rearranged; space stays O(1) |

---

## Instructor Notes

- **The load-bearing idea of this whole session is one sentence: "this is yesterday's `mergeTwoLists`, called repeatedly, with `down` instead of `next`."** Say it in the Hook, say it again after Activity 1, say it again in Slide Block B. Everything else — the dry run, the complexity — falls out of that one reuse.
- **The deck repeats the dry run and pseudocode explanation a second time later** (an alternate walkthrough of Example 1 and a line-by-line pseudocode breakdown) — treat this as reinforcement material to point students to after class, not additional lecture content; the 45-minute budget above assumes you deliver each beat once. <!-- placement: inferred -->
- **Example 2 in the deck (list with values 6, 8, 9, 11, 20, 25, 30, 60) has no worked dry run** — only the problem/output slides. Don't promise students a walkthrough of it; use it only if you want a quick independent-practice example after Activity 3.
- **The Classroom Quiz slot has no pre-built questions for this course** — pull 5–6 from the platform bank live.
- **Pacing risk:** the O(N²M) derivation in Slide Block B is easy to rush past since it's "just arithmetic," but it's the only genuinely new analytical idea today — don't compress it below the time given above.
