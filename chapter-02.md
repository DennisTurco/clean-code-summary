*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Writing Clear and Descriptive Names
Naming matters. Clear, intentional names make code easier to read, understand, and maintain.

## Names should revealing their intentions
Names should say:
* What something is,
* What it does,
* How it's used.

If you need a comment to explain it, the name isn't good enough.

*for example*:
```java
int n; // array dimension
```

> 🚩 **Personal note:** As a teacher, I've often noticed students writing things like `int integer` or `int[] array`. At first, it may seem better than `int n`, but in reality it's worse because it still tells you nothing useful. It's like having a baby and naming them "human". WTF dude! you can do better than that.

instead of doing it you should use more meaningful names like:
```java
int arrayDimension;
boolean isActive;
double paidAmount;
```

Now let's take a look Let's look at a poorly written function:
```java
public List<int> obtainCorrectValues(List<int> list1) {
    List<int> list2 = new List<>();
    for (int l : list1) {
        if (l % 2 == 0) {
            list2.add(l);
        }
    }
    return list2;
}
```
Even though it's simple, it takes longer than it should to understand. Ask yourself:
* What does `obtainCorrectValues` mean? What does "correct" refer to?
* What are `list1` and `list2` supposed to represent?
* What kind of list is returned?

Now, here's a clearer version:
```java
public List<int> getEvenFromList(List<int> values) {
    List<int> even = new List<>();
    for (int value : values) {
        if (value % 2 == 0) {
            even.add(value);
        }
    }
    return even;
}
```
Just by using better names, we make the code self-explanatory. Clean code often starts with clear names.

## Avoid confusion
Don't use unclear or misleading names:
* Too short: `ds`, `tmp`
* Easily confused: `O`, `0`, `l`, `1`
```java
int v = 0;
int c = O;
int O1 = 1;
if (c == 0) {
    O1 = c;
}
```
In this example:
* Is `O` the letter "O" or the number zero?
* Is `O1` supposed to be "O-one" or "zero-one"?

The more readable your code is, the fewer errors you're likely to introduce. Always prefer clarity over cleverness.

## Pronounceable names
Follow a simple rule: "If you can't pronunce it, don't use it".

Compare:
```java
class Rcrd2 {
	private Date insertymdhms;
	private Date updateymdhms;
	private final String rcrdId = "2";
	/* ... */
};
```

To:
```java
class Person {
	private Date insertTimestamp;
	private Date lastUpdateTimestamp;;
	private final String recordId = "2";
	/* ... */
};
```

## Searchable names are better
Use descriptive variable names like `maxToKeep` or `MAX_WIDTH` instead of hardcoded numbers. This makes the code easier to search, read, and understand.

## Some general name convenctions
* **Interfaces and Implementations**: Avoid prefixes like I in interface names (e.g., `IBetRepository`). Instead, name the interface `BetRepository` and the implementation something like `DefaultBetRepository` or `SqlBetRepository`.
* **Class Names**: Use noun phrases, not verbs. Good examples: `Person`, `User`. Avoid vague or process like names such as `Manager` or `Processor`.
* **Method Names**: Use verbs or verb phrases that clearly describe the action, like `insertNewUser()` or `getNewValue()`.

## Naming Best Practices
* Avoid cute or funny names: Use clear, descriptive names (`DeleteItems`, not `HolyHandGrenade`).
* Stick to one word per concept: Don't mix terms like fetch, get, and retrieve—pick one and be consistent.
* Avoid name overloading (puns): Use distinct names for different actions (`insert` vs. `add`) to reduce ambiguity.
* Use domain appropriate terms:
* Use solution domain names (e.g. `JobQueue`, `Visitor`) for technical concepts.
* Use problem domain names when representing real world entities or logic.


## Adding and Avoiding Context in Names
* **Add Meaningful Context**: Use well named classes or structures (e.g., Address, GuessStatisticsMessage) to give variables clear context, instead of adding confusing prefixes.
* **Avoid Gratuitous Context**: Don't overuse prefixes like GSD or Account if they add no real clarity, they make code harder to read and navigate.

## Final Note
Naming is a skill that improves with practice. Don't fear renaming for clarity, it helps code read more like natural language, making it easier to understand and maintain. Use refactoring tools confidently to support this.