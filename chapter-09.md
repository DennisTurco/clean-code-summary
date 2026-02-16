*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Unit Tests

## 1. Test Driven Developement and The Three Laws

![Test Driven Developement](https://developer.ibm.com/developer/default/articles/5-steps-of-test-driven-development/images/tdd-red-green-refactoring-v3.png)

**Test Driven Developement** (TDD) is a software development practice where you write unit tests before writing the production code.

TDD follows three simple laws:

- **Fist Law**: You may not write production code until you have written a failing unit test.
- **Second Law**: You may not write more of a unit test than is sufficient to fail (and not compiling counts as failing).
- **Third Law**: You may not write more production code than is sufficient to pass the currently failing test.

By following these rules, you naturally create many small tests and often achieve very high test coverage.

## 2. Mantein your tests clean

Dirty tests are not better than no tests. In fact, they can be worse.
Tests must evolve along with production code. If they are hard to read or maintain, they will eventually be ignored or deleted.

Test code is not second-class code. It must be treated with the same care as production code.

### 2.1 Tests Enable Change

Clean tests give you confidence to change the system. The higher the test coverage and quality, the less fear you have when refactoring.
If tests become messy, the production code will follow. Eventually, both will decay.

## 3. Clean Tests

The most important quality of a clean test is **readability**.

To achieve readability, focus on:

1. Clarity: The test's intent should be obvious.
2. Simplicity: Remove unnecessary details.
3. Density of expression: Say more with less code.

Follow this example:

```java
public class TimeRangeTest {

    private final LocalTime start = LocalTime.of(10, 0);
    private final LocalTime end   = LocalTime.of(12, 0);
    private final TimeRange range = new TimeRange(start, end);

    @Test
    public void constructor_shouldThrowException_whenEndIsBeforeStart() {
        LocalTime invalidEnd = start.minusHours(1);

        IllegalArgumentException ex = assertThrows(
                IllegalArgumentException.class,
                () -> new TimeRange(start, invalidEnd)
        );

        assertEquals("End time must be after start time", ex.getMessage());
    }

    @Test
    public void factoryMethod_shouldCreateEquivalentInstance() {
        TimeRange other = TimeRange.of(start, end);
        assertEquals(range, other);
    }

    // ...

    @Test
    public void containsExclusive_shouldExcludeStartAndEnd() {
        assertFalse(range.containsExclusive(start));
        assertFalse(range.containsExclusive(end));
    }

    // ...

    @Test
    public void overlaps_shouldReturnTrue_whenRangesOverlap() {
        TimeRange other = new TimeRange(
                start.plusHours(1),
                end.plusHours(1)
        );

        assertTrue(range.overlaps(other));
    }

    // ...
}
```

This test is easy to read:

- It clearly describes the scenario.
- It sets up only what is necessary.
- It checks a single behavior.

### 3.1 BUILD–OPERATE–CHECK Pattern

Each test should follow three steps:

1. **Build**: Prepare the test data.
2. **Operate**: Execute the action being tested.
3. **Check**: Verify the result.

### 3.2 One Assert Per Test - One Concept

Ideally, each test should verify **one concept**.

Having a single assertion per test often helps, but the real goal is testing one behavior. Multiple assertions are acceptable if they support the same idea.

## 5. F.I.R.S.T. Rules

Clean test follow five rules:

1. **Fast**: Tests must run quickly. Slow tests discourage frequent execution.
2. **Independent**: Tests should not depend on one another. They must run in any order.
3. **Repeatable**: Tests should produce the same results in any environment.
4. **Self-alidating**: Tests should clearly pass or fail. No manual verification.
5. **Timely**: Write tests before (or alongside) production code. Otherwise, the code may become hard to test.

## 6. Conclusion

Tests are essential to the health of a project. They protect flexibility, maintainability, and reusability.
When tests rot, code rots.
**Keep your tests clean**.
