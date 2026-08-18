# Session 56 — Next Greater Element

**Duration** 45 min <!-- placement: inferred — brute force plus optimal, standard two-approach session length for this block --> · **Topic** Stack & Queue — Next Greater Element · **Prerequisite** Session 55 — Balanced Parenthesis
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Next Greater Element | https://docs.google.com/presentation/d/1fYEqvg63eWARWKlNAGWz-tp1N6zvDi7VPJMo8DmVoAQ/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the Next Greater Element (NGE) problem: for each element, find the closest element to its *right* that's larger, or `-1` if none exists. *(REMEMBERING)*
2. Explain the brute-force approach (nested scan) and why it costs O(n²). *(UNDERSTANDING)*
3. Trace the optimal right-to-left monotonic-stack approach on a given array, including the pop loop at each step. *(APPLYING)*
4. Connect this problem back to Session 53's increasing/decreasing pairing rule — explain *why* NGE uses a decreasing-style scan direction and comparison. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 55 (Balanced Parenthesis) (0–5 min)

Say: *"Six on bracket matching, then we go back to monotonic stacks with an actual payoff this time."*

**Q1.** A bracket string is valid only if:
`A` Counts of each bracket type are equal · `B` Every opening has a matching closing bracket of the same type, closed in the correct order · `C` It contains no more than 3 brackets · `D` All brackets are the same type

**Q2.** What's pushed onto the stack while scanning a bracket string?
`A` Closing brackets · `B` Opening brackets · `C` Both, always · `D` Nothing — a counter is enough

**Q3.** A closing bracket arrives and the stack is empty. The string is:
`A` Automatically valid · `B` Invalid · `C` Valid if it's the last character · `D` Undetermined

**Q4.** After scanning the entire string, if the stack is *not* empty, the string is:
`A` Valid · `B` Invalid · `C` Valid only if it has fewer than 3 leftover elements · `D` Impossible to determine
→ *Read:* B. If this misses, re-state it in one line before moving on — today's problem is a different failure mode (missing information, not mismatched information), and mixing the two up will hurt this session's dry run.

**Q5.** Balanced Parenthesis runs in:
`A` O(1) time · `B` O(N) time · `C` O(N²) time · `D` O(log N) time

**Q6 (MSQ — pick all correct).** Which strings are valid?
`A` `()[]` · `B` `([)]` · `C` `{[()]}` · `D` `(()`

**Running it** — poll tool, ~25 s per question. Total 5 min including reads.

---

## Hook (5–8 min)

Write this array on the board: `[73, 74, 75, 71, 69, 72, 76, 73]` (a week of daily temperatures).

Ask: *"For each day, I want to know: how many days until a warmer day? Day 1 is 73° — the very next day, 74°, is warmer, so the answer for day 1 is 1. What about day 6, 72°?"*

Let students reason it out (day 7 is 76° — warmer — so 1 day). Then:

> *"You're doing 'next greater element' right now, just phrased as temperatures instead of numbers. It's one of the most common interview problems that exists, precisely because the naive way to solve it — check every day against every future day — is slow, and the clever way uses exactly the monotonic stack you built two sessions ago. Today, that stack finally answers something."*

---

## Slide Block A (8–16 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–10: Problem Statement, Example, Brute Force Approach intro -->
Covers: Problem Statement (for each element, find the closest larger element to its right; `-1` if none — the last element is always `-1`) → worked example `arr = [7, 3, 1, 5, 8, 4]` → output `[8, 5, 5, 8, -1, -1]` → Brute Force Approach: for each element, scan right until a larger one is found.

**Beats to emphasise**

- Read the output aligned under the input so the pattern is visible: `7→8`, `3→5`, `1→5`, `5→8`, `8→-1`, `4→-1`.
- **Say explicitly why the last element is always `-1`:** there's nothing to its right at all, so the search space is empty by definition — not a special case, just the natural consequence of the rule.
- Brute force, stated as one sentence: "for every position, walk right one step at a time until something bigger shows up, or you run out of array."

**Checkpoint (at 16 min)** — cold-call:
> *"For `arr = [7, 3, 1, 5, 8, 4]`, why is the answer for `8` equal to `-1`, but the answer for `4` is also `-1` — same answer, different reason?"*
> **Answer:** `8` is `-1` because nothing to its right (`4`) is bigger than it. `4` is `-1` because it's the *last* element — nothing is to its right at all.

---

## Slide Block B1 (16–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 9–30: Brute Force dry run, Pseudocode, Complexity, Code -->
Covers: Full dry-run walk of the brute force approach on `[7, 3, 1, 5, 8, 4]`, checking each element against everything to its right → pseudocode (nested loop) → complexity: O(n²) time, O(n) space.

**Beats to emphasise**

- Narrate the dry run exactly as the deck does: for `7`, scan right `[3, 1, 5, 8, 4]` — first value bigger than `7` is `8`. For `3`, scan right `[1, 5, 8, 4]` — first bigger is `5`. Continue through `1 → 5`, `5 → 8`, `8 → (nothing bigger, -1)`, `4 → (last element, -1)`.
- State the cost plainly: worst case, every element scans almost the entire rest of the array — that's the outer loop times the inner loop, O(n²).
- This is the same shape of inefficiency as Session 53's opening hook (`[5, 2, 8, 6, 3]`, scanning ahead by eye) — call that back explicitly.

**Checkpoint (at 24 min)** — cold-call:
> *"What's the absolute worst input for brute force here — the array that makes it do the most work?"*
> **Answer:** A strictly decreasing array (e.g. `[9, 8, 7, 6, 5]`) — every element has to scan almost the entire remaining array before concluding there's no greater element, hitting close to n² comparisons total.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Predict the Output: "Brute Force by Hand" (29–35 min)

**Format:** Predict-the-Output · **Exposes:** whether students can execute the brute-force scan themselves — necessary groundwork before the optimal approach replaces it with something less intuitive-looking.

**Setup line (say this):**
> *"New array: `[4, 8, 2, 9, 3]`. For each position, tell me the next greater element before I confirm — scan right, same as the dry run."*

**What students do:** Call out each answer in turn, one position at a time.

**Answers**

```
4 → scan [8, 2, 9, 3] → 8 is first bigger → 8
8 → scan [2, 9, 3]    → 9 is first bigger → 9
2 → scan [9, 3]       → 9 is first bigger → 9
9 → scan [3]          → nothing bigger → -1
3 → last element      → -1
```

**How it surfaces:** At `2`, ask before revealing: *"Is the answer `9` or `3`?"* — some students will grab the first number they see regardless of size (`3` is not bigger than `2`... wait it is, but it comes after 9). Push them to confirm they're finding the *first bigger* one, not just any nearby number, and that they stop scanning the moment they find it (they don't need to check anything past `9`).

**Debrief line:**
> *"Every one of those scans stops the instant it finds something bigger — you never need to look further right than that. The only question left is: can we avoid re-scanning from scratch for every single position? That's next."*

**Cut rule:** If running short, do just `4` and `2` — one straightforward case, one where "first bigger, not first different" needs reinforcing.

---

## Slide Block B2 (35–39 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 31–37: Optimal Approach (monotonic stack), traversal direction and rule -->
Covers: Optimal Approach — traverse the array **right to left**, maintain a stack of "candidates that might be the next greater element" for positions still to come. At each position, pop everything smaller-or-equal, then the new top (if any) is the answer; push the current element.

**Beats to emphasise**

- **Connect directly to Session 53:** this is a monotonically *decreasing* stack (bottom → top decreasing), scanning right to left — exactly the pairing rule from two sessions ago (decreasing stack ↔ next/previous greater).
- Say the rule as one sentence: *"Walking backwards, the stack only ever holds numbers that could still be someone's answer. Anything smaller than what's currently arriving can never be anyone's next-greater from this point on, so it gets thrown away."*
- This flips the brute-force intuition (scan forward from each element) into a single backward pass that reuses work instead of repeating it.

**Checkpoint (at 39 min)** — cold-call:
> *"Why do we scan right to left here, instead of left to right like the brute force did?"*
> **Answer:** Scanning right to left lets the stack accumulate "candidates to the right" as we go, so by the time we reach any position, the stack already holds exactly the information needed to answer it in O(1) amortised — no re-scanning.

---

## ⚡ Activity 2 — Live Trace: "Run the Optimal Stack" (39–43 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can execute the right-to-left monotonic-stack algorithm themselves, tying the mechanical trace back to the pairing rule just restated.

**Setup line (say this):**
> *"Same array as Activity 1: `[4, 8, 2, 9, 3]`. Right to left this time. I'll call the position, you tell me what gets popped, what the answer is, and what gets pushed — before I confirm."*

Run **right to left, one element at a time**:

```
3  → stack empty → answer -1 → push 3.                Stack: [3]
9  → top 3 ≤ 9 → pop 3. Stack empty → answer -1 → push 9.   Stack: [9]
2  → top 9 > 2 → no pop → answer 9 → push 2.          Stack: [9, 2]
8  → top 2 ≤ 8 → pop 2. top 9 > 8 → stop → answer 9 → push 8.  Stack: [9, 8]
4  → top 8 > 4 → no pop → answer 8 → push 4.          Stack: [9, 8, 4]
```

Final answers, read left to right: `4→8, 8→9, 2→9, 9→-1, 3→-1` — matching Activity 1 exactly.

**How it surfaces:** At `8`, ask before revealing: *"How many pops happen, and why does popping stop at `9`?"* Correct: one pop (`2`), then stop, because `9 > 8` means `9` is a valid answer for `8` and also still a valid future candidate — it doesn't get thrown away.

**Debrief line:**
> *"Same final answers as the brute force, same problem — but every element was pushed once and popped at most once across the whole array. That's O(n), not O(n²), and it's the exact same discipline you practiced two sessions ago."*

**Cut rule:** If running short, do only the `9` and `8` steps — `9` shows a pop-to-empty, `8` shows a pop-then-stop, which together cover the whole mechanism.

---

## Exit Ticket (43–45 min)

> For `arr = [5, 4, 3, 2, 1]` (strictly decreasing), what is the Next Greater Element for every position, and why?
> **Answer:** `[-1, -1, -1, -1, -1]` for all — since the array is strictly decreasing, nothing to the right of any element is ever larger.

**Homework:** trace the optimal right-to-left approach by hand on `[2, 7, 3, 5, 4]`. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| The brute force and optimal approaches can give different answers | Feels like "different algorithm" should mean "different logic entirely" | Activity 1 and Activity 2 use the *same* array and land on identical output — only the work done differs |
| The optimal approach should scan left to right, matching how humans read | Reading direction bias | Slide Block B2's explicit right-to-left framing, reinforced by the checkpoint question naming *why* |
| Popping stops after exactly one pop | Carried over from underestimating monotonic-stack loops generally | Activity 2's `8` step — one pop, but only because the next top happens to be bigger; a different array could pop several |
| A strictly decreasing array is an edge case that needs special handling | Every answer being `-1` looks like something must have gone wrong | Exit ticket — confirming this is just the ordinary algorithm producing an ordinary (if uniform) result |
| The stack in the optimal approach stores answers | It's easy to conflate "the stack" with "the answer array" since both update together | State explicitly: the stack stores *candidates*, a completely separate `ans` array stores the actual answers |

---

## Instructor Notes

- **This session is where Session 53's investment pays off — say so explicitly.** If students struggled with the increasing/decreasing pairing two sessions ago, this is the natural moment to re-anchor it, since NGE is the pairing's first real payoff.
- **Keep both activities on the exact same array (`[4, 8, 2, 9, 3]`).** Running brute force and optimal on identical input lets students see directly that the answers match — that's more convincing than two different examples ever would be.
- **Pacing:** if behind by minute 35, compress Slide Block B2's explanation to the one-sentence rule and lean on Activity 2's live trace to teach the mechanism instead of the slides.
- **Next session (Asteroid Collision) reuses this same right-to-left-adjacent stack discipline in a physically different framing** — flag that connection at the very end if time allows.
