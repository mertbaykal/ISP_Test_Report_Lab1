# Lab Assignment – Structural & Logic Coverage Analysis
 
## Method Under Test
 
```java
public static double calculateDosage(int age, double weight, boolean isHighRisk, boolean hasAllergy) {
    if ((age > 65 || isHighRisk) && weight < 50 && !hasAllergy || !isHighRisk) {
        return weight * 0.8;
    } else {
        return weight * 1.2 + age * 0.1;
    }
}
```
 
---
 
## Step 1 – Predicates and Clauses
 
The full predicate inside the `if` statement is:
 
```
((age > 65 || isHighRisk) && weight < 50 && !hasAllergy) || !isHighRisk
```
 
We define the following **clauses** (atomic boolean expressions):
 
| Clause | Expression      |
|--------|-----------------|
| **a**  | `age > 65`      |
| **b**  | `isHighRisk`    |
| **c**  | `weight < 50`   |
| **d**  | `hasAllergy`    |
 
So the predicate becomes:
 
```
P = ((a || b) && c && !d) || !b
```
 
We can split it into two top-level sub-predicates joined by `||`:
 
- **p1** = `(a || b) && c && !d`
- **p2** = `!b`
Full predicate: **P = p1 || p2**
 
---
 
## Step 2 – Truth Tables with Active Clause Check
 
### How to check if a clause is "active"
 
A clause `x` is **active** in a row if changing `x` (while keeping everything else the same) would change the value of the whole predicate **P**.
 
---
 
### Truth Table
 
> Note: There are 4 boolean clauses → 2⁴ = 16 combinations.  
> We evaluate `P = ((a || b) && c && !d) || !b` for each row.
 
| Row | a | b | c | d | !b | !d | a\|\|b | (a\|\|b)&&c | p1=(a\|\|b)&&c&&!d | p2=!b | **P=p1\|\|p2** |
|-----|---|---|---|---|----|----|--------|-------------|---------------------|-------|----------------|
| 1   | F | F | F | F | T  | T  | F      | F           | F                   | T     | **T**          |
| 2   | F | F | F | T | T  | F  | F      | F           | F                   | T     | **T**          |
| 3   | F | F | T | F | T  | T  | F      | F           | F                   | T     | **T**          |
| 4   | F | F | T | T | T  | F  | F      | F           | F                   | T     | **T**          |
| 5   | F | T | F | F | F  | T  | T      | F           | F                   | F     | **F**          |
| 6   | F | T | F | T | F  | F  | T      | F           | F                   | F     | **F**          |
| 7   | F | T | T | F | F  | T  | T      | T           | T                   | F     | **T**          |
| 8   | F | T | T | T | F  | F  | T      | T           | F                   | F     | **F**          |
| 9   | T | F | F | F | T  | T  | T      | F           | F                   | T     | **T**          |
| 10  | T | F | F | T | T  | F  | T      | F           | F                   | T     | **T**          |
| 11  | T | F | T | F | T  | T  | T      | T           | T                   | T     | **T**          |
| 12  | T | F | T | T | T  | F  | T      | T           | F                   | T     | **T**          |
| 13  | T | T | F | F | F  | T  | T      | F           | F                   | F     | **F**          |
| 14  | T | T | F | T | F  | F  | T      | F           | F                   | F     | **F**          |
| 15  | T | T | T | F | F  | T  | T      | T           | T                   | F     | **T**          |
| 16  | T | T | T | T | F  | F  | T      | T           | F                   | F     | **F**          |
 
---
 
### Active Clause Analysis
 
A clause is **active** in a row if flipping it changes P.
 
#### Clause **a** (age > 65)
- a is active when b=F (because if b=T and a=F, `a||b` is already T; flipping a doesn't change `a||b`)
- More precisely: a is active when **b=F**, **c=T**, **d=F** (so that p1 depends on a)
- Active rows: **9→changes to 1** (T→T, not determinate)... let's check carefully:
- 
| Row | a | b | c | d | P  | Flip a → P | Active? |
|-----|---|---|---|---|----|------------|---------|
| 1   | F | F | F | F | T  | T (row 9)  | No      |
| 2   | F | F | F | T | T  | T (row 10) | No      |
| 3   | F | F | T | F | T  | T (row 11) | No      |
| 4   | F | F | T | T | T  | T (row 12) | No      |
| 5   | F | T | F | F | F  | F (row 13) | No      |
| 6   | F | T | F | T | F  | F (row 14) | No      |
| 7   | F | T | T | F | T  | T (row 15) | No      |
| 8   | F | T | T | T | F  | F (row 16) | No      |
| 9   | T | F | F | F | T  | T (row 1)  | No      |
| 10  | T | F | F | T | T  | T (row 2)  | No      |
| 11  | T | F | T | F | T  | T (row 3)  | No      |
| 12  | T | F | T | T | T  | T (row 4)  | No      |
| 13  | T | T | F | F | F  | F (row 5)  | No      |
| 14  | T | T | F | T | F  | F (row 6)  | No      |
| 15  | T | T | T | F | T  | T (row 7)  | No      |
| 16  | T | T | T | T | F  | F (row 8)  | No      |
 
> **Clause a is never active!**  
> This is because `p2 = !b` — whenever `b=F`, p2=T makes P=T regardless of a. And when `b=T`, `!b=F`, so P depends on p1=`(a||b)&&c&&!d` = `(a||T)&&c&&!d` = `c&&!d`, which doesn't depend on a either. **Clause a has no effect on P.**
 
#### Clause **b** (isHighRisk)
Flip b and check if P changes:
 
| Row | a | b | c | d | P  | Flip b (paired row) | P'     | Active? |
|-----|---|---|---|---|----|----------------------|--------|---------|
| 1   | F | F | F | F | T  | Row 5  (a=F,b=T,c=F,d=F) | F  | **Yes** |
| 2   | F | F | F | T | T  | Row 6  | F      | **Yes** |
| 3   | F | F | T | F | T  | Row 7  | T      | No      |
| 4   | F | F | T | T | T  | Row 8  | F      | **Yes** |
| 5   | F | T | F | F | F  | Row 1  | T      | **Yes** |
| 6   | F | T | F | T | F  | Row 2  | T      | **Yes** |
| 7   | F | T | T | F | T  | Row 3  | T      | No      |
| 8   | F | T | T | T | F  | Row 4  | T      | **Yes** |
| 9   | T | F | F | F | T  | Row 13 | F      | **Yes** |
| 10  | T | F | F | T | T  | Row 14 | F      | **Yes** |
| 11  | T | F | T | F | T  | Row 15 | T      | No      |
| 12  | T | F | T | T | T  | Row 16 | F      | **Yes** |
| 13  | T | T | F | F | F  | Row 9  | T      | **Yes** |
| 14  | T | T | F | T | F  | Row 10 | T      | **Yes** |
| 15  | T | T | T | F | T  | Row 11 | T      | No      |
| 16  | T | T | T | T | F  | Row 12 | T      | **Yes** |
 
**Clause b is active in rows: 1, 2, 4, 5, 6, 8, 9, 10, 12, 13, 14, 16**
 
#### Clause **c** (weight < 50)
Flip c and check:
 
| Row | a | b | c | d | P  | Flip c (paired row) | P'  | Active? |
|-----|---|---|---|---|----|----------------------|-----|---------|
| 1   | F | F | F | F | T  | Row 3  | T   | No      |
| 2   | F | F | F | T | T  | Row 4  | T   | No      |
| 3   | F | F | T | F | T  | Row 1  | T   | No      |
| 4   | F | F | T | T | T  | Row 2  | T   | No      |
| 5   | F | T | F | F | F  | Row 7  | T   | **Yes** |
| 6   | F | T | F | T | F  | Row 8  | F   | No      |
| 7   | F | T | T | F | T  | Row 5  | F   | **Yes** |
| 8   | F | T | T | T | F  | Row 6  | F   | No      |
| 9   | T | F | F | F | T  | Row 11 | T   | No      |
| 10  | T | F | F | T | T  | Row 12 | T   | No      |
| 11  | T | F | T | F | T  | Row 9  | T   | No      |
| 12  | T | F | T | T | T  | Row 10 | T   | No      |
| 13  | T | T | F | F | F  | Row 15 | T   | **Yes** |
| 14  | T | T | F | T | F  | Row 16 | F   | No      |
| 15  | T | T | T | F | T  | Row 13 | F   | **Yes** |
| 16  | T | T | T | T | F  | Row 14 | F   | No      |
 
**Clause c is active in rows: 5, 7, 13, 15**
 
#### Clause **d** (hasAllergy)
Flip d and check:
 
| Row | a | b | c | d | P  | Flip d (paired row) | P'  | Active? |
|-----|---|---|---|---|----|----------------------|-----|---------|
| 1   | F | F | F | F | T  | Row 2  | T   | No      |
| 2   | F | F | F | T | T  | Row 1  | T   | No      |
| 3   | F | F | T | F | T  | Row 4  | T   | No      |
| 4   | F | F | T | T | T  | Row 3  | T   | No      |
| 5   | F | T | F | F | F  | Row 6  | F   | No      |
| 6   | F | T | F | T | F  | Row 5  | F   | No      |
| 7   | F | T | T | F | T  | Row 8  | F   | **Yes** |
| 8   | F | T | T | T | F  | Row 7  | T   | **Yes** |
| 9   | T | F | F | F | T  | Row 10 | T   | No      |
| 10  | T | F | F | T | T  | Row 9  | T   | No      |
| 11  | T | F | T | F | T  | Row 12 | T   | No      |
| 12  | T | F | T | T | T  | Row 11 | T   | No      |
| 13  | T | T | F | F | F  | Row 14 | F   | No      |
| 14  | T | T | F | T | F  | Row 13 | F   | No      |
| 15  | T | T | T | F | T  | Row 16 | F   | **Yes** |
| 16  | T | T | T | T | F  | Row 15 | T   | **Yes** |
 
**Clause d is active in rows: 7, 8, 15, 16**
 
---
 
## Step 3 – Test Requirements (TRs)
 
### CACC (Correlated Active Clause Coverage)
 
For each clause, we need at least one test pair where:
- The clause is **active**
- The clause is **true** in one test → P is **true**
- The clause is **false** in the other test → P is **false**
(The other clauses can be anything as long as they are the same in both tests.)
 
| Active Clause | TR Pair (rows) | Clause=T → P | Clause=F → P |
|---------------|----------------|--------------|--------------|
| **a**         | —              | —            | — (a is never active, no TR possible) |
| **b** (b=T→P changes, b=F→P changes) | Rows **(5, 1)** | b=T, P=F | b=F, P=T |
| **c**         | Rows **(7, 5)** | c=T, P=T | c=F, P=F |
| **d**         | Rows **(7, 8)** | d=F (→!d=T), P=T | d=T (→!d=F), P=F |
 
> Note: Clause **a** is never active in any row, so no TR exists for it under CACC/RACC. This is acceptable — it means `age > 65` is a **redundant clause** in this predicate.
 
**CACC TRs:**
- TR1: {b=T → P=F} paired with {b=F → P=T} → Rows **5 & 1** (a=F, c=F, d=F; b varies)
- TR2: {c=T → P=T} paired with {c=F → P=F} → Rows **7 & 5** (a=F, b=T, d=F; c varies)
- TR3: {d=F → P=T} paired with {d=T → P=F} → Rows **7 & 8** (a=F, b=T, c=T; d varies)
---
 
### RACC (Restricted Active Clause Coverage)
 
RACC is stricter: when the active clause changes, **all other clauses must be the same** in both tests of the pair. CACC allows other clauses to differ; RACC does not.
 
Looking at our pairs above, they already satisfy RACC (only the active clause changes, everything else is fixed):
 
| Active Clause | Row A | Row B | Difference     |
|---------------|-------|-------|----------------|
| **b**         | Row 1 (a=F,b=F,c=F,d=F) → P=T | Row 5 (a=F,b=T,c=F,d=F) → P=F | Only b changes |
| **c**         | Row 7 (a=F,b=T,c=T,d=F) → P=T | Row 5 (a=F,b=T,c=F,d=F) → P=F | Only c changes |
| **d**         | Row 7 (a=F,b=T,c=T,d=F) → P=T | Row 8 (a=F,b=T,c=T,d=T) → P=F | Only d changes |
 
**RACC TRs:**
 
| TR# | Test pair | Active Clause |
|-----|-----------|---------------|
| TR1 | (a=F, **b=F**, c=F, d=F) & (a=F, **b=T**, c=F, d=F) | **b** |
| TR2 | (a=F, b=T, **c=T**, d=F) & (a=F, b=T, **c=F**, d=F) | **c** |
| TR3 | (a=F, b=T, c=T, **d=F**) & (a=F, b=T, c=T, **d=T**) | **d** |
 
Unique rows needed: **1, 5, 7, 8**
 
---
 
## Step 4 – Concrete Test Values and JUnit 5 Parameterized Tests
 
### Mapping clauses to concrete values
 
| Clause | Condition     | TRUE value         | FALSE value     |
|--------|---------------|---------------------|-----------------|
| a      | age > 65      | age = 70           | age = 30        |
| b      | isHighRisk    | isHighRisk = true  | isHighRisk = false |
| c      | weight < 50   | weight = 40.0      | weight = 70.0   |
| d      | hasAllergy    | hasAllergy = true  | hasAllergy = false |
 
### Test cases for RACC
 
| Test | a (age) | b (isHighRisk) | c (weight) | d (hasAllergy) | Expected P | Expected return |
|------|---------|----------------|------------|-----------------|------------|-----------------|
| T1   | 30      | false          | 70.0       | false           | **true**   | 70.0 * 0.8 = **56.0** |
| T2   | 30      | true           | 70.0       | false           | **false**  | 70.0*1.2 + 30*0.1 = **87.0** |
| T3   | 30      | true           | 40.0       | false           | **true**   | 40.0 * 0.8 = **32.0** |
| T4   | 30      | true           | 40.0       | true            | **false**  | 40.0*1.2 + 30*0.1 = **51.0** |
 
### RACC pair mapping
 
| RACC TR | Test pair | Active clause |
|---------|-----------|---------------|
| TR1     | T1 & T2   | b (isHighRisk) |
| TR2     | T3 & T2   | c (weight)    |
| TR3     | T3 & T4   | d (hasAllergy) |
 
---
 
### JUnit 5 Parameterized Tests
 
```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.assertEquals;
 
public class DosageCalculatorTest {
 
    /**
     * RACC Test Requirements:
     *
     * TR1 (active clause: isHighRisk / b):
     *   T1: age=30, weight=70.0, isHighRisk=false, hasAllergy=false → P=true  → 56.0
     *   T2: age=30, weight=70.0, isHighRisk=true,  hasAllergy=false → P=false → 87.0
     *
     * TR2 (active clause: weight / c):
     *   T3: age=30, weight=40.0, isHighRisk=true,  hasAllergy=false → P=true  → 32.0
     *   T2: age=30, weight=70.0, isHighRisk=true,  hasAllergy=false → P=false → 87.0
     *
     * TR3 (active clause: hasAllergy / d):
     *   T3: age=30, weight=40.0, isHighRisk=true,  hasAllergy=false → P=true  → 32.0
     *   T4: age=30, weight=40.0, isHighRisk=true,  hasAllergy=true  → P=false → 51.0
     */
    @ParameterizedTest(name = "Test {index}: age={0}, weight={1}, isHighRisk={2}, hasAllergy={3} → expected={4}")
    @CsvSource({
        // T1 – TR1 (b active, b=false → P=true)
        "30, 70.0, false, false, 56.0",
        // T2 – TR1 & TR2 (b active, b=true → P=false) / (c active, c=false → P=false)
        "30, 70.0, true,  false, 87.0",
        // T3 – TR2 & TR3 (c active, c=true → P=true) / (d active, d=false → P=true)
        "30, 40.0, true,  false, 32.0",
        // T4 – TR3 (d active, d=true → P=false)
        "30, 40.0, true,  true,  51.0"
    })
    void testCalculateDosage(int age, double weight, boolean isHighRisk, boolean hasAllergy, double expected) {
        double result = DosageCalculator.calculateDosage(age, weight, isHighRisk, hasAllergy);
        assertEquals(expected, result, 0.001);
    }
}
```
 
---
 
## Summary
 
| Coverage | TRs needed | Tests used |
|----------|-----------|------------|
| CACC     | 3 pairs (b, c, d) | T1–T4 |
| RACC     | 3 pairs (b, c, d) — same clauses fixed in each pair | T1–T4 |
 
> **Note:** Clause `a` (`age > 65`) is **never active** in this predicate. Changing `age` alone can never change the outcome of `P`, because `!isHighRisk` (p2) already dominates the result whenever `isHighRisk=false`, and when `isHighRisk=true`, the predicate depends on `c && !d` regardless of `a`. This is a logical redundancy in the method.
 
