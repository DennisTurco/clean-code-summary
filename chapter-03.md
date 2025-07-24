*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Designing Focused and Readable Functions
Functions are one of the main things that help you to organize your program.

## Small
As Robert Martin says:
> "*The first rule of functions is that they should be small. The second rule of functions is that they should be smaller than that*".

## Do one thing
A function should perform a single, clearly defined task, which should be evident from its name.
If a function does more than one thing, split it into multiple smaller functions each responsible for just one job.

## Only one level of abstraction
To ensure a function does only one thing, all of its internal statements should operate at the same level of abstraction.
Avoid mixing **high-level logic** (e.g., `getHtml()`) with **low-level details** (e.g., `.append("\n")`) in the same function.
If such a mix exists, refactor the function to maintain a **consistent abstraction level**.

### The stepdown rule
Code should be structured like a **top-down narrative**, where each function introduces and calls functions at the **next level of abstraction**. This approach makes the program easy to read, following a logical **step-down structure**.

Following this rule ensure:
- **Consistent abstraction levels** within functions.
- **Shorter, more focused functions** that do one thing.
- **Improved readability and maintainability**.

Although challenging to master, this technique is essential for writing clean, well-structured code.


## Switch statements

Switch statements are problematic because they:

- Tend to grow large and violate the **Single Responsibility Principle (SRP)**
- Require modifications when new types are added, breaking the **Open/Closed Principle (OCP)**
- Often need to be duplicated across multiple functions, leading to **code repetition**

Solution: Use Polymorphism

Instead of spreading switch statements throughout the code, **encapsulate them in an abstract factory**. The switch should only be used once to instantiate the correct subclass and hidden behind an **inheritance hierarchy**.

- **Keeps switch logic isolated** in one place
- **Allows new types to be added** without modifying existing functions
- **Enables polymorphic behavior**, making the code cleaner and more maintainable

```java
public abstract class Vehicle {
    public abstract void startEngine();
    public abstract void drive();
    public abstract void stopEngine();
}

public interface VehicleFactory {
    public Vehicle makeVehicle(VehicleRecord r) throws InvalidVehicleType;
}

public class VehicleFactoryImpl implements VehicleFactory {
    public Vehicle makeVehicle(VehicleRecord r) throws InvalidVehicleType {
        switch (r.type) {
            case CAR:
                return new Car(r);
            case TRUCK:
                return new Truck(r);
            case MOTORCYCLE:
                return new Motorcycle(r);
            default:
                throw new InvalidVehicleType(r.type);
        }
    }
}
```

## Naming and Function Arguments
* Choose clear, meaningful names so code reads like prose.
* Prefer long, descriptive names over short, cryptic ones.
* Keep functions small and focused, making naming easier.
* Consistency in naming (verbs, nouns) improves readability.
* Refactor names freely — good names often lead to better structure.

*$\rightarrow$ Code should be “pretty much what you expected” just by reading function names*

## Keep Function Arguments Minimal
Ideal number of arguments:
→ 0 (niladic) > 1 (monadic) > 2 (dyadic)
→ Avoid 3+ (triadic/polyadic) unless absolutely necessary.

**Why fewer is better:**
* Reduces cognitive load
* Simplifies testing
* Avoids confusing output arguments, return values instead.

**Best Practices:**
* Aim for no arguments when possible.
* Use one or two related arguments.
* Group multiple values into objects to reduce argument count.

*$\rightarrow$ Clear, minimal arguments make functions easier to read, test, and maintain.*

### Common Monadic Form
Monadic functions (functions with a single argument) generally serve two purposes:

1. **Querying**: Asking a question about the argument.
    - Example: `boolean fileExists("MyFile")` $\rightarrow$ checks if the file exists.
2. **Transformation**: Operating on the argument to transform it into something else.
    - Example: `InputStream fileOpen("MyFile")` $\rightarrow$ transforms a file name into an `InputStream`.

A less common form is using a **single argument as an event** where the argument alters the system’s state without returning a value.

- Example: `void passwordAttemptFailedNtimes(int attempts)` $\rightarrow$ tracks event occurrences.

Best Practices:

- **Clarify intent**: Choose names that clearly indicate whether the function is querying, transforming, or handling an event.
- **Avoid confusing forms**: For example, avoid using output arguments for transformations like `void includeSetupPageInto(StringBuffer pageText)`. Instead, use a return value to indicate the transformation, such as `StringBuffer transform(StringBuffer in)`.

### Flag arguments
Avoid to pass a boolean into a function, because lead the method to become more complex: it's saying that the function is doing multiple things depending of the value of the boolean you are passing.

For example if you read a method call `createUser(true)` you are not able what that true is saying, with the signature of the method `createUser(boolean isMale)` it become more clear but you should refactorize the method by splitting it into 2 pieces: `createMaleUser()` and `createFemaleUser()`.

### Function Arguments: Keep Them Simple
* One argument (**monadic**): is easiest to understand.
Example: `writeField(name)` is clearer than `writeField(outputStream, name)`.
* Two arguments (**dyadic**): are acceptable when natural (e.g., `new Point(0, 0)`) but are harder to read and require care with order and meaning.
* Three arguments (**triadic**): greatly increase complexity and should be avoided unless absolutely necessary.

### Function Arguments and Naming
1. **Use Argument Objects**: If a function needs more than 2–3 arguments, group related ones into a class (e.g., use `Point` instead of separate `x` and `y`).
2. **Limit Argument Count**: Even for functions with variable arguments (e.g., String.format), keep the core arguments to a maximum of 3 for clarity and simplicity.
3. **Name Functions Clearly**: Function names should form clear verb/noun pairs (e.g., `write(name)`) or include argument hints (e.g., `assertExpectedEqualsActual(expected, actual)`) to make the code self-explanatory.

## No side effects
A function should do only one thing, and that task should be clearly reflected by its name.

Bad example:
```java
public class IntCollection {
    List<int> values = new ArrayList<>();

    public boolean isValueMissing(int value) {
        for (int v : values) {
            if (v == value) {
                return false;
            }
        }
        values.add(value); // Side effect!
        return true;
    }
}
```
The function looks like it's just checking if a value is missing — but it also modifies the internal list by adding the value if it's not found.
This side effect breaks the function's single responsibility and makes it misleading.

If it must both check and add, the function should be called `IsValueMissingAndAddingIfNot` instead of `isValueMissing`.

Or better yet, split into two separate functions for clarity and maintainability.

## Command Query Separation
A function should either do something (a command) or return something (a query) — but not both.

Mixing the two can lead to confusion and unpredictable behavior.
A function should clearly act (e.g., `saveToDatabase()`) or answer (e.g., `isSaved()`), but not do both at once.

## Exceptions are better than error codes
Returning error codes from functions violates the **Command Query Separation** principle

*Example: Using error codes*
```java
BackgroundService service = new BackgroundService();
int result = service.startService();

if (result != 0) {
    return E_ERROR;
}
```

*Better: Using exceptions*
```java
try {
    BackgroundService service = new BackgroundService();
    service.startService();
} catch (IOException e) {
    logger.log(e.getMessage());
}
```

This improve readability by handling the error in a single place by:
* Centralizing error handling
* Improving readability and separation of concerns
* Avoiding scattered checks and confusing logic

### Extract try/catch blocks
`try/catch` blocks tend to clutter the main logic, making code harder to read and follow.
To improve clarity, move error handling code into separate functions so the core process remains clean and focused.

*Original (inline error handling):*
```java
public static void exportRemindListToJson() {
    Path desktopPath = Paths.get(System.getProperty("user.home"), "Desktop", remindListFileStr);
    Path sourcePath = Paths.get(sourcePathStr);
    try {
        Files.copy(sourcePath, desktopPath, StandardCopyOption.REPLACE_EXISTING);
    } catch (Exception ex) {
        logger.log(ex.getMessage());
    }
}
```

*Better (extracted error handling):*
```java
public static void exportRemindListToJson() {
    Path desktopPath = Paths.get(System.getProperty("user.home"), "Desktop", remindListFileStr);
    Path sourcePath = Paths.get(sourcePathStr);
    copyFile(sourcePath, desktopPath);
}

private static void copyFile(Path source, Path destination) {
    try {
        Files.copy(source, destination, StandardCopyOption.REPLACE_EXISTING);
    } catch (Exception ex) {
        logError(ex);
    }
}

private static void logError(Exception ex) {
    logger.log(ex.getMessage());
}
```

* Improves readability by keeping the main function focused
* Encourages reuse of error-handling logic
* Simplifies testing and maintenance


### Keep Error Handling Isolated

Since **functions should do one thing**, error handling should be isolated. This means:

1. If a function contains `try`, it should **only** handle errors.
2. The **try/catch** block should be the **first** thing in the function.
3. After the `catch` or `finally`, there should be **nothing else**.


### Avoid Centralized Error Dependencies

Defining error codes in a central class (e.g., `Error.java`) creates a **dependency magnet**, requiring multiple classes to import it. This leads to **recompilation and redeployment** whenever errors change, discouraging updates.

**Better Approach:** Use **exceptions** instead.

- New exceptions can be **derived from a base class** without affecting existing code.
- This **eliminates tight coupling** and makes error handling more maintainable.


## Write It Once, Use It Everywhere

Duplication clutters code and increases maintenance effort $\rightarrow$ a single change might require multiple updates.
Many programming paradigms (like OOP, AOP, and structured programming) exist largely to reduce repetition and centralize logic.

## Write with Clear Control Flow
Dijkstra’s **single-entry, single-exit** rule suggests:

- **Only one return statement per function.**
- **No break or continue in loops.**
- **Never use goto.**

However, **for small functions**, multiple return statements or breaks can **improve clarity** without harming maintainability.


## Crafting Clean Functions

Writing clean code is a **process**, just like writing a paper. Initially, functions may come out **long, complicated, and messy**, with poor naming, duplication, and complex logic. But the key is to **start with something functional**, typically covered by unit tests, and **refine it over time**.

Here's how the process works:

1. **First Draft**: Functions start **clumsy**, with nested loops, long arguments, and arbitrary names. The main goal is to get the logic working.
2. **Refinement**: Gradually, you **split functions**, change names, remove duplication, and reorder logic. The code gets smaller and cleaner.
3. **Unit Tests**: During the entire process, **unit tests** ensure the code stays correct as you refactor and optimize it.
4. **End Result**: After enough refinement, the function follows best practices, such as being **small**, **focused**, and **clear**