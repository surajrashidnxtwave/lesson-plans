# Session 52 — Infix, Prefix, and Postfix

**Duration** 60 min <!-- placement: inferred — precedence/associativity rules plus a full multi-operator, parenthesised conversion dry run need more room than 45 --> · **Topic** Stack & Queue — Expression Notations & Infix-to-Postfix Conversion · **Prerequisite** Session 51 — Stack Implementation Using Linked List
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Infix, Prefix, and Postfix | https://docs.google.com/presentation/d/1f58-Pm4m_3hWMLcdPlCaZW8_zwLXOMh4HGyf3m-VNE0/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. Define operator, operand, and expression, and state the precedence/associativity of `()`, `^`, `*`/`/`/`%`, and `+`/`-`. *(REMEMBERING)*
2. Distinguish infix, prefix (Polish Notation), and postfix (Reverse Polish Notation) by where the operator sits relative to its operands. *(UNDERSTANDING)*
3. Explain why prefix and postfix notation never need parentheses to fix evaluation order. *(UNDERSTANDING)*
4. Convert an infix expression containing parentheses and mixed-precedence operators to postfix, using the stack-based algorithm. *(APPLYING)*
5. Trace the algorithm's stack and output contents symbol by symbol for a multi-operator expression. *(ANALYZING)*

---

## Warm-Up Poll — Retrieval Practice on Session 51 (Stack Using Linked List) (0–6 min)

Say: *"Eight on the linked-list stack, then we leave stack mechanics behind for a bit and talk about expressions."*

**Q1.** In a linked-list stack, `top` corresponds to:
`A` The tail of the list · `B` The head of the list · `C` The middle node · `D` A separate index variable

**Q2.** The correct order for `push` is:
`A` Update top → link new node → create node · `B` Create node → link its `next` to the old top → update top · `C` Link next → create node → update top · `D` Order doesn't matter

**Q3.** The correct order for `pop` is:
`A` Delete the top node → then move `top` to `top->next` · `B` Move `top` to `top->next` → then delete the old node · `C` Delete and move simultaneously · `D` Order doesn't matter
→ *Read:* If Q2/Q3 aren't both near-unanimous, that's last session's core rule not sticking — a 20-second recap is worth it before moving to today's very different topic.

**Q4.** What happens if you delete the old top node *before* reassigning `top`?
`A` Nothing, it's equivalent · `B` `top` becomes a dangling pointer — reading `top->next` next is undefined behaviour · `C` The stack silently becomes empty · `D` A compile error

**Q5 (MSQ — pick all correct).** Which are genuine advantages of a linked-list stack over an array-based one?
`A` Dynamic size · `B` No fixed-capacity overflow · `C` Faster direct-index access · `D` No shifting of other elements needed for push/pop
→ *Read:* Correct: A, B, D. C is the array stack's advantage, not the linked list's — if anyone picks it, that's the two implementations blending together in memory.

**Q6.** True or False: a linked-list stack can never fail to push under any circumstances.
`A` True · `B` False

**Q7.** `empty()` on a linked-list stack checks:
`A` `top == -1` · `B` `top == null` · `C` `size == capacity` · `D` `top->next == null`

**Q8.** What is the time complexity of push, pop, and top on a linked-list stack?
`A` O(n) · `B` O(1) · `C` O(log n) · `D` Depends on stack size

**Running it** — poll tool, ~30 s per question. Total 6 min including reads.

---

## Hook (6–9 min)

Write on the board: `8 / 4 + 2`. Ask: *"What does this evaluate to?"* Someone will say 4 (÷ first, giving 2, plus 2 = 4). Good — that's correct, and it only works because *you* know division happens before addition.

Then say: *"Now imagine you're a very literal calculator with no built-in sense of precedence, reading left to right. How do I write this expression so you can evaluate it correctly *without ever needing to know a precedence rule, and without a single parenthesis*?"*

Let a couple of guesses land or fail. Then: *"That's not a trick question — it's a solved problem, and it's exactly what postfix and prefix notation are for. By the end of today you'll convert any expression into a form a dumb, literal machine can evaluate with zero ambiguity."*

---

## Slide Block A (9–17 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 5–8: Operator & Operand, Expression, Parentheses, Precedence & Associativity -->
Covers: Operator vs Operand → Expression → Parentheses (grouping, not operators/operands themselves) → Precedence & Associativity table.

**Beats to emphasise**

- **Operators** (`+ - * / ^`) act on **operands** (`a, b, 1, 2, A, B...`). Simple, but say it explicitly — the whole conversion algorithm is built on classifying every symbol as one or the other (plus parentheses as a third, special case).
- **Precedence table, read top to bottom exactly as ranked:** `()` highest, `^` (right-to-left associativity), `*`/`/`/`%` (left-to-right), `+`/`-` (left-to-right, lowest).
- **Associativity is the tie-breaker when precedence is equal.** Flag `^` as the odd one out — it's the only right-to-left operator in this table, and it will matter later in the worked example (`... ^ f` at the very end of the expression).

**Checkpoint (at 17 min)** — cold-call:
> *"`a + b * c` — which operator executes first, and why?"*
> **Answer:** `*` executes first — multiplication has higher precedence than addition, regardless of left-to-right reading order.

---

## ⚡ Activity 1 — Predict-the-Output: "Write It Without Parentheses" (17–23 min)

**Format:** Predict-the-Output · **Exposes:** whether students can apply precedence intuitively to produce postfix *before* seeing the formal stack algorithm — priming them for why the algorithm's precedence-popping rule exists.

**Setup line (say this):**
> *"No algorithm yet — just your own reasoning. `8 / 4 + 2`. Operators must come *after* their operands, no parentheses allowed. Write the postfix form. Ninety seconds, then I want hands up."*

**What students do:** Attempt it individually, then share answers.

**Correct answer (from the deck's own example):** `8 4 / 2 +`

Follow immediately with a harder one: `a + (b * c) - d` → expect `a b c * + d -`.

**How it surfaces:** The most common wrong answer for the second one is `a b + c * d -` (evaluating strictly left to right, ignoring that `*` inside the parentheses must resolve *before* the `+`). When this comes up, ask: *"If a machine read your version left to right, what would it compute first — and is that what the original expression means?"* Let them catch the mismatch themselves.

**Debrief line:**
> *"Even with no parentheses, postfix isn't 'operators wherever' — precedence is still baked into the *order* operators appear in. The mechanical algorithm you're about to see is really just a disciplined way of getting this right every single time, even when you can't eyeball it."*

**Cut rule:** If running short, do only `8 / 4 + 2` and skip the harder parenthesised example — the core insight (precedence still matters) is demonstrated either way.

---

## Slide Block B (23–31 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 9–13: Infix, Prefix (Polish Notation), Postfix (Reverse Polish Notation), Examples table -->
Covers: Infix (operator between operands — what humans read) → Prefix / Polish Notation (operator *before* operands) → Postfix / Reverse Polish Notation, RPN (operator *after* operands) → side-by-side examples table for all three expressions.

**Beats to emphasise**

- **Infix is for humans; prefix and postfix are for machines.** Say this framing explicitly — it's the "why do we even need this" answer.
- Walk the examples table row by row: `8 / 4 + 2` → prefix `+ / 8 4 2`, postfix `8 4 / 2 +`. `a + (b*c) - d` → prefix `- + a * b c d`, postfix `a b c * + d -`. Point out these match exactly what Activity 1 just had students derive by hand.
- **Neither prefix nor postfix ever needs a parenthesis** — the position of the operator itself fully encodes the grouping. This is the direct payoff of the Hook's challenge.

**Checkpoint (at 31 min)** — show of hands:
> *"In postfix `a b c * +`, which operation happens first when this is evaluated?"*
> **Answer:** `b * c` — you scan left to right and evaluate an operator the moment you have its two operands ready.

---

## **Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block C (36–50 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred slide grouping — Slides 14–38: Steps to Convert Infix to Postfix, worked example on "a + b * c / (d - e) ^ f" -->
Covers: The stack-based conversion algorithm, then the deck's full worked dry run on `a + b * c / (d - e) ^ f`.

**Beats to emphasise**

- **The four rules, stated as a checklist before the dry run starts:**
  1. Operand → straight to output.
  2. `(` → push onto the stack.
  3. `)` → pop to output until a `(` is found, then **discard** that `(` (it never goes to output or stays on the stack).
  4. Operator → pop from the stack to output while the stack's top has **higher or equal precedence** (respecting associativity), *then* push the current operator.
  5. After the scan finishes: pop everything remaining on the stack to the output.
- Narrate the full dry run on `a + b * c / (d - e) ^ f` symbol by symbol, matching the deck exactly:
  - `a` → output. `+` → stack empty, push. `b` → output. `*` → top of stack is `+` (lower precedence), so don't pop, push `*`. `c` → output. `/` → top is `*` (equal precedence, left-to-right), pop `*` to output, then push `/`. `(` → push. `d` → output. `-` → top is `(`, don't pop across it, push `-`. `e` → output. `)` → pop `-` to output, discard the `(`. `^` → top is now `/` (lower precedence than `^`), don't pop, push `^`. `f` → output. End of string → pop remaining stack (`^`, then `/`, then `+`) to output.
  - **Final postfix:** `a b c * d e - f ^ / +`
- **Call out the one subtlety that trips students up:** when comparing `/` (already on stack) against the incoming `^`, `^` is higher precedence, so nothing gets popped — `^` is simply pushed on top. This is the associativity/precedence rule doing real work, not just parentheses.

**Checkpoint (at 50 min)** — cold-call, mid-recap:
> *"Right after processing the `)` that closes `(d - e)`, what does the stack contain, and what has the output produced so far?"*
> **Answer:** Stack: `+`, `/` (from before the parenthesis). Output so far: `a b c * d e -`.

---

## ⚡ Activity 2 — Live Coding / Dry-Run Relay: "Finish the Conversion" (50–56 min)

**Format:** Live Coding / Dry-Run Relay · **Exposes:** whether students can apply the precedence-popping rule themselves on the trailing, trickiest part of the expression (the `^`, which is right-associative and interacts with `/` sitting on the stack) — rather than just having watched it happen.

**Setup line (say this):**
> *"We're picking up exactly where the last slide left off: stack has `+`, `/` on it, output is `a b c * d e -`. Remaining symbols: `^`, `f`, end-of-string. For each one, tell me the new stack and the new output *before* I confirm."*

Run **one symbol at a time**:

```
^     → Compare against stack top '/'. ^ has HIGHER precedence than /, so do NOT pop.
        Push ^.  Stack: [+, /, ^]   Output: a b c * d e -
f     → Operand, straight to output.
        Stack: [+, /, ^]   Output: a b c * d e - f
(end) → Pop everything left on the stack, in order: ^, then /, then +.
        Stack: []   Output: a b c * d e - f ^ / +
```

**How it surfaces:** Before revealing the `^` step, ask explicitly: *"Would we pop `/` here if `^` were left-to-right associative instead of right-to-left?"* Correct answer: yes — this is the one place in the whole example where associativity (not just precedence) changes the outcome, so make students state *why* `^` doesn't trigger a pop.

**Common wrong answer:** students pop `/` before pushing `^`, treating "higher or equal precedence" as "any operator on the stack." Correct by re-reading rule 4 aloud: pop only while the stack top is higher-**or-equal**, and `/` is lower than `^`, so it stays.

**Debrief line:**
> *"You just did, symbol by symbol, exactly what the algorithm does mechanically. There's no cleverness beyond 'compare precedence, maybe pop, always push.' That's why a computer can do this reliably and a human parsing by eye can't, at scale."*

**Cut rule:** If running short, skip the associativity discussion question and just confirm each step's stack/output directly.

---

## ⚡ Activity 3 — Spot the Bug: "The Missing Discard" (56–58 min)

**Format:** Spot the Bug · **Exposes:** the easy-to-miss detail in rule 3 — that the matching `(` must be *discarded*, not pushed to output or left on the stack.

**Setup line (say this):**
> *"Someone converts `(a + b) * c` and gets this buggy postfix. Ten seconds — what's wrong?"*

Put this on screen:

```
Buggy output:  a b + ( * c
```

**What students do:** Hands up with the error.

**Answer:** The `(` should never appear in the output at all — rule 3 says pop to output until you *hit* the `(`, then throw the `(` away. The correct postfix is `a b + c *`.

**Debrief line:**
> *"The open parenthesis's whole job is to be a wall on the stack that stops premature popping. Once a matching `)` arrives and does its job, the `(` has nothing left to do — it's discarded, never emitted."*

**Cut rule:** If running very short, cut this activity entirely and fold the rule restatement into the Slide Block C beats instead.

---

## Exit Ticket (58–60 min)

> Convert `a * b + c` to postfix by hand, one symbol at a time, showing the stack state after each step.
> **Answer:** `a` → output `a`. `*` → stack empty, push. `b` → output `a b`. `+` → top is `*` (higher/equal precedence), pop `*` to output, then push `+`. Output: `a b *`. `c` → output `a b * c`. End → pop `+`. **Final postfix: `a b * c +`.**

Scan responses on the way out. If several students stop popping too early (leaving an operator stranded on the stack at the end), that's rule 5 ("pop everything remaining after the scan") not landing — reopen Session 53 with a quick recap.

**Homework:** convert `(a - b) / c * d` to postfix by hand, tracking stack and output at every symbol. <!-- placement: inferred — no homework/RM/practice units exist for this course per deviation #2 -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| Postfix conversion ignores precedence since there are no parentheses | "No parentheses" sounds like "no rules" | Activity 1 — showing that a naive left-to-right postfix guess produces the wrong evaluation order |
| The matching `(` is popped to the output along with the operators inside it | Rule 3 is easy to skim as "pop until you see `(`" without registering "then discard it" | Activity 3's Spot the Bug, isolating exactly this omission |
| "Pop while higher precedence" also means pop when precedence is lower or unrelated | The rule has two clauses (precedence *and* associativity) that are easy to compress into one | Slide Block C's explicit narration of the `/` vs `^` comparison, where nothing is popped |
| Right-to-left associativity (`^`) behaves the same as left-to-right (`+ - * /`) | Every other operator in the table is left-to-right, so `^` looks like an outlier worth ignoring | Activity 2's explicit "would we pop if `^` were left-associative?" question |
| Prefix and postfix are just infix "with the operator moved," evaluated the same way | The three notations share the same operators and operands, so they look like cosmetic reorderings | Slide Block B's checkpoint — evaluating `a b c * +` left to right and showing operators fire the moment both operands are ready, a genuinely different evaluation mechanism |

---

## Instructor Notes

- **This session has no code implementation slides** — the deck stays entirely at the level of manual conversion (rules + one fully worked dry run). Don't introduce code; match the deck's own scope.
- **The single worked example (`a + b * c / (d - e) ^ f`) is doing a lot of work** — it's the only concrete conversion the deck runs end-to-end, and it deliberately includes every rule (operand, low-precedence push, equal-precedence pop, parenthesis push/pop/discard, and the one associativity exception with `^`). Don't substitute a simpler example for pacing — if you must trim, cut narration speed, not coverage.
- **The `^` vs `/` step is the one moment worth slowing down for.** Everything else in the dry run is "pop while higher-or-equal, then push" on repeat; this is the only step where the answer is "don't pop," and it's precisely because of associativity, not precedence alone.
- **Pacing risk:** Slide Block C is the longest block of the session (25 slides in the deck, animated step by step). If you're behind schedule entering it, narrate faster rather than skipping steps — every step sets up either the mid-block checkpoint or Activity 2's continuation.
- **Set up the next few sessions.** Closing line, if time allows: "Everything from here — monotonic stacks, next greater element, balanced parentheses — reuses this exact 'push while a condition holds, otherwise pop' pattern. Today's mechanical rule *is* the pattern."
