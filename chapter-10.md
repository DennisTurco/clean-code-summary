*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Classes and Objects

## 1. Standard Class Structure

The standard class structure of a class in Java follows the **step-down rule**, meaning the code should read like a newspaper article, from high-level concepts to details.

The recommended order is:

1. Public static constants;
2. Private static variables;
3. Private instance variables;
4. Public variables (only if there is a valid reason);
5. Public methods;
6. Private methods;

Following the principle of encapsulation, variables and methods should be kept private whenever possible.

## 2. Designing Small and Focused Classes

The primary rule for classes is that they **should be small**.

Like functions, a class should contain only methods that share a single responsibility.
Avoid placing methods with different purposes or responsabilities in the same class.

Example:

```java
public class GeneralUtility {
    public void insertDataToJson(MyData data)
    public MyData readDataFromJson()
    public void exportResultToTxt()
    public void importResultFromTxt()
    ...
    // many private methods
}
```

`GeneralUtility` has too many responsability.

This class should be split into two separate classes, such as `JsonUtility` and `TxtUtility`.

A class name should clearly describe its **single responsability**.

### 2.1 The Single Responsibility Principle

The **Single Responsibility Principle (SRP)** states that a class or module should have **one, and only one, reason to change**.

This is one of the most important concepts in object-oriented design.

Returning of the `GeneralUtility` example, the class has multiple reasons to change because it handles multiple responsabilities. Therefore, it violates SRP.

Although having many small classes may initially feel inconvenient, it is far easier to understand, modify, and maintain small, focused classes than a single large class containing complex logic.

### 2.2 Class Cohesion

Classes should have a small number of instance variables.

In general:

- The more instance variables a method uses, the more cohesive it is with its class.
- A class is maximally cohesive when every method uses every instance variable.

However, keeping methods small and parameter lists short can somethimes lead to a growt in instance variables that are only used by certain methods.

Whenthis happens, variables and methods should be separated into multiple classes so that each class becomes more cohesive.

### 2.3 Small Classes as a Result of High Cohesion

Breaking large functions into smaller ones often leads to an increase in the number of classes.

The general idea is: **when a class loses cohesion, it should be split**.

Dividing large functions into smaller ones often reveals opportunities to extract new classes. This results in better organization, improved readability, and a more transparent system structure.

## 3. Designing for Change

Change is constant, so systems should be designed to **minimize risk when changes occur**.

Classes that handle multiple responsibilities are more likely to break when modified. Splitting responsibilities into smaller, focused classes reduces complexity, improves clarity, and makes testing easier.

New features should be added by **extending the system rather than modifying existing code**, following the **Single Responsibility Principle** and the **Open–Closed Principle**.

### 3.1 Isolating Code from Change

Requirements evolve, so code should be structured to limit the impact of those changes.

Depending directly on concrete implementations makes systems fragile and difficult to test. By depending on interfaces or abstract concepts instead, implementation details can change without affecting the rest of the system.

This approach improves testability, reduces coupling, increases flexibility and reuse, and follows the **Dependency Inversion Principle (DIP)**:
classes should depend on abstractions, not concrete implementations.
