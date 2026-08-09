# Session 45 — Add Two Numbers

**Duration** 45 min · **Topic** Linked List — Add Two Numbers · **Prerequisite** Session 44 — Length of Cycle
**Session type** Concept lecture

<!-- placement: inferred — duration set to the 45 min default per session sizing guidance; single-problem session with one dominant technique (digit-by-digit traversal with carry) -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Add Two Numbers | https://docs.google.com/presentation/d/1UFNX3HAh8y-V1tSh-HQdEB3rCHgKYFUIwOjv0ugKvzg/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the "Add Two Numbers" problem: two non-negative numbers stored as linked lists with digits in reverse order, summed into a resulting list. *(REMEMBERING)*
2. Explain why storing digits least-significant-first lets the algorithm add head-to-head instead of needing to reverse anything, and why a dummy head simplifies building the result. *(UNDERSTANDING)*
3. Dry-run the digit-by-digit addition with carry propagation for a given pair of lists, including the case where a final carry creates an extra node. *(APPLYING)*
4. Justify why the algorithm's time and space complexity is `O(max(M, N))`. *(ANALYZING)*

<!-- placement: inferred — phrased from the deck's repeated Key Takeaways slides (32–34) -->

---

## Warm-Up Poll — Retrieval Practice (0–6 min)

Retrieval on Session 44 (Length of Cycle). Say: *"Six on yesterday's cycle-length problem before we drop cycles entirely and do something new today."*

**Q1.** In the brute-force "length of cycle" approach, what does the map store alongside each node?
`A` Its value doubled · `B` Its index/position in the traversal · `C` Its `next` pointer's address twice · `D` Nothing — just the node

**Q2.** When a node is found already in the map, how is cycle length computed?
`A` currentIndex + storedIndex · `B` currentIndex − storedIndex · `C` storedIndex − currentIndex · `D` storedIndex ÷ currentIndex

**Q3.** What is the time complexity of that brute-force approach?
`A` O(N) · `B` O(N log N) · `C` O(log N) · `D` O(1)
→ *Read:* If many pick O(N), they're carrying over Session 43's cycle-*detection* complexity. Flag the map-vs-set distinction again in one sentence before moving on — it won't resurface again this course, so this is the last chance to lock it in.

**Q4.** What is its space complexity?
`A` O(1) · `B` O(log N) · `C` O(N) · `D` O(N²)

**Q5.** In the optimal (Floyd's) approach for cycle length, what happens immediately after slow and fast first meet?
`A` Return 0 immediately · `B` Move slow one more step and start counting from 1 · `C` Reset both pointers to head · `D` Swap slow and fast

**Q6.** What is the time complexity of the optimal cycle-length approach?
`A` O(N) exactly · `B` O(N + length of the cycle) · `C` O(log N) · `D` O(N²)

**Q7. (MSQ)** Which of these are true of the optimal approach's space usage? *(pick all that apply)*
`A` O(1) · `B` Uses only a few pointers · `C` Uses a map · `D` Grows with the cycle's length

**Q8.** True or False: if there's no cycle, the "length of cycle" function returns `-1`.
`A` True · `B` False
→ *Read:* Correct answer is False — it returns `0`. If this trips people up, it's worth a 10-second correction now; today's problem also has a "what if nothing's there" edge case (an empty input list), and sloppy return values are the recurring failure mode across both.

**Running it** — poll tool, ~40 s per question, project the distribution after each. Total 6 min including reads.

---

## Hook (6–8 min)

Say: *"Last two sessions were entirely about loops in linked lists — does one exist, how long is it. Today, forget cycles completely. New skill: doing arithmetic ON a linked list."*

Put this on the board:

```
List a:  0 → 2 → 4 → NULL
List b:  1 → 3 → 5 → NULL
```

Ask: *"If I told you each of these lists is a number written backwards, what two numbers am I holding, and what's their sum?"* Let the room work it out: `420 + 531 = 951`. Then reveal the actual output list: `1 → 5 → 9 → NULL` — also backwards, also `951`.

Say: *"You just did the algorithm in your head. Today we make a computer do it one digit at a time, without ever assembling the whole number."*

---

## Slide Block A (8–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Problem Statement, Examples 1 & 2, Approach -->

Covers: Problem Statement → Example 1 (no carry) → Example 2 (with carry) → Approach.

**Beats to emphasise**

- **Reverse order is a feature, not an inconvenience.** Addition works right-to-left in real life (ones, then tens, then hundreds). Storing the least-significant digit first means the algorithm can add head-to-head, in order, without ever needing to know how long either number is in advance.
- **The dummy head removes a special case.** `ans` starts as a throwaway node valued `-1`; `cur` points to it. Every real digit gets appended after `cur`, and at the very end the function returns `ans->next`, silently dropping the dummy. This avoids writing separate code for "what if this is the very first node."
- **Example 2 is why carry matters.** `689 + 83 = 772`... check it against the deck: list `a = 6,8,9` (reverse order) is the number `986`; list `b = 8,3` is `38`; `986 + 38 = 1024`, and the output list `4,2,0,1` (reverse order) reads as `1024`. The output has **one more digit** than either input — that extra digit only exists because of a leftover carry.

**Checkpoint (at 17 min)** — cold-call one student:
> *"In Example 2, the result has 4 digits even though both inputs have at most 3. Where does that extra digit come from?"*
> **Answer:** After both input lists are fully consumed, there's still a carry left over (from the final addition step). That leftover carry becomes one more node, appended at the very end of the result.

---

## ⚡ Activity 1 — Dry-Run Relay: Carry the Digit (17–21 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** conflating *when* carry is computed with *when* it's used, and the instinct to "reset" carry to 0 each round instead of overwriting it.

**Setup line (say this):**
> *"Four roles, four volunteers: temp1, temp2, carry-keeper, and cur. I'll call out each iteration of Example 1 — 0,2,4 plus 1,3,5 — and each of you announces your role's new value, in order: sum, then new node, then carry, then move pointers."*

**What students do:** Walk iteration 1 live: `temp1 = 0`, `temp2 = 1`, `carry = 0` → sum = `0 + 1 + 0 = 1` → new node value = `1` (`sum % 10`) → carry-keeper announces `carry = 0` (`sum / 10`) → temp1/temp2 move to `2` and `3`. Repeat for iteration 2 (`2 + 3 = 5`, carry stays `0`) and iteration 3 (`4 + 5 = 9`, carry stays `0`). End: both temps are null, carry is `0`, so nothing extra is appended; result reads `1 → 5 → 9`.

**How wrong answers surface:** Two common slips — (1) the carry-keeper says "reset carry to 0" at the start of each round, as if it needs manual resetting; correct them: it isn't reset, it's *recomputed fresh* from `sum / 10` every iteration, and this example just happens to keep landing on 0. (2) Students compute the new node's value using the *carry* instead of `sum % 10` — make them say the formula out loud each time.

**Debrief line:**
> *"Carry is never 'reset.' It's overwritten every single iteration by `sum / 10`, whatever that turns out to be. If you find yourself writing `carry = 0` inside the loop, you've broken the algorithm."*

**Cut rule:** If running short, run only iterations 1 and 3 live, and narrate iteration 2 verbally without the relay.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (26–34 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Pseudocode, Complexity Analysis, Code Implementation, Key Takeaways -->

Covers: Pseudocode (including the final `if (carry == 1)` step) → Complexity Analysis → C++ Code → Key Takeaways.

**Beats to emphasise**

- **The loop condition is `temp1 != null || temp2 != null`** — OR, not AND. As long as *either* list still has digits, the loop keeps going; the exhausted side simply contributes nothing (`if (temp1 != null) sum += temp1->data`, guarded individually for each side).
- **The final `if (carry == 1) { cur->next = new Node(1) }` line is the "carry the 1" from long addition**, applied exactly once, after both lists are done. It's a one-line edge case, not a separate algorithm.
- Complexity is **`O(max(M, N))`**, not `O(M + N)` — the loop runs once per *position*, up to the length of the longer list, because the shorter list just stops contributing after it ends. It doesn't run once per node summed across both lists.

**Checkpoint (at 34 min)** — show hands:
> *"Why is the time complexity `O(max(M, N))`, and not `O(M + N))`?"*
> **Answer:** The loop advances one position at a time, and it keeps running as long as *either* list has a node left — so the number of iterations equals the length of the longer list, not the combined length of both.

---

## ⚡ Activity 2 — Spot the Bug: The Wrong Loop Condition (34–39 min)

**Format:** Spot the Bug · **Exposes:** the assumption that `&&` and `||` are interchangeable in a "keep going while there's more to do" loop.

**Setup line (say this):**
> *"Real code on the left, my version on the right. I changed exactly one character. Tell me what breaks, and use Example 1 to prove it — list a has 3 digits, list b has 3 digits, so first try it there, then tell me what happens if list b only had 2."*

```
// Correct (from the deck)
while (temp1 != null || temp2 != null) { ... }
```

```
// Buggy version
while (temp1 != null && temp2 != null) { ... }
```

**What students do:** 45 seconds to test it mentally against Example 1 (same length — bug doesn't show), then against a shortened list `b` of only 2 digits. Someone identifies that the buggy `&&` version stops the instant *either* list runs out — even if the other list still has digits left.

**How wrong answers surface:** Students often say "no difference" because they only test the equal-length example, where both conditions behave identically. Push them: *"Now make list b one digit shorter. Does anything change?"* — that's when the AND-version silently drops the remaining digits and any leftover carry.

**Debrief line:**
> *"`&&` means both must still have something. `||` means either is enough. Any 'process until everything's done' loop over two things of possibly different lengths needs `||` — get this backwards and you silently truncate your answer with no error message at all."*

**Cut rule:** If running short, skip the mental test and just state the two scenarios (equal-length vs. unequal-length) directly.

---

## ⚡ Activity 3 — Predict the Output: The Leftover Carry (39–43 min)

**Format:** Predict-the-Output · **Exposes:** the belief that carry only matters *inside* the loop and can be ignored once both lists end.

**Setup line (say this):**
> *"One more list pair, said out loud, no board: list a is just the single digit 5. List b is also just the single digit 5. Predict the output list — all of it — before I show you."*

**What students do:** Work it out: `5 + 5 = 10` in one iteration → new node value `0` (`sum % 10`), carry becomes `1` (`sum / 10`). Both lists are now exhausted, but carry is `1`, not `0`.

**How wrong answers surface:** Most predict the output stops at a single node `0 → NULL`, forgetting the deck's explicit note (Slide 19): *if carry != 0 after the loop ends, append one more node with that carry value.* Walk them to the correct output: `0 → 1 → NULL` — representing `10`, correctly reconstructed in reverse digit order.

**Debrief line:**
> *"Carry doesn't get flushed away just because the loop ended. Check it one more time, after the loop, or you'll silently drop the most significant digit of your answer."*

**Cut rule:** If running short, skip the "predict before reveal" pause and just walk the example directly, landing on the same debrief line.

---

## Exit Ticket (43–45 min)

> Two linked lists, `a = 9 → NULL` and `b = 9 → NULL` (representing the single digits 9 and 9). Write out the resulting linked list, digit by digit, and say in one sentence why it has two nodes instead of one.
> **Answer:** `9 + 9 = 18` → new node value `8` (`sum % 10`), carry `1` (`sum / 10`). Loop ends (both lists exhausted), but carry is `1`, so one more node is appended with value `1`. Result: `8 → 1 → NULL` (representing `18`). It has two nodes because the leftover carry after the loop becomes its own node.

**Homework:** re-attempt the dry run of Example 2 (the one with the carry) from memory. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Carry needs to be manually reset to `0` each iteration | It "feels like" a running total that should clear | Activity 1's relay — carry-keeper states it's overwritten by `sum / 10`, never reset |
| `&&` and `||` are interchangeable in a "keep going" loop over two lists | Both "look like" the natural way to say "while there's more" | Activity 2's Spot the Bug — showing the AND-version silently drops digits from the longer list |
| Carry left over after the loop ends can be safely ignored | Attention naturally drops once the main loop is "done" | Activity 3 — walking `9 + 9` to its two-node result |
| Time complexity is `O(M + N)` because "both lists get processed" | Sounds intuitive if you're thinking about total nodes touched, not loop iterations | Slide Block B's checkpoint — reasoning from iteration count, not node count |

---

## Instructor Notes

- **Have Example 1's dry run pre-drawn** (four roles: `temp1`, `temp2`, `carry`, `cur`/`ans`) before class — Activity 1 depends on it and setting it up live costs minutes you don't have.
- **Classroom Quiz placement is inferred**, not sourced from the deck — pull 5–6 MCQs from the platform pool at the marked point.
- **The deck repeats its own Key Takeaways and pseudocode slides several times** (e.g., slides 32–34, 44–45) — this is a source artefact, not intentional pacing. Deliver each concept once at the pace of Slide Block B above; don't re-teach every repeated slide.
- **Pacing risk:** Activity 2's Spot the Bug only "clicks" once students test it against an *unequal-length* pair — if the room jumps straight to "no difference," don't move on until someone tries the shortened list b.
