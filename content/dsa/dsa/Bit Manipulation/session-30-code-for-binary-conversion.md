# Session 30 — Code for Binary Conversion

**Duration** 45 min · **Topic** Bit Manipulation — Implementing Decimal↔Binary Conversion · **Prerequisite** Session 29 (Binary Operations)
**Session type** Concept lecture

**Platform units**

| Resource | Unit ID |
|---|---|
| Video + deck — Code for Binary Conversion | https://docs.google.com/presentation/d/1YVnOvqCzI51L5D70ct18HYf65TdWnnYxDO8LwEHJ3fA/edit |

---

## Learning Objectives

By the end of this session, students will be able to:

1. State the time and space complexity of decimal-to-binary conversion — O(log₂ n) for both. *(REMEMBERING)*
2. State the time and space complexity of binary-to-decimal conversion — O(n) time, O(1) space. *(REMEMBERING)*
3. Trace the `decimal_to_binary` and `binary_to_decimal` pseudocode line by line on a fresh input. *(APPLYING)*
4. Explain why one algorithm's space complexity depends on the input value (log₂ n) while the other's depends on the input's string length and stays constant. *(ANALYZING)*
5. Implement both conversions in Python. *(APPLYING)*

<!-- placement: inferred from the two Complexity Analysis blocks (slides 12-13, 25-26) and the closing recap slide (30), which is the only content this deck adds beyond a restatement of Session 29's algorithms. -->

---

## Warm-Up Poll — Retrieval Practice on Session 29 (0–6 min)

> Retrieval practice on **Binary Operations**. Solo answers, no discussion yet.

**Q1.** The least significant bit (LSB) of a binary number is:
`A` The leftmost bit · `B` The rightmost bit · `C` Whichever bit is 1 · `D` The middle bit
→ *Read:* Answer B.

**Q2.** In the decimal-to-binary division-by-2 method, why must you reverse the collected remainders before returning them?
`A` You don't have to — it's already in the right order · `B` The remainders are collected LSB-first, but the binary number reads MSB-first · `C` Reversing makes the number smaller · `D` It's a Python-specific quirk
→ *Read:* Answer B.

**Q3.** In `binary_to_decimal`, the loop starts at index `n-1` and goes down to `0`. What does index `n-1` represent?
`A` The MSB · `B` The LSB · `C` The sign bit · `D` An error index
→ *Read:* Answer B — rightmost character in the string is the LSB.

**Q4.** Binary addition: `1 + 1 + 1` (with an incoming carry) equals what, and what do you write down for that column?
`A` 3, write 3 · `B` 3 in binary is 11, write 0 and carry 1 · `C` 2, write 0 carry 1 · `D` 1, no carry
→ *Read:* Answer B.

**Q5.** The "subtraction without borrow" shortcut computes each bit as `bitA XOR bitB`. When does this shortcut give a *correct* answer?
`A` Always, for any two binary numbers · `B` Only when no column ever needs to borrow from the next one · `C` Only when both numbers are even · `D` Never — it's always wrong
→ *Read:* Answer B.

**Q6.** *(MSQ — pick 2)* To compute the 2's complement of a binary number, you:
`A` Add 1 to the original number · `B` Invert every bit (1's complement) · `C` Then add 1 to that inverted result · `D` Multiply by -1
→ *Read:* B and C.

**Q7.** After a 2's-complement subtraction, the result's MSB is 1. What does that tell you?
`A` Nothing — MSB is irrelevant · `B` The true answer is negative, and you take the 2's complement of the result to find its magnitude · `C` The calculation is invalid · `D` The true answer is exactly 1
→ *Read:* Answer B.

**Running it** — poll tool, ~40 s per question. Total 6 min.

---

## Hook (6–9 min)

Say: *"Yesterday you did the division-by-2 method and the positional-value method by hand, on paper, with me writing on the board. Today the question is different: if I ask you to defend, in Big-O, exactly how expensive each of those two algorithms is — could you? Today's deck exists for exactly that question, and it has one edge case buried in it that will catch almost everyone."*

Write both function signatures on the board with nothing else: `decimal_to_binary(n)` and `binary_to_decimal(str)`. Say: *"By the end of this session you can state the time and space complexity of both, from memory, and you'll know the one input that breaks one of them."*

---

## Slide Block A (9–18 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 4-16, 32-38: decimal-to-binary problem, approach, pseudocode walkthrough, and complexity -->
Covers: Decimal-to-Binary problem statement → worked example (`25 → 11001`) → approach (divide by 2, record remainder, repeat until quotient is 0, reverse) → pseudocode walked line-by-line → complexity analysis → C++/Python code.

**Beats to emphasise**

- This is the **same algorithm as Session 29** — say that explicitly, don't re-teach it as new. The value-add today is the complexity argument: *"The algorithm halves n every iteration (integer division by 2), so the number of iterations is log₂ n. For a 32-bit integer that's a hard ceiling around 31 iterations — that's where the deck's 'O(31)' comment comes from."*
- Space complexity is O(log₂ n) too, and for the *same reason* as time: the string `s` grows by one character per iteration, and there are log₂ n iterations.
- Run the Python code (`decimal_to_binary`) live for `n = 25` and confirm `11001`.

**Checkpoint (at 18 min)** — cold-call:
> *"Time complexity of decimal-to-binary, one phrase. Space complexity, one phrase."*
> **Answer:** Time: O(log₂ n), because n halves each loop. Space: O(log₂ n), because the output string's length tracks the number of loop iterations.

---

## ⚡ Activity 1 — Spot the Bug: What Does `decimal_to_binary(0)` Return? (18–23 min)

**Format:** Spot the Bug / Predict-the-Output · **Exposes:** an edge case the deck's own pseudocode never addresses — the loop condition `while (n != 0)` never executes when `n` starts at 0, and the deck never shows an explicit `if n == 0` guard for this algorithm.

**Setup line (say this):**
> *"One line of code, one input. `decimal_to_binary(0)`. Trace the pseudocode from the slide exactly as written — no assumptions, no 'obviously it should return...'. What does the code you were just shown actually return?"*

Put the deck's own pseudocode back on screen:
```
decimal_to_binary(n) {
    s = ""
    while (n != 0) {
        if (n % 2 == 0) s += "0"
        else s += "1"
        n = n / 2
    }
    reverse(s)
    return s
}
```

**What students do:** Trace it by hand for `n = 0`. The `while` condition is `0 != 0`, which is false immediately — the loop body never runs. `s` stays `""`. The function returns an **empty string**, not `"0"`.

**How to handle wrong answers:** If someone confidently says "it returns `0`," ask them to point to the exact line that would produce that character. There isn't one — that's the point. This isn't a trick question about binary; it's a trick question about reading a loop condition literally.

**Debrief line:**
> *"This pseudocode has a real gap: it never special-cases zero. In a proper implementation you'd add `if n == 0: return "0"` before the loop. Reading code means trusting what's on the screen over what you assume the answer 'should' be — that's the whole skill this activity is testing."*

**Cut rule:** If short on time, skip the group trace and just ask the question cold, then reveal the answer directly — the core "empty string, not zero" catch survives without the group work.

---

**Classroom Quiz** (~5 min) — 5-6 MCQs from the platform bank, run here. <!-- placement: inferred — question bank not available in source material; instructor to pull from platform -->

---

## Slide Block B (28–38 min) — DELIVER SLIDES AS-IS

<!-- placement: inferred — slides 17-29, 43-58: binary-to-decimal problem, approach, pseudocode walkthrough, complexity -->
Covers: Binary-to-Decimal problem statement → worked example (`11001 → 25`) → approach (positional value, right to left) → pseudocode walked line-by-line → complexity analysis → C++/Python code.

**Beats to emphasise**

- Contrast directly against Slide Block A: this algorithm's loop runs once **per character in the string**, not once per halving of a value — so its time complexity is O(n) where n is the **string length**, not O(log₂ n).
- Space complexity is O(1) — `ans`, `prod`, `n` are three fixed variables regardless of how long the input string is. This is the sharpest contrast in the whole session: decimal→binary's space *grows* with input size, binary→decimal's space *doesn't*, even though both algorithms are doing "the same conversion" in opposite directions.
- Run the Python code live for `"11001"` and confirm `25`.

**Checkpoint (at 38 min)** — show of hands:
> *"True or false: binary-to-decimal is O(log₂ n) time, same as decimal-to-binary."*
> **Answer:** False. It's O(n) time where n is the length of the input string — the loop runs once per character, it doesn't halve anything.

---

## ⚡ Activity 2 — Predict-the-Output: Counting Iterations to Prove the Complexity (38–43 min)

**Format:** Predict-the-Output · **Exposes:** treating "O(log₂ n)" and "O(n)" as interchangeable labels instead of counts you can actually verify by tracing.

**Setup line (say this):**
> *"Two predictions, no code running yet. First: how many times does the `while` loop in `decimal_to_binary` execute for `n = 100`? Second: how many times does the `for` loop in `binary_to_decimal` execute for the string `"1100100"` (which is 100 in binary)?"*

**What students do:** For the first, they should reason `log₂ 100 ≈ 6.6`, so the loop runs **7 times** (100 → 50 → 25 → 12 → 6 → 3 → 1 → 0, count the divisions). For the second, they should just count characters: `"1100100"` has **7 characters**, so the loop runs exactly 7 times.

**How to handle wrong answers:** If someone gives a fractional answer for the first ("6.6 times"), redirect: loop counts are always whole numbers — walk the actual divide-by-2 chain on the board until it hits 0 and count the steps.

**Debrief line:**
> *"Both loops happened to run 7 times for this particular input — that's not a coincidence, it's because the number of bits needed to represent 100 IS log₂ 100 rounded up. But the reason they run 7 times is completely different: one is halving a value, the other is walking a string. Same iteration count today, completely different growth behavior as the numbers get bigger — try n = 1,000,000 in your head and the gap becomes obvious."*

**Cut rule:** If short on time, run only the decimal-to-binary prediction (`n = 100`) and drop the binary-to-decimal half — the O(log₂ n) proof is the higher-value half of this activity.

---

## Exit Ticket (43–45 min)

> On paper before anyone leaves: What does `decimal_to_binary(0)` actually return according to the pseudocode shown today — and what *should* it return for a correct implementation?
> **Answers:** Returns `""` (empty string) as written. A correct implementation should return `"0"` — this requires an explicit zero-check the deck's pseudocode doesn't have.

Scan responses on the way out. If most students still say "it returns `0`," Activity 1's core lesson didn't land — reopen with the pseudocode trace at the start of Session 31.

**Homework:** re-attempt today's dry run — trace `decimal_to_binary` and `binary_to_decimal` for a number you pick yourself, then check by hand. <!-- placement: inferred — no homework/practice-unit table exists for this course; this is a natural close, not a platform assignment -->

---

## Common Misconceptions

| Misconception | Why students hold it | Correct it live by |
|---|---|---|
| `decimal_to_binary(0)` returns `"0"` | It's the "obviously correct" answer, and nobody traces the loop condition literally | Activity 1 — tracing the pseudocode line by line and finding no code path that produces the character `"0"` |
| Binary-to-decimal is also O(log₂ n) time, "because it's the reverse operation" | Both functions convert between the same two representations, so complexities feel like they should match | Slide Block B checkpoint — the loop runs once per **character**, not once per **halving** |
| A longer time complexity label (O(n) vs O(log₂ n)) always means a slower algorithm in practice | Big-O notation is taught as an ordering without reference to what n actually is | Activity 2 — showing both loops ran the same number of times (7) for this specific input, before the growth rates diverge |
| Space complexity must always equal time complexity for the same function | Most of the algorithms students have seen so far have matching time/space Big-O | Contrasting decimal-to-binary (O(log₂ n) both) against binary-to-decimal (O(n) time, O(1) space) directly, back to back |
| "Trace the code" means predicting what a correct version *should* do, not what the code on screen *actually* does | Students default to reasoning about the algorithm's intent rather than its literal implementation | Activity 1's explicit framing: "no assumptions, no 'obviously it should return'" |

---

## Instructor Notes

- **This is a lighter, code-focused session** deliberately following the heavier Session 29 — resist the urge to re-teach the division-by-2 or positional-value methods from scratch. Say "same algorithm as yesterday" and move straight to complexity.
- **The n=0 edge case (Activity 1) is the single highest-value catch in this session** — it's a genuine gap in the deck's own pseudocode, not a contrived teaching example, so it rewards students for reading code skeptically rather than trusting the "obvious" answer.
- **Keep both function signatures on the board for the full session** (`decimal_to_binary(n)` and `binary_to_decimal(str)`) — students will otherwise start describing one algorithm's complexity while thinking about the other.
- **Have the editor open with both Python snippets ready to run** before the session starts — running `decimal_to_binary(25)` and `binary_to_decimal("11001")` live takes 30 seconds each and is worth doing exactly once per function, not more (the deck's own repeated pseudocode-walkthrough slides already provide the line-by-line detail).
- **Classroom Quiz question bank does not exist yet for this course** — pull 5–6 MCQs from the platform live at the marked point.
