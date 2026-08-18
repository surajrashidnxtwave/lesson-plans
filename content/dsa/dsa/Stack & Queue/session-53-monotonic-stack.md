# Session 53 — Monotonic Stack

**Duration** 45 min <!-- placement: inferred — single concept, shortest deck (22 slides) in the block --> · **Topic** Stack & Queue — Monotonic Stack · **Prerequisite** Session 52 — Infix, Prefix, and Postfix
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Monotonic Stack | https://docs.google.com/presentation/d/13WDE1ZfHsX0jqvbNcpwrp9SJi8etqsdYNhRL4-6-YwE/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define a monotonic stack: a stack that maintains its elements in strictly increasing or strictly decreasing order, rather than plain LIFO. *(REMEMBERING)*
2. Distinguish a monotonically **increasing** stack (bottom → top increasing, useful for next/previous *smaller*) from a monotonically **decreasing** stack (bottom → top decreasing, useful for next/previous *greater*). *(UNDERSTANDING)*
3. Trace the conditional push/pop rule that builds an increasing or decreasing stack for a given input array. *(APPLYING)*
4. Explain why each element is pushed and popped at most once across a full run, and connect that to the O(n) efficiency this pattern buys over a nested-loop approach. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 52 (Infix, Prefix, Postfix) (0–5 min)

Say: *"Eight quick ones on notation and conversion before we meet a new kind of stack."*

**Q1.** In infix notation, the operator sits:
`A` Before both operands · `B` Between the operands · `C` After both operands · `D` Anywhere

**Q2.** In postfix notation, the operator sits:
`A` Before both operands · `B` Between the operands · `C` After both operands · `D` Anywhere

**Q3.** Prefix notation is also known as:
`A` Reverse Polish Notation · `B` Polish Notation · `C` Infix Notation · `D` Canonical Notation

**Q4.** Postfix notation is also known as:
`A` Reverse Polish Notation (RPN) · `B` Polish Notation · `C` Infix Notation · `D` Canonical Notation

**Q5.** Correct precedence order, highest to lowest:
`A` `+/-` > `*,/,%` > `^` > `()` · `B` `()` > `^` > `*,/,%` > `+,-` · `C` `^` > `()` > `+,-` > `*,/,%` · `D` All operators have equal precedence

**Q6.** The associativity of `^` (exponentiation) is:
`A` Left-to-right · `B` Right-to-left · `C` It has no associativity · `D` Same as `+`/`-`
→ *Read:* If this isn't near-unanimous B, that's last session's one genuine exception rule slipping — it matters again today whenever two operators tie on precedence.

**Q7 (MSQ — pick all correct).** Which are genuine steps of the infix-to-postfix algorithm?
`A` An operand goes straight to the output · `B` `(` is pushed onto the stack · `C` `)` pops to output until `(` is found, then the `(` is discarded · `D` Every operator is pushed immediately, regardless of what's already on the stack
→ *Read:* Correct: A, B, C. D is the trap — operators only get pushed *after* popping anything of higher-or-equal precedence first.

**Q8.** After the entire infix string has been scanned, what happens to whatever operators remain on the stack?
`A` They're discarded · `B` They're popped to the output, in order · `C` They stay on the stack forever · `D` They cause an error

**Running it** — poll tool, ~25 s per question. Total 5 min including reads.

---

## Hook (5–8 min)

Write the array on the board: `[5, 2, 8, 6, 3]`. Ask:

> *"For each number, I want the closest number to its *right* that's smaller than it. Just eyeball it — shout out answers."*

Let students call out (5→2, 2→none, 8→6, 6→3, 3→none). Then:

> *"You just did that by scanning ahead every time — for a 5-element array that's manageable. For a 100,000-element array, scanning ahead from every position is brutally slow. Today's tool solves this with a stack that only ever moves forward, and touches each element at most twice, total, for the *entire* array. That's the monotonic stack, and it's the engine behind almost every session left in this block."*

---

## Slide Block A (8–16 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–6: Introduction, Types of Monotonic Stack -->
Covers: What is a Monotonic Stack (maintains elements in increasing or decreasing order by value, not just insertion order) → conditional push/pop → the two types: Monotonically Increasing (bottom→top increasing; finds next/previous **smaller**) and Monotonically Decreasing (bottom→top decreasing; finds next/previous **greater**).

**Beats to emphasise**

- **A monotonic stack is still a stack** — you only ever push and pop at the top, same as every stack so far. What's different is the *rule* for when you pop: not "whenever the caller asks," but "whenever the incoming element would break the order you're maintaining."
- **Say the pairing explicitly, twice:** increasing stack ↔ next/previous *smaller*. Decreasing stack ↔ next/previous *greater*. This pairing is the single fact students most often invert under pressure in later sessions (Next Greater Element in Session 56 uses a *decreasing*-style scan; Largest Rectangle in Session 58 uses both).
- This is a **building block**, not a problem with its own final answer — flag that explicitly: "today we practice maintaining the invariant; the next few sessions are where this invariant solves an actual problem."

**Checkpoint (at 16 min)** — cold-call:
> *"If I need the next *greater* element for every position in an array, which type of monotonic stack do I reach for — increasing or decreasing?"*
> **Answer:** Decreasing.

---

## Slide Block B1 (16–24 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 7–12: Example — Monotonically Increasing Stack, dry run on arr = [5, 2, 8, 6, 3] -->
Covers: The full push/pop dry run building a monotonically **increasing** stack on `arr = [5, 2, 8, 6, 3]`.

**Beats to emphasise**

- Narrate the rule before every step: *"While the top of the stack is greater than the incoming element, pop. Then push the incoming element."*
- Walk it exactly as the deck does: push `5` → stack `[5]`. Incoming `2`: `5 > 2` is true, pop `5`, push `2` → stack `[2]`. Incoming `8`: `2 > 8` is false, no pop, push `8` → stack `[2, 8]`. Incoming `6`: `8 > 6` is true, pop `8`, push `6` → stack `[2, 6]`. Incoming `3`: `6 > 3` is true, pop `6`, push `3` → stack `[2, 3]`.
- Say out loud after each pop: *"That element just got popped because something smaller showed up after it — which is exactly the information 'next smaller element' needs."* <!-- placement: inferred — the deck demonstrates the invariant-maintenance mechanics without explicitly stating the next-smaller-element output per index; this framing bridges to that upcoming use, made explicit here as instructor commentary -->

**Checkpoint (at 24 min)** — cold-call:
> *"Walking through that dry run, why did `8` get popped when `6` arrived, but `2` never got popped at all?"*
> **Answer:** `8 > 6` triggers a pop (order would break), but `2` is never greater than anything that comes after it in this array, so it's never violated and stays at the bottom for the whole run.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Live Trace: "You Build the Decreasing Stack" (29–35 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can flip the comparison direction themselves (increasing's rule uses `top > incoming`; decreasing's rule uses `top < incoming`) rather than just having watched one direction demonstrated — using the exact same array the deck itself uses for the decreasing example, so their prediction can be checked slide-for-slide against Slide Block B2.

**Setup line (say this):**
> *"Same array, `[5, 2, 8, 6, 3]`. This time we're building a monotonically *decreasing* stack — bottom to top, decreasing. The rule flips: while the top of the stack is *less than* the incoming element, pop. I'll call out each number, you tell me the stack *before* I reveal it on the next slide."*

Run **one number at a time**, taking a predicted stack state before confirming:

```
5   → stack empty → push 5.                         Stack: [5]
2   → top (5) < 2? No (5 is not less than 2) → push 2.   Stack: [5, 2]
8   → top (2) < 8? Yes → pop 2.
      top (5) < 8? Yes → pop 5.
      → push 8.                                      Stack: [8]
6   → top (8) < 6? No → push 6.                       Stack: [8, 6]
3   → top (6) < 3? No → push 3.                       Stack: [8, 6, 3]
```

**How it surfaces:** At the `8` step, ask before revealing: *"How many pops happen here, and why more than one?"* Correct answer: two pops (`2`, then `5`) — both violate the decreasing order relative to `8`, and the rule says keep popping *while* the condition holds, not just once.

**Common wrong answer:** students pop only `2` (the immediate top) and stop, treating monotonic-stack popping like a single conditional check rather than a `while` loop. Correct by re-reading the rule: "while the top is less than incoming" — it's a loop, not an `if`.

**Debrief line:**
> *"Same mechanism, mirrored comparison. Increasing pops on `top > incoming`; decreasing pops on `top < incoming`. Everything else — push after popping, one element in and out at most once — is identical."*

**Cut rule:** If running short, do only the `8` step (the one with two pops) and the debrief — that's the step carrying the whole lesson (looped popping, not single-shot).

---

## Slide Block B2 (35–39 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 13–20: Example — Monotonically Decreasing Stack dry run, comparison of both final stacks, Key Takeaways -->
Covers: Reveal of the decreasing-stack dry run (confirming Activity 1's predictions) → side-by-side comparison of both final stacks on the same input → Key Takeaways.

**Beats to emphasise**

- Confirm the final decreasing stack matches what students just predicted: `[8, 6, 3]` bottom to top.
- Put both final stacks side by side (increasing: `[2, 3]`; decreasing: `[8, 6, 3]`) and note they're built from the *same* five numbers with only the comparison direction flipped.
- **Key Takeaways, read as a two-line summary:** increasing stack → next/previous smaller problems. Decreasing stack → next/previous greater problems.

*(No separate checkpoint here — Activity 1 already served as the check for this block.)*

---

## ⚡ Activity 2 — Predict & Discuss: "Why Not Just Check Every Pair?" (39–43 min)

**Format:** Predict-the-Output / Discussion · **Exposes:** whether students understand *why* the monotonic stack pattern is efficient — not just that it produces a correct order — which is the entire motivation for using it in every session that follows.

**Setup line (say this):**
> *"For `[5, 2, 8, 6, 3]`, if I found the next smaller element for every position by checking every element to its right with a nested loop, how many comparisons, worst case? Now count: across the whole increasing-stack dry run we just did, how many times, total, was any single element pushed or popped?"*

**What students do:** Estimate both numbers. Nested loop: roughly n²/2 comparisons in the worst case (5 elements → up to ~10). Stack version: every element is pushed exactly once, and popped at most once — so at most `2n` stack operations total (5 elements → at most 10, but typically fewer).

**How it surfaces:** If students can't see why "pushed once, popped at most once" bounds the *total* work, point back at the dry run: `8` was pushed once and popped once; `2` and `5` were each pushed once and popped once; `6` and `3` were pushed once and never popped (they're still on the stack at the end). No element is ever touched a third time.

**Debrief line:**
> *"That 'each element in, each element out, at most once' argument is the proof that this pattern is O(n) instead of O(n²) — you don't need to trust it, you can count it, and you just did. You'll see this exact argument formalised in the next few sessions' complexity analysis."*

**Cut rule:** If running short, skip the nested-loop comparison count and go straight to counting the stack operations, then deliver the debrief line.

---

## Exit Ticket (43–45 min)

> For `arr = [4, 1, 5, 2]`, would building a monotonically **increasing** stack pop the `4` at any point? If so, when?
> **Answer:** Yes — when `1` arrives, `4 > 1` triggers a pop of `4` before `1` is pushed. (Full trace: push `4` → `[4]`; `1` arrives, pop `4`, push `1` → `[1]`; `5` arrives, `1 > 5` false, push `5` → `[1, 5]`; `2` arrives, `5 > 2` true, pop `5`, push `2` → `[1, 2]`.)

Scan responses on the way out. If several students say `4` is never popped, that's the "pop happens on the very next smaller arrival, not just the global minimum" rule not landing — reopen Session 54 with a quick recap.

**Homework:** trace both a monotonically increasing and a monotonically decreasing stack build on `[6, 3, 9, 2, 8]` by hand. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Popping happens once per incoming element, at most | Most everyday "if this then that" reasoning is single-shot, not looped | Activity 1's `8` step — showing two pops triggered by one incoming element, driven by a `while`, not an `if` |
| Increasing stack ↔ next greater; decreasing stack ↔ next smaller (the pairing flipped) | The names "increasing"/"decreasing" describe the stack's own order, which feels intuitively backwards from what it's used to *find* | Slide Block A's explicit, twice-repeated pairing statement, plus the minute-16 checkpoint testing it directly |
| A monotonic stack finds an actual answer (like "the next smaller element for index 3") by itself | The dry run's visual focus is on stack contents, not a per-index answer array | Explicitly flagging, per this session's inferred framing, that today builds the *mechanism* — the payoff (an actual answer array) arrives in Sessions 56 and 58 |
| Building a monotonic stack costs more than a plain nested-loop scan, since there's "extra bookkeeping" | Maintaining an order feels like it should add overhead compared to "just checking everything" | Activity 2 — counting total push/pop operations directly against nested-loop comparisons on the same small example |
| Elements popped off a monotonic stack are simply discarded and irrelevant | The dry run doesn't show where popped elements "go" | Note explicitly (as the visual is silent on this): in real problems, the moment an element is popped is exactly when you learn its answer (e.g., "next smaller/greater found") — covered concretely from Session 56 onward |

---

## Instructor Notes

- **This is the shortest deck in the block (22 slides) and the lightest session** — no pseudocode, no code implementation, no complexity-analysis slide. Resist the urge to pad it with content from later sessions (Next Greater Element, Largest Rectangle) — those sessions build directly on this one and will feel repetitive if the payoff is spoiled here.
- **The one thing worth over-teaching today is the increasing↔smaller / decreasing↔greater pairing.** Every session for the next two weeks assumes students can retrieve this instantly. If time is short anywhere else, protect the two moments that drill it: the minute-16 checkpoint and Slide Block B2's side-by-side comparison.
- **Activity 1 is load-bearing** — it's the only place students actively apply the rule themselves rather than watching it applied. Do not cut it if you're behind; cut Activity 2 first per its own cut rule.
- **Set up the next few sessions explicitly.** Closing line, if time allows: "Min Stack, Next Greater Element, Asteroid Collision, Largest Rectangle in Histogram — all four of the next sessions are this exact pattern wearing a different problem's clothes. If today's rule is solid, the hard part of all four is already done."
