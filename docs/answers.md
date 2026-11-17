# 21CSC301T – JFLAP Activity – SET 43 (Reg. No. …163)

| Q | Unit | Task | JFLAP file |
|---|------|------|------------|
| Q1 | U4 | PDA for {w c wᴿ \| w ∈ {a,b}*}, trace `abcba` | `Q1_PDA_wcwR.jff` |
| Q2 | U5 | TM deciding {aⁿ bⁿ cᵐ \| n, m ≥ 1} | `Q2_TM_anbncm.jff` |
| Q3 | U3 | Remove useless symbols | `Q3_Grammar_original.jff`, `Q3_Grammar_simplified.jff` |
| Q4 | U3 | Remove inaccessible symbols | `Q4_Grammar_original.jff`, `Q4_Grammar_simplified.jff` |

---

## Q1 – PDA for L = { w c wᴿ | w ∈ {a, b}* }

**Idea:** push every symbol of w. On `c`, switch to matching mode. Pop one symbol for each input symbol that matches the top of the stack. When only Z (the start stack symbol) is left, accept.

States: q0 (start, push), q1 (match/pop), q2 (final). Accept by **final state**.

| From | Input | Pop | Push | To | Meaning |
|------|-------|-----|------|----|---------|
| q0 | a | λ | a | q0 | push a |
| q0 | b | λ | b | q0 | push b |
| q0 | c | λ | λ | q1 | middle marker, switch mode |
| q1 | a | a | λ | q1 | match a |
| q1 | b | b | λ | q1 | match b |
| q1 | λ | Z | Z | q2 | stack back to Z → accept |

Formal: P = ({q0,q1,q2}, {a,b,c}, {a,b,Z}, δ, q0, Z, {q2})

**Trace of `abcba`** (state, remaining input, stack; top is on the left):

```
(q0, abcba, Z)
⊢ (q0, bcba,  aZ)     push a
⊢ (q0, cba,   baZ)    push b
⊢ (q1, ba,    baZ)    read c
⊢ (q1, a,     aZ)     pop b
⊢ (q1, λ,     Z)      pop a
⊢ (q2, λ,     Z)      ACCEPT
```

**Test strings** (Input → Multiple Run in JFLAP):

| Accepted | Rejected |
|----------|----------|
| `c` | `λ` (empty) |
| `aca` | `abcab` |
| `abcba` | `abcb` |
| `abbcbba` | `abccba` |
| `aabcbaa` | `abcbaa` |

---

## Q2 – TM deciding L = { aⁿ bⁿ cᵐ | n, m ≥ 1 }

**Idea:** repeatedly mark one `a` as X and its matching `b` as Y. When no unmarked `a` is left, check that only Y's remain before the c's, then check there is at least one `c` and nothing after.

| State | Read | Write | Move | Next | Meaning |
|-------|------|-------|------|------|---------|
| q0 | a | X | R | q1 | mark an a |
| q0 | Y | Y | R | q3 | all a's used up → verify rest |
| q1 | a | a | R | q1 | skip a's |
| q1 | Y | Y | R | q1 | skip Y's |
| q1 | b | Y | L | q2 | mark matching b |
| q2 | a | a | L | q2 | go back |
| q2 | Y | Y | L | q2 | go back |
| q2 | X | X | R | q0 | next round |
| q3 | Y | Y | R | q3 | skip Y's |
| q3 | c | c | R | q4 | at least one c |
| q4 | c | c | R | q4 | skip c's |
| q4 | □ | □ | S | q5 | end of input → ACCEPT |

q0 = start, q5 = final (halt-accept). Any missing transition means halt and reject.

**Example run on `aabbc`:**

```
(state written just before the symbol under the head)
q0aabbc ⊢ Xq1abbc ⊢ Xaq1bbc ⊢ Xq2aYbc ⊢ q2XaYbc ⊢ Xq0aYbc
⊢ XXq1Ybc ⊢ XXYq1bc ⊢ XXq2YYc ⊢ Xq2XYYc ⊢ XXq0YYc
⊢ XXYq3Yc ⊢ XXYYq3c ⊢ XXYYcq4□ ⊢ XXYYcq5□   ACCEPT
```

**Test strings:**

| Accepted | Rejected |
|----------|----------|
| `abc` | `ab` (no c) |
| `aabbc` | `aabc` (more a's) |
| `abccc` | `abbc` (more b's) |
| `aaabbbcc` | `abca`, `acb`, `bc` |

---

## Q3 – Remove useless symbols

G: S → AB | a, A → a, B → b, C → cC | c

**Step 1: generating (productive) symbols.** These can derive a terminal string.
- A → a ✔, B → b ✔, C → c ✔, S → a ✔
- All of S, A, B, C are generating, so nothing is removed in this step.

**Step 2: reachable symbols from S.**
- S → AB, so A and B are reachable. S → a gives terminal a. A gives a, B gives b.
- **C never appears on the right side of any production reachable from S**, so C (and terminal c) are unreachable.

**Simplified grammar:**

```
S → AB | a
A → a
B → b
```

Useless symbols removed: **C, c**. Language unchanged: L = {ab, a}.

In JFLAP: open `Q3_Grammar_original.jff` → **Convert → Transform Grammar**. It walks through λ-removal, unit removal and useless-symbol removal. Take a screenshot of the useless-production step.

---

## Q4 – Remove inaccessible symbols

G: S → aA | b, A → a, B → bB | b

**Reachability from S:**
- Start: {S}
- S → aA adds A. S → b gives a terminal only.
- A → a gives a terminal only.
- No further symbols. Reachable = {S, A}, terminals {a, b}.

**B is inaccessible**, so delete every B-production.

**Simplified grammar:**

```
S → aA | b
A → a
```

L = {aa, b}.

---

## How to submit

1. Open each `.jff` in JFLAP 7.1 (File → Open).
2. Q1: **Input → Step by State** with `abcba` and screenshot each step (this is the trace). Then use **Input → Multiple Run** with the test strings above.
3. Q2: **Input → Multiple Run** with the test table, plus one **Step** run screenshot.
4. Q3/Q4: **Convert → Transform Grammar** screenshots, plus the simplified grammar file.
5. Upload the `.jff` files and screenshots to Google Classroom.
