*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Formatting

Readable, well formatted code is easier to understand, maintain and extend. Formatting is about communication, not personal taste or rigid dogma. A consistent style makes codebase easier for everyone to work with.

## 1. The Purse of Formatting

Formatting sets exceptions for readability and maintainability. Even after code evolves and changes, the formatting patterns you chose will influence how future developers navigate and modify it.

## 2. Vertical Formatting

How big should a ﬁle be?
In Java, ﬁle size corresponds to class size. Small files are generally easier to understand than large ones.

### 2.1 The Newspaper Metaphor

A good source file should read like a newspaper:

* **Top**: High level concepts and purpose (the "headline").
* **Mid**: Supporting details and explanations.
* **Bottom**: Low level implementation details.

### 2.2 Vertical Openness Between Concepts

Separate distinct concepts with **blank lines**. Just as paragraphs break up ideas in prose, whitespace makes wide easier to read.

Good:

```java
public void saveUser(User user) {
    validateUser(user);

    userRepository.save(user);
}

public boolean userExists(String email) {
    return userRepository.findByEmail(email) != null;
}
```

Bad:

```java
public void saveUser(User user) {
    validateUser(user);
    userRepository.save(user);
}
public boolean userExists(String email) {
    return userRepository.findByEmail(email) != null;
}
```

The blank line between the two functions gives the eye a natural pause, making it easier to spot where one function ends and the next begins.

### 2.3 Vertical Density

Lines of code that are closely related should appear close together without unnecessary blank lines.
Openness signals separation; density signals tight coupling.

### 2.4 Vertical Distance

Keep related concepts vertically close. Avoid forcing readers to jump around files unnecessarily.

* **Variable Declarations**: Declare local variables as near as possible to their usage. Loop counters should be declared inside the loop statement.
* **Instance Variables**: Place them at the top of the class; they're usually used by multiple methods.
* **Conceptual Affinity**: Keep related functions or logic together, whether because of direct dependence or similarity of purpose.

### 2.5 Vertical Ordering

Arrange functions so that higher-level functions appear above the lower-level ones they depend on. This creates a logic top-down flow.

## 3. Horizontal Formatting

Avoid overly long lines. While modern monitors are wide, extremely long lines reduce readability. Aim for concise statements that fit comfortably without excessive horizontal scrolling.

### 3. Horizontal Openness and Density

Use spaces to visually group related elements and separate unrelated ones.
Example:

```java
sum   = price + tax;     // related tightly
total = sum - discount;  // separate calculation
```

Avoid this:

```java
sum=price+tax;
total=sum-discount;
```

### 3.1 Horizontal Alignment

Lining up variable names and types vertically often adds noise rather than clarity:

```java
protected long            requestTimeLimit;
private   long            requestProgress;
private   boolean         hasError;
```

It may look neat, but it emphasizes column alignment over meaning. Prefer consistent spacing without forced alignment.

### 3.2 Indentation

Indentation visually represents scope hierarchy. Without it, code becomes unreadable.

Bad:

```java
if (isValid) {
process();
}
```

Good:

```java
if (isValid) {
    process();
}
```

Avoid breaking indentation just to save space, such as:

```java
public String render() throws Exception {return ""; }
```

Even short statements benefit from proper formatting.

## 4. Team Rules

Personal preferences don't matter if you're working in a team $\rightarrow$ the team's agreed style is what counts. A consistent format across the codebase reduces friction, makes reviews easier, and improves collaboration.
