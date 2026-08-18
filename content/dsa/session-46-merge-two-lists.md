# Session 46 — Merge Two Sorted Linked Lists

**Duration** 45 min · **Topic** Linked List — Merge Two Sorted Lists · **Prerequisite** Session 45 — Add Two Numbers
**Session type** Concept lecture

<!-- placement: inferred — duration set to the 45 min default per session sizing guidance; single-problem session reusing the dummy-node + two-pointer pattern from Session 45 -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Merge Two Sorted Linked Lists | https://docs.google.com/presentation/d/1tfsL74t346U40DnXNCzCSXcJfBxY3cJyO37vhpCdeQU/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the merge-two-sorted-lists problem, including that either input list may be `null`. *(REMEMBERING)*
2. Explain why a dummy node avoids special-casing which list's head becomes the merged list's head, and why the merge needs no extra data structure. *(UNDERSTANDING)*
3. Dry-run the two-pointer merge for a given pair of sorted lists, including the tie-breaking rule when both current values are equal. *(APPLYING)*
4. Contrast why this merge runs in `O(N + M)` time while Session 45's addition ran in `O(max(M, N))`, despite both using a dummy node and two pointers. *(ANALYZING)*

<!-- placement: inferred — phrased from the deck's Key Takeaways slides (38–39) -->

---

## Warm-Up Poll — Retrieval Practice (0–6 min)

Retrieval on Session 45 (Add Two Numbers). Say: *"Six on yesterday's addition problem before we reuse half of that machinery for something new today."*

**Q1.** Why are digits added starting from the *head* of each list rather than the tail?
`A` Because linked lists can only be traversed backward · `B` Because the lists store digits in reverse order — least significant digit first · `C` Because sorting is required first · `D` It's arbitrary

**Q2.** What does the "dummy head" node do in the Add Two Numbers algorithm?
`A` Stores the final answer's last digit · `B` Gives `cur` somewhere to point before any real result node exists, so the first real node doesn't need special-case code · `C` Detects cycles · `D` Nothing — it's discarded before use

**Q3.** In one iteration, what is computed first?
`A` `sum = carry + digit from a + digit from b` · `B` `carry = sum / 10`, before `sum` is even known · `C` A new node, before `sum` is computed · `D` None of these

**Q4.** What value gets stored in the new node created each iteration?
`A` `sum` · `B` `sum % 10` · `C` `sum / 10` · `D` `carry`

**Q5.** What is `carry` updated to at the end of each iteration?
`A` `sum % 10` · `B` `sum / 10` · `C` Always `0` · `D` Always `1`

**Q6.** What is the time and space complexity of Add Two Numbers?
`A` O(min(M, N)) · `B` O(M + N) · `C` O(max(M, N)) · `D` O(M × N)
→ *Read:* If many pick O(M + N), point out this is the same distinction as "loop iterations" vs. "total nodes touched" — worth a 10-second recap, because today's problem lands on `O(N + M)` for a different reason, and the contrast is the whole point of this session's final checkpoint.

**Q7.** True or False: if list `a` finishes before list `b`, the loop stops immediately and any remaining digits of `b` are lost.
`A` True · `B` False
→ *Read:* Correct is False (the `||` condition keeps going, and the missing side just contributes 0). If this is shaky, expect it to resurface today in a different shape — merge has its own "what happens when one list ends first" moment.

**Q8. (MSQ)** If a carry of `1` remains after both lists are fully processed, which of these are true? *(pick all that apply)*
`A` An extra node with value `1` is appended · `B` The result ends up one digit longer than the longer input · `C` It's simply discarded · `D` It becomes the new head

**Running it** — poll tool, ~40 s per question, project the distribution after each. Total 6 min including reads.

---

## Hook (6–8 min)

Say: *"Yesterday you did arithmetic on a linked list. Today, no arithmetic at all. You're a librarian with two shelves, each already sorted, and you need to combine them into one sorted shelf — without pulling every book off first."*

Put Example 1 on the board:

```
List 1: 1 → 3 → 7 → 9 → NULL
List 2: 2 → 4 → 5 → 8 → 11 → 15 → NULL
Merged: 1 → 2 → 3 → 4 → 5 → 7 → 8 → 9 → 11 → 15 → NULL
```

Ask: *"Both lists are already sorted. You are not allowed to put every value into one big list and re-sort it. What's the smallest amount of looking-around you'd need to do to build the merged list, one value at a time?"* Take 1–2 guesses (expect "just look at the front of each pile and take the smaller one"). Say: *"That's the entire algorithm. Today you write it."*

---

## Slide Block A (8–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Problem Statement, Examples 1 & 2, Approach -->

Covers: Problem Statement (note: either input list may be `null`) → Example 1 → Example 2 (negative values, duplicate/tied values) → Approach.

**Beats to emphasise**

- **Either input may be `null` — say this explicitly.** The problem statement calls this out as a note, and it matters: if one list is empty from the start, the "merge" is really just returning the other list untouched.
- **The dummy node returns from yesterday, doing the same job.** `dummy` is a throwaway node (value `0`); `cur` starts pointing at it. Every comparison winner gets linked after `cur`; at the end, `dummy->next` is returned, dropping the dummy — identical pattern to Session 45's `ans`/`cur`.
- **Example 2 has ties.** List 1 = `3, 7, 8, 11, 17`; List 2 = `-1, 2, 3, 8, 14`. When both current values are equal (`3` vs `3`, then later `8` vs `8`), the comparison `temp1->data <= temp2->data` is true, so **List 1's node wins the tie** and is linked first. Point this out now — it's the seed of Activity 3.

**Checkpoint (at 17 min)** — cold-call one student:
> *"In Example 2, when both pointers are sitting on a node valued 8, which list's node gets added to the merged list first, and why does the code guarantee that?"*
> **Answer:** List 1's node, because the comparison is `<=` (less-than-or-equal), not strict `<`. On a tie, the condition is still true, so List 1 always wins ties.

---

## ⚡ Activity 1 — Dry-Run Relay: Merge by Comparison (17–21 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** forgetting to advance *only* the pointer whose node was just used, and losing track of which pointer (`temp1`/`temp2`) versus which pointer (`cur`) is being moved.

**Setup line (say this):**
> *"Three volunteers: temp1 on list one, temp2 on list two, cur building the merged list starting from a dummy. I'll call out each comparison from Example 1 — 1,3,7,9 versus 2,4,5,8,11,15 — you three narrate what moves."*

**What students do:** Walk the first four comparisons live: `1` vs `2` → `1` smaller, link it, move `temp1` to `3`; `3` vs `2` → `2` smaller, link it, move `temp2` to `4`; `3` vs `4` → `3` smaller, move `temp1` to `7`; `7` vs `4` → `4` smaller, move `temp2` to `5`. At each step, `cur` moves forward by exactly one — to whichever node was just linked.

**How wrong answers surface:** The most common slip is moving *both* `temp1` and `temp2` on every comparison, instead of only the one whose value was just used. Catch it by asking after each round: *"Which pointer just lost the comparison? Did it move?"* — the loser stays put; only the winner's pointer advances.

**Debrief line:**
> *"Only one pointer moves per comparison — the one whose value you just consumed. Move the wrong one, or move both, and you'll either skip a value or compare the same value twice."*

**Cut rule:** If running short, walk only the first two comparisons live and narrate the rest verbally, landing on the final merged order.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (26–34 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Pseudocode, Complexity Analysis, Code Implementation, Key Takeaways -->

Covers: Pseudocode → Complexity Analysis → C++ Code → Key Takeaways.

**Beats to emphasise**

- **After the main loop, one list is fully drained — attach the rest of the other directly.** `if (temp1) cur->next = temp1; else cur->next = temp2;` — whichever list still has nodes left gets linked in one shot. There is no need to walk it node-by-node; it's already sorted.
- **The main loop condition is `temp1 && temp2`** — AND, not OR, and that's correct here (unlike yesterday's OR). The comparison loop can only run while *both* lists still have something to compare; the moment either runs dry, the loop must stop and hand off to the "attach the rest" step.
- Complexity is **`O(N + M)`**, not `O(max(N, M))` — every node from both lists is visited and linked exactly once, whether during the comparison loop or the final hand-off. Contrast this explicitly with Session 45, where the loop only ran `max(M, N)` times because both lists were consumed *together*, in lockstep, one digit-position at a time.

**Checkpoint (at 34 min)** — show hands:
> *"Yesterday's Add Two Numbers was O(max(M, N)). Today's merge is O(N + M). Both use two pointers and a dummy node — so why the different complexity?"*
> **Answer:** In addition, both lists advance together, one position per iteration, so the loop only runs as many times as the *longer* list. In merge, only one pointer advances per comparison, so it can take up to `N + M` total comparisons to fully drain both lists — every node gets touched once, but not necessarily in lockstep.

---

## ⚡ Activity 2 — Spot the Bug: The Missing Hand-Off (34–39 min)

**Format:** Spot the Bug · **Exposes:** assuming the comparison loop merges *everything*, and forgetting that whichever list survives longer needs to be explicitly attached afterward.

**Setup line (say this):**
> *"Here's the real merge function with its last three lines deleted — it just returns right after the while loop ends. Run it mentally on Example 1: list one has 4 nodes, list two has 6. What does the output look like, and what's missing?"*

```
// Buggy version — missing the post-loop hand-off
while (temp1 != null && temp2 != null) {
    ... // same comparison logic
}
return dummy->next
```

**What students do:** Trace Example 1 by hand: the loop naturally ends once `temp1` becomes `null` (list one, the shorter one, drains first). At that point, list two still has `11 → 15` left. With the hand-off deleted, the function returns immediately — the merged list ends at `9`, and `11, 15` are never attached.

**How wrong answers surface:** Some students assume the while loop "must" have already included everything sorted, since the earlier part of the merge looked complete. Point back at the loop condition — `&&` stops as soon as *either* side is empty, and nothing in the loop body handles what's left over. That's a separate, deliberate step.

**Debrief line:**
> *"The comparison loop only handles the part where both lists still have contenders. The moment one list wins the race and finishes, whatever's left in the other list is still sorted and just needs to be tacked on — the deck's own dry run shows exactly this moment, when temp1 goes null and 11, 15 get attached from list two."*

**Cut rule:** If running short, skip the hand-trace and just ask directly: *"What's missing, and which two values get lost in Example 1?"*

---

## ⚡ Activity 3 — Predict the Output: Ties (39–43 min)

**Format:** Predict-the-Output · **Exposes:** the assumption that equal values require special handling, rather than falling naturally out of `<=`.

**Setup line (say this):**
> *"Example 2 again: list one is 3,7,8,11,17. List two is -1,2,3,8,14. At some point both pointers land on a node valued 3, and later both land on a node valued 8. Predict: does the code need an extra check to handle a tie, or does it just work? And if it 'just works,' which list's 3 ends up first in the output?"*

**What students do:** Reason through the `<=` comparison: when values are equal, `temp1->data <= temp2->data` evaluates to true, so list 1's node is linked and list 1's pointer advances — no special tie-handling code exists or is needed.

**How wrong answers surface:** Some predict an extra `if (temp1->data == temp2->data)` branch must exist somewhere in the pseudocode. Point them back at the actual pseudocode — there are only two branches, `<=` and `else`; equality is silently absorbed into the `<=` branch.

**Debrief line:**
> *"You don't need a third branch for 'equal.' `<=` already decides ties for you — list one always wins them. That's one line of comparison doing the work of what looks like it should need three cases."*

**Cut rule:** If running short, skip the prediction step and just point at the `<=` operator directly, stating that it silently handles ties.

---

## Exit Ticket (43–45 min)

> List 1 is `5 → NULL`. List 2 is `null` (completely empty). Walk through the algorithm and state what the merged output is, and which part of the code produces it.
> **Answer:** The comparison loop (`while (temp1 && temp2)`) never runs at all, since `temp2` is `null` from the start. Execution falls straight to the hand-off: `if (temp1) cur->next = temp1;` — so the entire list `5 → NULL` gets attached as-is. Output: `5 → NULL`. This is exactly why the problem statement calls out that either input list may be `null`.

**Homework:** re-attempt the dry run of Example 2 (the one with ties and a negative value) from memory. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Both `temp1` and `temp2` advance on every comparison | It feels symmetric — both pointers are "in the loop" | Activity 1's relay — only the winning pointer moves each round |
| The comparison loop merges everything by itself | The loop looks like "the whole algorithm" since it's the biggest block of code | Activity 2's Spot the Bug — showing 11, 15 silently dropped without the post-loop hand-off |
| Equal values need a dedicated tie-handling branch | Ties feel like a special case that "must" need its own code | Activity 3 — showing `<=` already resolves ties with no extra branch |
| Merge and Add-Two-Numbers should have the same complexity since both use two pointers + a dummy node | Surface-level pattern match on the code shape | Slide Block B's checkpoint — lockstep advancement (addition) vs. one-pointer-at-a-time advancement (merge) |

---

## Instructor Notes

- **Have Example 1's four pointers pre-labelled on the board** (`temp1`, `temp2`, `dummy`, `cur`) before class — Activity 1 depends on it.
- **Classroom Quiz placement is inferred**, not sourced from the deck — pull 5–6 MCQs from the platform pool at the marked point.
- **The deck re-walks Example 2's dry run a second time near the end** (slides 64–70) and re-explains the pseudocode line-by-line a second time (slides 52–59) — this is repeated source material, not additional content. Use it only if a section of the room is still stuck after Slide Block B; otherwise it's redundant with what's already been delivered.
- **Pacing risk:** Activity 3 (ties) is easy to rush since it looks like a minor footnote, but it directly sets up the O(N+M) vs O(max(M,N)) contrast in the final checkpoint — don't compress it below the allotted time.
