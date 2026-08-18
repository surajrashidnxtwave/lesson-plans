# Session 55 — Balanced Parenthesis

**Duration** 40 min <!-- placement: inferred — single classic algorithm, one approach, no complexity trade-offs to weigh — the lightest session since Monotonic Stack --> · **Topic** Stack & Queue — Balanced Parenthesis · **Prerequisite** Session 54 — Min Stack
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Balanced Parenthesis | https://docs.google.com/presentation/d/1NzWVMwN6CqCKR--ORa0Gy6uVLGrY2vXjM304aOBOeDg/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the three conditions for a bracket sequence to be valid: matching type, correct nesting order, and every closing bracket having a corresponding opening one. *(REMEMBERING)*
2. Explain why a stack — not a counter — is the right tool: order and type both matter, not just counts. *(UNDERSTANDING)*
3. Trace the push-on-open, match-and-pop-on-close algorithm on a given string, including both valid and invalid cases. *(APPLYING)*
4. Identify the two distinct failure modes — closing bracket with an empty stack, and closing bracket that doesn't match the stack's top — and explain why the string is invalid in each. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 54 (Min Stack) (0–5 min)

Say: *"Six quick ones on Min Stack before we look at a much older, much more famous stack problem."*

**Q1.** Min Stack's four required operations, all O(1), are:
`A` push, pop, top, getMin · `B` push, pop, sort, getMin · `C` insert, delete, search, getMin · `D` push, remove, peek, getMax

**Q2.** In the pair-stack approach, each stack entry stores:
`A` Just the value · `B` The value and the minimum-so-far at that point · `C` The value and its index · `D` The value twice

**Q3.** In the encoded-value approach, an encoded sentinel is pushed only when:
`A` Every single push · `B` The incoming value is a new minimum · `C` The stack is empty · `D` The incoming value equals the current minimum

**Q4.** On `pop()`, how do you know the value you just removed was an encoded sentinel, not a real element?
`A` It's negative · `B` It's less than the current `mini` · `C` It's greater than the current `mini` · `D` You can't tell
→ *Read:* B. If this misses, the decode/encode formula pair needs one more pass before today's algorithm — bracket matching assumes stack push/pop is already second nature.

**Q5.** The pair-stack approach's space complexity is:
`A` O(1) · `B` O(n) · `C` O(2n) · `D` O(n²)

**Q6 (MSQ — pick all correct).** Which are true of both Min Stack approaches?
`A` `getMin()` is O(1) · `B` `push()` is O(1) · `C` They both use exactly one stack · `D` They both track a running minimum somehow

**Running it** — poll tool, ~25 s per question. Total 5 min including reads.

---

## Hook (5–8 min)

Write this on the board: `(){[{}])`

Ask: *"Your code editor underlines a bracket in red the instant you type something like this. How does it know, instantly, without running your program?"*

Let a few guesses land. Then:

> *"Every compiler, every linter, every IDE runs some version of what you're building in the next 30 minutes. It's one of the oldest problems in computer science, and it's the cleanest possible demonstration of why a stack — specifically, last-in-first-out — is exactly the right shape for 'things that must close in the reverse order they opened.'"*

---

## Slide Block A (8–15 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 4–10: Problem Statement, Examples 1 & 2, Approach -->
Covers: Problem Statement (`(`, `)`, `{`, `}`, `[`, `]` — valid if every opening has a matching closing bracket of the same type, closed in the correct order) → Example 1 (`{}()[]` → valid) → Example 2 (`(){[{}])` → invalid, the `{` and `)` don't pair) → Approach: push openings, match closings against the top.

**Beats to emphasise**

- Read Example 2's failure out loud character by character until the mismatch: `(`, `)` — closes fine. `{`, `[`, `{` — all pushed. `}` — matches the most recent `{`, pops fine. `]` — the top is now the *outer* `{`, not `[`. Mismatch.
- **Say explicitly: "count of brackets is not enough."** `(){[{}])` has three of each type — perfectly balanced counts — and is still invalid. Order and type both matter; a stack is what enforces both at once.
- Preview the two ways this can fail, without solving them yet: (1) a closing bracket shows up when the stack is empty, (2) a closing bracket's type doesn't match what's on top. Both get built into the dry run next.

**Checkpoint (at 15 min)** — cold-call:
> *"Why can't I just count opening and closing brackets and compare the totals?"*
> **Answer:** Equal counts don't guarantee correct order or matching types — `(){[{}])` proves it: 3 and 3, still invalid.

---

## Slide Block B (15–20 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 11–39: Dry Run, Pseudocode, Complexity Analysis, Code, Key Takeaways -->
Covers: Full dry run of `s = "(){[{}])"` character by character, ending in the mismatch at `]` → pseudocode → complexity (O(N) time, O(N) space) → C++ code.

**Beats to emphasise**

- Narrate every step exactly as the deck does: `(` push. `)` matches top `(`, pop — stack empty. `{` push. `[` push. `{` push (stack: `{`, `[`, `{`). `}` matches top `{`, pop (stack: `{`, `[`). `]` matches top `[`, pop (stack: `{`). `)` — top is `{`, does **not** match `)` — invalid, stop.
- State the two failure modes explicitly, matching the deck's own note: if you hit a closing bracket and the stack is **empty**, invalid. If you hit a closing bracket and it **doesn't match the top**, invalid. After the whole string, if the stack is **not empty** (unclosed openings remain), also invalid.
- Complexity is refreshingly simple here: one pass, one stack, O(N) time and O(N) space (worst case: every character is an opening bracket).

**Checkpoint (at 20 min)** — cold-call:
> *"Suppose the string is just `"(("` — two opening brackets, nothing else. Walk me through what happens, and is it valid?"*
> **Answer:** Push `(`, push `(` — stack has two elements, string ends. The stack is *not* empty, so it's invalid — every opening needs a matching close.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## ⚡ Activity 1 — Spot the Bug: "Valid or Not, and Why" (25–31 min)

**Format:** Spot the Bug · **Exposes:** whether students can identify *which* of the two failure modes applies, not just guess valid/invalid from a glance.

**Setup line (say this):**
> *"Four strings on the board. For each one: valid or invalid? If invalid, tell me exactly which character breaks it and why — empty-stack-on-close, or mismatched-type-on-close."*

Put all four on screen at once:

```
1.  "([])"
2.  "([)]"
3.  "((("
4.  ")("
```

**What students do:** 45 seconds silent, then hands up. Take one student per string.

**Answers**

| # | Valid? | Reason |
|---|---|---|
| 1 | Valid | Properly nested: `(`, `[`, `]` matches `[`, `)` matches `(` |
| 2 | Invalid | At `)`: stack top is `[`, doesn't match — mismatched-type-on-close |
| 3 | Invalid | End of string, stack still has 3 unmatched `(` — non-empty stack at the end |
| 4 | Invalid | At `)`: stack is empty (nothing pushed yet) — empty-stack-on-close |

**How it surfaces:** For string 2, push students to say specifically what's on top of the stack the instant `)` appears (`[`, not `(`) — this is the mismatched-type failure, distinct from string 4's empty-stack failure. Many students will say "invalid" correctly but conflate the two reasons.

**Debrief line:**
> *"Three ways this breaks: empty stack when you need to pop, wrong type on top when you pop, or leftover stack when you're done. Every invalid string in this problem is exactly one of those three — nothing else."*

**Cut rule:** If running short, do strings 2 and 4 only — they're the two genuinely distinct failure modes; 1 and 3 are confirmations, not new information.

---

## ⚡ Activity 2 — Live Trace: "You Run the Stack" (31–36 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can execute the full algorithm themselves end to end, on a string they haven't seen.

**Setup line (say this):**
> *"New string: `{[()()]}`. I want the stack state after every single character — call it out before I write it."*

Run **one character at a time**, taking a prediction before each:

```
{   → push                    Stack: [{]
[   → push                    Stack: [{, []
(   → push                    Stack: [{, [, (]
)   → matches top (           Stack: [{, []
(   → push                    Stack: [{, [, (]
)   → matches top (           Stack: [{, []
]   → matches top [           Stack: [{]
}   → matches top {           Stack: []
```

End of string, stack empty → **valid**.

**How it surfaces:** At each `)`, confirm out loud what's being matched against — students should say "top of stack" every time, not "the most recent `(` I can remember," which breaks down on longer strings.

**Debrief line:**
> *"Every close only ever looks at one thing: the top of the stack. Not the whole string, not memory — just the top. That's the entire algorithm, executed eight times in a row."*

**Cut rule:** If running short, do the first four characters only (`{[()`) plus the final `}` — enough to show both a push run and a full unwind to empty.

---

## Exit Ticket (36–40 min)

> Is `"[(])"` valid? Name the exact character where it fails and which failure mode it is.
> **Answer:** Invalid. At `]` (third character): stack top is `(` (from the second character), which doesn't match `]` — mismatched-type-on-close.

Scan responses on the way out. If several students pick the wrong character, that's "checking against the top of the stack" not yet automatic — worth a 90-second recap opening Session 56.

**Homework:** trace `"{[]}()"` and `"{(})"` by hand, stating valid/invalid and why. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Equal counts of each bracket type means the string is balanced | Feels like the natural, simpler check | Slide Block A's `(){[{}])` example — 3 and 3, still invalid |
| Any closing bracket that has *appeared before* is a valid match | Students track "have I seen this type opened" instead of "is it on top *right now*" | Activity 1, string 2 (`([)]`) — `[` was opened, but it's not on top when `)` arrives |
| A string with only opening brackets and no closers is "vacuously valid" since nothing ever mismatched | Nothing throws an error mid-scan, so it feels fine | Activity 1, string 3 (`(((`) — explicitly checking the stack is empty *after* the loop ends |
| The stack needs to be checked for emptiness only at the very end | Natural to think of "empty" as a final-state property | Activity 1, string 4 (`)(`) — the empty-stack check has to happen the instant a closing bracket arrives, mid-scan |
| Matching is based on position/index bookkeeping rather than the stack itself | Some students want to track opening positions in a separate array out of habit | Point out the stack's *top* already encodes "most recent unmatched opening" — no extra bookkeeping needed |

---

## Instructor Notes

- **This is a fast, clean session — protect the time you save for Session 56, which is heavier.** Do not let Slide Block A run long; the problem statement and both examples are simple enough that over-explaining costs momentum.
- **Activity 1 is the load-bearing activity.** It's the only place students name a *specific* failure mode rather than just voting valid/invalid. Do not cut it; cut Activity 2 to its stated cut rule first if behind.
- **Have all four Activity 1 strings and the Activity 2 string written on the board before class starts** — writing them live burns time you don't have in a 40-minute session.
- **This session is a template for everything that follows in interviews:** stack-based validation of nested/paired structures shows up constantly (HTML tag matching, expression parsing). Say this explicitly if time allows — it's a strong motivation hook for why this "toy" problem matters.
