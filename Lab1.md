# ISP_Test_Report_Lab1
This project applies Input Space Partitioning (ISP) and Base Choice Coverage (BCC) to test a Java method that finds common elements between two lists ignoring case sensitivity. The approach ensures systematic, complete, and minimal test coverage.

I used ISP to divide inputs into simple categories like null, empty, and normal cases. Then I used Base Choice Coverage to create 7 tests. Each test changes only one condition, so coverage is systematic but minimal.

# ISP Testing – findCommonIgnoreCase

## Method

```java
public static List<String> findCommonIgnoreCase(List<String> list1, List<String> list2)
````

This method returns common strings from both lists (case-insensitive).
It keeps the original format from `list1` and removes duplicates.

---
| Test case        | C1 | C2 | C3 | C4 | Result                |
|------------------|----|----|----|----|------------------------|
| T1 – base        | 3  | 3  | 2  | 1  | ["Banana", "Cherry"]  |
| (3, 3, 2, 1)     |    |    |    |    |                        |
| T2 – list1 empty | 2  | 3  | 2  | 1  | []                     |
| (2, 3, 2, 1)     |    |    |    |    |                        |
| T3 – list1 null  | 1  | 3  | –  | –  | exception              |
| (1, 3, -, -)     |    |    |    |    |                        |
| T4 – list2 empty | 3  | 2  | 2  | 1  | []                     |
| (3, 2, 2, 1)     |    |    |    |    |                        |
| T5 – list2 null  | 3  | 1  | –  | –  | exception              |
| (3, 1, -, -)     |    |    |    |    |                        |
| T6 – no match    | 3  | 3  | 1  | 1  | []                     |
| (3, 3, 1, 1)     |    |    |    |    |                        |
| T7 – duplicates  | 3  | 3  | 2  | 2  | ["Apple"]              |
| (3, 3, 2, 2)     |    |    |    |    |                        |


## 1. Characteristics

We divide the input into simple categories:

### C1 – list1

* null
* empty
* non-empty

### C2 – list2

* null
* empty
* non-empty

### C3 – Matching

* no match
* has match

### C4 – Duplicates in list1

* no duplicates
* has duplicates

---

## 2. (a) Disjoint

Each category is separate.

Example:

* list cannot be both null and empty
* matching is either yes or no

So, partitions are **disjoint**.

---

## 3. (b) Complete

We covered:

* null inputs
* empty lists
* normal cases
* matching / no matching
* duplicates

So, all cases are included → **complete**.

---

## 4. (c) Base Choice Coverage

### Base Test

```java
list1 = ["Apple", "Banana", "Cherry"]
list2 = ["banana", "cherry"]

Result = ["Banana", "Cherry"]
```

---

### Tests

1. Base case
2. list1 empty → []
3. list1 null → exception
4. list2 empty → []
5. list2 null → exception
6. no match → []
7. duplicates → only one result

Total: **7 tests**

---

## 5. (d) JUnit Tests

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import java.util.*;

public class FindCommonIgnoreCaseTest {

    @Test
    void baseTest() {
        List<String> list1 = Arrays.asList("Apple", "Banana", "Cherry");
        List<String> list2 = Arrays.asList("banana", "cherry");
        assertEquals(Arrays.asList("Banana", "Cherry"),
            YourClass.findCommonIgnoreCase(list1, list2));
    }

    @Test
    void list1Empty() {
        assertTrue(YourClass.findCommonIgnoreCase(
            new ArrayList<>(), Arrays.asList("banana")).isEmpty());
    }

    @Test
    void list1Null() {
        assertThrows(NullPointerException.class, () ->
            YourClass.findCommonIgnoreCase(null, Arrays.asList("banana")));
    }

    @Test
    void list2Empty() {
        assertTrue(YourClass.findCommonIgnoreCase(
            Arrays.asList("Apple"), new ArrayList<>()).isEmpty());
    }

    @Test
    void list2Null() {
        assertThrows(NullPointerException.class, () ->
            YourClass.findCommonIgnoreCase(Arrays.asList("Apple"), null));
    }

    @Test
    void noMatch() {
        assertTrue(YourClass.findCommonIgnoreCase(
            Arrays.asList("Apple"), Arrays.asList("Durian")).isEmpty());
    }

    @Test
    void duplicates() {
        assertEquals(Arrays.asList("Apple"),
            YourClass.findCommonIgnoreCase(
                Arrays.asList("Apple", "apple"),
                Arrays.asList("apple")));
    }
}
```

---

## Conclusion

* We used ISP to divide inputs
* We used BCC to create tests
* Only 7 tests cover all important cases

```
