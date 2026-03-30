# a) Graph for the function (CFG)

**Nodes:**

* Start
* Loop condition: `i < temps.length`
* `temp = temps[i]`
* Decision 1: `temp < -50 OR temp > 60`
* Decision 2: `temp >= 20`
* `warmDays++`
* Loop end
* Final decisions:

  * `hasInvalid`
  * `all warm`
  * `no warm`
  * `some warm`
* End

**Edges:**

* Start → Loop
* Loop (true) → temp assignment
* temp → invalid check
* invalid (true) → hasInvalid = true → loop
* invalid (false) → warm check
* warm (true) → warmDays++ → loop
* warm (false) → loop
* Loop (false) → final checks
* hasInvalid → “Invalid temperatures detected”
* all warm → “All days were warm”
* no warm → “No warm days”
* else → “Some days were warm”

---

# b) DU-Paths (temps)

**Definition:** `temps` parameter

**Uses:**

* loop condition (`temps.length`)
* indexing (`temps[i]`)

**DU-paths:**

1. `temps → loop condition`
2. `temps → temp assignment`
3. `temps → invalid check`
4. `temps → warm condition`

✔ All DU-paths are loop-redefined (due to iteration)

---

# c) Prime Path Coverage (Minimal Test Set)

```
T1: []
T2: [25, 22, 21]      → All warm
T3: [10, 15, 18]      → No warm
T4: [25, -60, 30]     → Invalid
T5: [10, 25, 30]      → Mixed
```

---

# d) Edge-Pair Coverage (Minimal Test Set)

```
E1: []
E2: [25]
E3: [10]
E4: [25, 22]
E5: [10, 25]
E6: [25, -60]
```

---

# e) JUnit Tests (from minimal sets)

```java
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class TempTest {

    @Test
    void testAllWarm() {
        assertEquals("All days were warm.",
            analyzeTemperatures(new int[]{25, 22, 21}));
    }

    @Test
    void testNoWarm() {
        assertEquals("No warm days.",
            analyzeTemperatures(new int[]{10, 15, 18}));
    }

    @Test
    void testInvalid() {
        assertEquals("Invalid temperatures detected.",
            analyzeTemperatures(new int[]{25, -60, 30}));
    }

    @Test
    void testMixed() {
        assertEquals("Some days were warm.",
            analyzeTemperatures(new int[]{10, 25, 30}));
    }
}
```

