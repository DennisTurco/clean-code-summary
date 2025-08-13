*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Comments
* Well-placed comments can be helpful.
* Poorly placed or unnecessary comments can be harmful.

We use comments when the code doesn't clearly express its intent.
However, the best code is so clean that **no comments are needed at all**.

If you feel the need to write a comment, it's often a sign that your code isn't as clear as it should be.

## 1. Comments That Attempt to Clean Dirty Code
Comments are often written next to confusing or messy code.
But instead of using comments to **explain unclear code**, it's better to **clean the code itself**.

## 2. Explain Yourself in Code
instead of writing a comment like this:
```java
// check the backup has not already been saved
if(!backup.isAutoBackup() || backup.getNextDateBackup() == null || backup.getTimeIntervalBackup() == null)
```

You should refactor the code like:
```java
if (!isBackupSaved(backup))
```

The second version is much clearer and easies to read.
Whenever possible, let the code **speak for itself**.

## 3. When Comments Are Necessary
Sometimes comments are genuinely useful, but **only when there's no better alternative**.
Still prefer clean code over comments when you can.

### 3.1 Legal Comments
In some cases, comments are required for legal or licensing reasons, for example:
```java
/*
 * Copyright (c) 2025 John Doe.
 * This code is licensed under the MIT License.
 */
```
Where possible, consider moving legal comments to a separate file.

### 3.2 Informative Comments
Informative comments can clarify constants or configuration details:

```java
// Regex pattern for validating email addresses
private static final String EMAIL_REGEX = "^[a-zA-Z0-9_+&*-]+(?:\\.[a-zA-Z0-9_+&*-]+)*@" +
                                          "(?:[a-zA-Z0-9-]+\\.)+[a-zA-Z]{2,7}$";
```

### 3.3 Explanaining Intent
Use a comment when you need to explain why something is done, not what is done.

```java
// We subtract 1 because the list is zero-indexed
int lastItem = items.get(count - 1);
```

### 3.4 Clarification
se comments to clarify code that can't easily be rewritten or simplified:
```java
// Magic number 42 is the meaning of life in this context
processInput(42);
```

### 3.5 Warning of Consequences
Warn other developers about possible side effects or caveats:

```java
// WARNING: This method is not thread-safe
public void updateConfig() {
    // ...
}
```

But in many modern environments, it's better to use annotations like `@Deprecated` or `@ThreadUnsafe` if supported.

### 3.6 TODO
`// TODO` comments are acceptable and useful to mark unfinished or temporary code:

```java
// TODO: refactor this method to improve performance
```

Most IDEs will highlight these for future attention.


### 3.7 Amplification
Use a comment to emphasize the importance of a specific piece of logic:

```java
// This check prevents data corruption in case of duplicate IDs
if (database.contains(id)) {
    throw new IllegalStateException("Duplicate ID detected");
}
```

### 3.8 Javadocs for APIs
If you're writing a `public API`, you should always include proper Javadoc documentation:

```java
/**
 * Calculates the area of a circle.
 *
 * @param radius the radius of the circle
 * @return the area
 */
public double calculateArea(double radius) {
    return Math.PI * radius * radius;
}
```

## 4. Bad comments

### 4.1 Mumbling
If you decide to write a comment, **make it the best you can**. A poorly written comment can be worse than no comment at all, as it may mislead or confuse the reader, forcing them to read the entire block of code to understand what's going on.

### 4.2 Redundant Comments
Avoid comments that state the obvious or merely repeat what the code already expresses:
```java
// return true if it is a valid email, false otherwise
public static boolean isValidEmail(String email) {
    if (email == null) {
        return false;
    }
    Matcher matcher = EMAIL_PATTERN.matcher(email);
    return matcher.matches();
}
```

This comment is unnecessary $\rightarrow$ the method name and code already make the purpose clear.

### 4.3 Misleading Comments
Misleading comments are worse than no comments. They give a false idea of what the code does and confuse readers.
$\rightarrow$ Always make sure your comments are accurate and stay up-to-date with the code.

### Noise Comments
These are comments that add no meaningful value and state the obvious. For example:

```java
public class LimitDocument extends PlainDocument {
    private int limit;

    // constructor
    public LimitDocument(int limit) {
        this.limit = limit;
    }
}
```

The comment `// constructor` tells us nothing we didn't already know. It's just noise and should be removed.

### 4.4 No Comment Instead of a Function or a Variable
Don't use comments to explain what a piece of code does **when a well-named variable or method would be clearer**.

```java
// we are getting a Path object to check if both files exist
if (!Files.exists(Path.of(path1)) || !Files.exists(Path.of(path2))) {
    setError(ErrorTypes.InputError, trayIcon, backupName);
    return false;
}
```

Write this:

```java
Path sourcePath = Path.of(path1);
Path destinationPath = Path.of(path2);

if (!Files.exists(sourcePath) || !Files.exists(destinationPath)) {
    setError(ErrorTypes.InputError, trayIcon, backupName);
    return false;
}
```

This version is much easier to read and doesn't need a comment.


### 4.5 Position Makers
Some programmers try to "organize" blocks of code or functions inside a class by creating banner-style comments, for example:

```java
// Exporters ###########################################
```

or:
```java
// Exporters ///////////////////////////////////////////
```

or:
```java
//////////////////////// Exporters
```

…and so on.

These markers don't actually improve organization $\rightarrow$ they only create noise. A well-structured class with clear function names should make the organization obvious without such banners. Remove them.

### 4.6 Closing Brace Comments
Some developers add comments after closing braces in an attempt to make long functions more readable, like this:

```java
public static List<Backup> getBackupList() {
    try {
        List<Backup> backups = JSONBackup.readBackupListFromJSON();
        BackupManagerGUI.backups = backups;
        return backups;
    } // try
    catch (IOException e) {
        ExceptionManager.openExceptionMessage(e.getMessage(), Arrays.toString(e.getStackTrace()));
    } // catch
    catch (Exception e) {
        logger.error("An error occurred: " + e.getMessage(), e);
    } // catch

    return null;
}
```

Instead of relying on these comments, make the code itself clearer. Break large functions into smaller ones or reduce their complexity so the flow is easy to follow without needing `// try` or `// catch`.

### 4.7 Commented-out Code

Example:
```java
// if (value == 100) {
//     RunningBackups.updateBackupToJSON(new RunningBackups(context.backup.getBackupName(), path2, value, BackupStatusEnum.Finished));
// } else {
//     RunningBackups.updateBackupToJSON(new RunningBackups(context.backup.getBackupName(), path2, value, BackupStatusEnum.Progress));
// }

if (value == 100) {
    updateAfterBackup(path1, path2, context);
    deleteOldBackupsIfNecessary(context.backup.getMaxBackupsToKeep(), path2);
}
```
Commented-out code is one of the worst things you can leave in a codebase. It causes multiple problems:
- Another programmer might assume it’s there for a reason and leave it untouched.
- Others may ignore it entirely, so it just becomes dead clutter.
- Over time, nobody remembers why it’s there.

If you need to disable a block of code temporarily, delete it. Your version control system (e.g., Git) remembers every change, you can always bring it back.

### 4.8 Function Headers
Short, well-named functions do not need extra descriptive headers. The function name itself should be clear enough to describe its purpose.

### 4.9 Javadocs in Nonpublic Code

Javadocs are valuable for public APIs, where they help external users understand your code.
But in non-public code, they are often just clutter. Good naming and clean structure are far more effective.