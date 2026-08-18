# Session 44 — Length of Cycle

**Duration** 45 min · **Topic** Linked List — Length of Cycle · **Prerequisite** Session 43 — Cycle Detection
**Session type** Concept lecture

<!-- placement: inferred — duration set to the 45 min default per session sizing guidance; this is a single-problem session (two approaches, both direct extensions of Session 43's machinery) so no extension is warranted -->

**Platform units**

| Resource | Link |
|---|---|
| Video + deck — Length of Cycle | https://docs.google.com/presentation/d/1WZCBJX5XX5Cheq0vAV4cG5_HrRiU-Kq_KeXk26opBuI/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the "length of cycle" problem and explain how it differs from the yes/no cycle-detection problem of Session 43. *(REMEMBERING)*
2. Explain how the brute-force map approach recovers cycle length from `currentIndex − storedIndex`. *(UNDERSTANDING)*
3. Dry-run the Floyd's-based optimal approach, including the second phase where the slow pointer counts its way back to the meeting point. *(APPLYING)*
4. Compare the time/space complexity of the brute-force map approach (`O(N log N)` / `O(N)`) against the optimal two-pointer approach (`O(N + cycle length)` / `O(1)`), and explain why the brute-force cost changed from Session 43's `O(N)`. *(ANALYZING)*

<!-- placement: inferred — phrased from the deck's two Key Takeaways slides (46–51 of session-43 for baseline, 50–51 of this deck) -->

---

## Warm-Up Poll — Retrieval Practice (0–6 min)

Retrieval on Session 43 (Cycle Detection). Say: *"Seven quick ones on yesterday's cycle detection before we build on top of it today."*

**Q1.** In the brute-force cycle detection approach, what do we use to track visited nodes?
`A` Array · `B` A set (hash-based) · `C` Stack · `D` Queue
→ *Read:* If B isn't near-unanimous, re-show the brute-force dry run's "Set" column for 30 seconds before moving on — today's session swaps this set for a map, and that swap is the whole point of Slide Block A.

**Q2.** What is the time complexity of Session 43's brute-force approach?
`A` O(1) · `B` O(log n) · `C` O(n) · `D` O(n²)

**Q3.** What is its space complexity?
`A` O(1) · `B` O(n) · `C` O(log n) · `D` O(n²)

**Q4.** In Floyd's (tortoise-and-hare) approach, how many steps do the slow and fast pointers take per move, respectively?
`A` 1 and 2 · `B` 2 and 1 · `C` 1 and 3 · `D` Equal steps

**Q5.** If a linked list has no cycle, what eventually happens to the fast pointer?
`A` It becomes null · `B` It equals slow forever · `C` It loops forever · `D` It throws an error

**Q6.** What is the space complexity of the optimal (Floyd's) cycle detection approach?
`A` O(n) · `B` O(1) · `C` O(log n) · `D` O(n²)
→ *Read:* This is the number that matters most today — Slide Block B's optimal approach for cycle *length* keeps this exact O(1), while the brute force does not. If this isn't solid, slow down Slide Block B's complexity beat.

**Q7.** In yesterday's brute-force dry run, the cycle was detected when the traversal revisited which node?
`A` The node valued 2 · `B` The node valued 4 · `C` The node valued 9 · `D` The node valued 3
→ *Read:* Correct answer is B (node 4). If most miss this, project yesterday's dry-run slide for 20 seconds — today's dry run reuses the same list shape.

**Running it** — poll tool, ~40 s per question, project the distribution after each. Total 6 min including reads.

---

## Hook (6–8 min)

Put yesterday's cycle example back on screen: the list `5 → 7 → 4 → 9 → 2 → 3 → 1 → 9` looping back, cycle answer = `True`.

Say: *"Yesterday you answered one question about this list: does it loop? Today I'm asking a harder one: if it loops, exactly how many nodes are in the loop?"*

Ask: *"You already have working code from yesterday that finds the loop. Shout out — how would you change it to also count the loop's length?"* Take 2–3 guesses (expect answers like "count from where it repeats" or "keep going until you're back where you started" — both are the seeds of today's two approaches). Don't confirm or correct — say: *"Hold that thought, we're about to build exactly that."*

---

## Slide Block A (8–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Problem Statement, Brute-Force Approach, Dry Run, Pseudocode, Complexity Analysis, Code Implementation -->

Covers: Problem Statement → Brute-Force Approach → Dry Run (map of node→index) → Pseudocode → Complexity Analysis → C++ Code.

**Beats to emphasise**

- **Reframe yesterday's set as today's map.** Yesterday you only needed a yes/no answer ("have I seen this node?") so a *set* was enough. Today you need *when* you saw it, so the set becomes a **map from node to its index** in the traversal.
- **The entire insight is one subtraction.** When a node is found already in the map, `cycleLength = currentIndex − storedIndex`. Walk the dry run's final slide (`8 − 2 = 6`) slowly — this is the one line students must be able to reproduce from memory.
- **Complexity changed, and that's deliberate.** The deck states time complexity as `O(N log N)`, not `O(N)` — because this approach uses an (ordered) `map`, where insert/find cost `O(log N)` each, unlike yesterday's `unordered_set` at `O(1)` average. Say this contrast out loud; it is the most commonly missed distinction in the whole session.

**Checkpoint (at 17 min)** — cold-call two students:
> *"Why is cycle length `currentIndex − storedIndex`, and not just `currentIndex`?"*
> **Answer:** `currentIndex` counts every node visited from the head, including the straight-line part before the loop starts. `storedIndex` marks exactly where the loop begins. Subtracting the two removes that straight-line prefix and leaves only the loop itself.

---

## ⚡ Activity 1 — Dry-Run Relay: "Be the Map" (17–21 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** confusing "index" (a position count) with "node identity", and forgetting that the map is checked *before* it is updated, not after.

**Setup line (say this):**
> *"Eight of you, up front, in a line. You are nodes 5, 7, 4, 9, 2, 3, 6, 8 — in that order. Node 8 secretly points back to whichever of you is holding 4. I'll tap each of you in turn. When tapped, say three things out loud: your value, your index, and whether you're already 'in the map'."*

**What students do:** Line up in order (matches the deck's list: `5, 7, 4, 9, 2, 3, 6, 8`, index 0–7, with node `8` pointing back to the student holding `4`, index 2). Tap student 1 through student 8 in order. Each says: *"I'm [value], index [i], not in the map → add me."* When you reach a 9th tap — routed back to the student holding `4` — that student says: *"I'm 4, and I'm already in the map at index 2!"*

**How wrong answers surface:** Two common slips — (1) a student announces the wrong index because they start counting from 1, not 0; (2) when you tap back to node `4`, the class jumps straight to "cycle length is 8" instead of subtracting. Let the class sit with the wrong answer for a beat, then ask: *"8 nodes have been visited total — is the loop 8 nodes long?"* and point back at the physical line — there are clearly only 6 people between the two "4" taps.

**Debrief line:**
> *"The map never told you the length directly — it only ever told you 'have I been here, and when.' The subtraction is where the real answer lives. That's true of almost every map-based dry run you'll do this course."*

**Cut rule:** If running short, skip standing everyone up — do it as call-and-response from seats using the slide's own numbers, and jump straight to the final subtraction (`8 − 2 = 6`).

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (26–34 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Optimal Approach, Dry Run (meet, then count), Pseudocode, Complexity Analysis, Code Implementation, Key Takeaways -->

Covers: Optimal Approach (Floyd's, two phases) → Dry Run → Pseudocode → Complexity Analysis → C++ Code → Key Takeaways.

**Beats to emphasise**

- **Phase 1 is exactly yesterday's algorithm.** Slow moves 1 step, fast moves 2; if they meet, a cycle exists. Don't re-teach this from scratch — name it as "the part you already know."
- **Phase 2 is new: counting back to the meeting point.** Once `slow == fast`, move `slow` one more step, set `count = 1`, then keep moving `slow` alone until it meets `fast` again — that count is the cycle length. This is a genuinely different technique from the brute force: no map, just pointers.
- **No extra data structure at all.** Zero sets, zero maps — hence `O(1)` space, a sharp contrast with the brute force's `O(N)`. Time is `O(N + cycle length)`: one pass to find the meeting point, plus at most one more lap around the cycle to count it.

**Checkpoint (at 34 min)** — show hands:
> *"In the pseudocode, right after `slow == fast` is detected, we set `count = 1` — not `count = 0`. Who thinks `count` should start at `0` instead?"*
> **Answer:** `count = 1`, because the very next line already moves `slow` forward once *before* the counting loop starts. That first move already used up one step, so `count` must reflect it — starting at `0` would undercount by exactly one.

---

## ⚡ Activity 2 — Spot the Bug: The Missing First Step (34–39 min)

**Format:** Spot the Bug · **Exposes:** the off-by-one error of skipping the "move slow once, set count = 1" step before the counting loop begins.

**Setup line (say this):**
> *"Here's the real pseudocode next to a version I wrote that looks almost identical. One of them is broken. Find it, and tell me exactly what number the broken one returns for our example — not just 'it's wrong'."*

Put both on screen:

```
// Correct (from the deck)
if (slow == fast) {
    slow = slow->next
    count = 1
    while (slow != fast) {
        slow = slow->next
        count++
    }
    return count
}
```

```
// Buggy version
if (slow == fast) {
    count = 0
    while (slow != fast) {
        slow = slow->next
        count++
    }
    return count
}
```

**What students do:** 60 seconds silent, then hands up. Someone identifies the missing `slow = slow->next` before the loop; push for the actual returned value.

**How wrong answers surface:** Most students correctly spot "something's missing" but guess the buggy version returns a slightly-wrong number like 5. Walk it through: at the moment `slow == fast` is true, the `while (slow != fast)` condition is immediately false — the loop body never runs, and the function returns `0` on the spot, for every single cycle, regardless of its real length.

**Debrief line:**
> *"Any loop that says 'count until you're back where you started' needs one deliberate step taken before counting begins — otherwise you're already 'back where you started' and the loop never runs. Zero steps counted, zero returned. Same shape of bug you'll meet in for-loops all course."*

**Cut rule:** If running short, skip the silent-think time and just ask the question aloud, taking the first raised hand.

---

## ⚡ Activity 3 — Predict the Output: Map vs. Set (39–43 min)

**Format:** Predict-the-Output · **Exposes:** the assumption that any hash-based lookup structure is automatically `O(1)`.

**Setup line (say this):**
> *"Yesterday's brute force used `unordered_set` and ran in O(N). Today's brute force does something that sounds identical — track every node you've seen — but the deck says it's O(N log N), not O(N). Before I tell you why: predict, in one sentence, what's different in the code."*

**What students do:** 30 seconds pair discussion, then two or three pairs share their guess out loud.

**How wrong answers surface:** The common wrong guess is "today's approach checks more things" or "the map is bigger." Redirect: the *pattern* (store what you've seen, check before adding) is identical — the only change is the container. Today's code uses `map` (an ordered, tree-based container — each insert/find costs `O(log N)`), where yesterday's used `unordered_set` (hash-based, `O(1)` average).

**Debrief line:**
> *"Same insight, different container, different cost. From here on, when you read someone's code — including your own — check the container name, not just what it's used for. `map` and `unordered_map` are not interchangeable in complexity, even though they read almost the same."*

**Cut rule:** If running short, skip the pair discussion and just state the contrast directly, then move to the exit ticket.

---

## Exit Ticket (43–45 min)

> In your own words: what does `currentIndex − storedIndex` actually measure? And separately — in the optimal approach, why does `count` start at `1` rather than `0`?
> **Answers:** The subtraction measures the number of nodes between the first time you saw a repeated node and the second time — i.e., the length of the loop. `count` starts at `1` because the first step away from the meeting point has already been taken before the counting loop begins.

Scan responses on the way out — if the subtraction answer is weak across the room, open Session 45 with a 60-second recap before moving on.

**Homework:** re-attempt both dry runs (brute-force map, and Floyd's meet-then-count) from memory. <!-- placement: inferred -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Cycle length = `currentIndex` alone | The index feels like "the answer" since it's the last number computed | Activity 1's physical relay — pointing out only 6 people separate the two "node 4" taps, not 8 |
| Map-based tracking is the same speed as set-based tracking | Both "sound like" hash lookups | Activity 3's map-vs-set contrast, and naming `O(log N)` vs `O(1)` explicitly |
| The counting loop can start at `count = 0` | It's the default they reach for when initialising any counter | Activity 2's Spot the Bug — showing the buggy version returns `0` for every cycle, not just this one |
| Cycle detection (Session 43) and cycle length (this session) are "basically the same algorithm" | Both use slow/fast pointers and both involve a meeting point | Slide Block B's explicit two-phase framing: Phase 1 is identical to Session 43, Phase 2 is new |

---

## Instructor Notes

- **Draw the 8-node list with its loop-back before class starts.** Both the brute-force and optimal dry runs reuse the same shape (`5,7,4,9,2,3,6,8` with `8 → 4`) — having it on the board already saves real time in Activity 1.
- **Do not re-derive Floyd's from scratch in Slide Block B.** Session 43 already built the "why do slow and fast eventually meet" intuition — spend that time on Phase 2 (the counting step) instead, since it's genuinely new material.
- **Classroom Quiz placement is inferred**, not sourced from the deck — this DSA course's question bank isn't in the source material. Pull 5–6 MCQs from the platform pool at the marked point.
- **Pacing risk:** Slide Block A's map-vs-set complexity contrast is easy to rush past because it feels like a footnote — it isn't. It's tested directly in Activity 3 and the warm-up poll of Session 45.
