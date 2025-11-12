*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# Error Handling

Although we aim to write code that never fails, error handling is sometimes a must-have. When needed, it should be as clean and explicit as possible.

## 1. Exceptions Instead of Returns Codes

Beginners (and sometimes even experienced programmers) often try to handle errors by returning special codes or just logging an error. This makes the code harder to use and easier to misuse. for example:

```java
class Rational {
    private final float numerator;
    private final float denominator;

    public Rational(float numerator, float denominator) {
        if (denominator == 0) {
            logger.log("Unable to create a rational value with 0 as denominator");
            this.numerator = 0;
            this.denominator = 1; // fallback
            return; // silently returns "invalid" object
        }

        this.numerator = numerator;
        this.denominator = denominator;
    }
}
```

This does not make sense $\rightarrow$ The calling code won't know whether the object is valid or not.

We should refactorize the previous code like:

```java
class Rational {
    private final float numerator;
    private final float denominator;

    public Rational(float numerator, float denominator) {
        if (denominator == 0) {
            throw new IllegalArgumentException("Denominator cannot be zero");
        }
        this.numerator = numerator;
        this.denominator = denominator;
    }
}
```

and now the caller could handle the error properly:

```java
try {
    Rational r = new Rational(1, 0);
} catch (IllegalArgumentException e) {
    logger.log("Failed to create rational: " + e.getMessage());
}
```

## 2. Checked vs Unchecked Exceptions

In Java there are 2 big families of exceptions:

1. **Checked Exceptions** (extends `Exception`, but not `RuntimeExceptions`).
In this case Java force you to 2 possible options:
   * Catch them with a `try-catch`.
   * Declare them in the method with `throws`.
1. **Unchecked Exceptions** (extends `RuntimeExceptions`)
   * The compiler lets your code compile even if you don't handle them.
   * But they can still occur at runtime (program will crash if not handled).

But **you should use Unchecked Exceptions where possible**. Because the main issue is that checked exceptions violate the [Open/Clodes Principle](https://www.geeksforgeeks.org/java/open-closed-principle-in-java-with-examples/). If a low-level method adds a new checked exception, all higher-level method in the call chain must change their signatures, creating a cascade of unnecessary notifications and breaking incapsulation.

![image](./imgs/errors1.png.png)

**Checked Exception**:

```java
import java.io.*;

class FileReaderChecked {
    public void readFile(String path) throws IOException {
        FileReader file = new FileReader(path);
        file.read();
        file.close();
    }
}

public class Main {
    public static void main(String[] args) {
        FileReaderChecked reader = new FileReaderChecked();
        try {
            reader.readFile("test.txt");
        } catch (IOException e) {
            System.out.println("Error reading file: " + e.getMessage());
        }
    }
}

```

**Unchecked Exception**:

```java
class FileReaderUnchecked {
    public void readFile(String path) {
        if (path == null) {
            throw new IllegalArgumentException("Path cannot be null");
        }
        // imagine reading file here
    }
}

public class Main {
    public static void main(String[] args) {
        FileReaderUnchecked reader = new FileReaderUnchecked();
        reader.readFile(null); // throws unchecked exception at runtime
    }
}

```

## 3. Context With Exceptions

Always provide informative error messages when throwing exceptions. Include not just a brief message, but also context such as the operation being performed, the values involved, or the type of failure. This makes debugging and logging much easier.

## 4. Define Exception Classes

When designing exception classes, focus on how they will be caught and handled, not on their source or type.
Poor exception classification leads to repetitive and cluttered code, as shown when multiple exceptions from a third-party library require nearly
identical handling.

A better approach is to wrap third-party APIs and translate their exceptions into a single, meaningful exception type that suits your application's needs. For example:

  Let’s say your app fetches data from an external web API.
  The API’s SDK throws many low-level exceptions, but from your application’s perspective, they all mean one thing: “Data fetch failed.”
  Instead of catching every possible SDK exception separately, you can wrap them in a single, meaningful exception.

* Without Wrapping $\rightarrow$ Messy code

    ```java
    try {
        ExternalApiClient client = new ExternalApiClient("https://example.com");
        String data = client.getData();
        processData(data);
    } catch (TimeoutException e) {
        logError("Request timed out", e);
        showErrorToUser("Network error, please try again later.");
    } catch (InvalidResponseException e) {
        logError("Invalid response from server", e);
        showErrorToUser("Server sent bad data.");
    } catch (AuthenticationFailedException e) {
        logError("Authentication failed", e);
        showErrorToUser("Please log in again.");
    }
    ```

* With Wrapping $\rightarrow$ Cleaner design

    1. Create a wrapper class for the API

        ```java
        public class DataService {
            private ExternalApiClient client;

            public DataService(String endpoint) {
                client = new ExternalApiClient(endpoint);
            }

            public String fetchData() {
                try {
                    return client.getData();
                } catch (TimeoutException | InvalidResponseException | AuthenticationFailedException e) {
                    throw new DataFetchException("Failed to fetch data", e);
                }
            }
        }
        ```

    2. Define a unified exception

        ```java
        public class DataFetchException extends RuntimeException {
            public DataFetchException(String message, Throwable cause) {
                super(message, cause);
            }
        }
        ```

    3. Handle it at a higher level

        ```java
        DataService service = new DataService("https://example.com");

        try {
            String data = service.fetchData();
            processData(data);
        } catch (DataFetchException e) {
            logError(e.getMessage(), e);
            showErrorToUser("Unable to retrieve data. Please try again later.");
        }
        ```

Benefits of wrapping APIs:

1. Minimizes dependency on third-party libraries, making it easier to replace them later.
2. Simplifies testing
3. Allows you to design cleaner, more consistent exception hierarchies.

## 5. Define the Normal Flow

Usually, aborting on exceptions is fine, but sometimes you don't want to interrupt the normal processing.

For example, you might throw an exception if a customer isn't found in the database.
But that forces every caller to deal with an exception, cluttering your code.
Instead, we'll use the **Special Case Pattern**: return a default customer object that represents "no customer found."

Without the Special Case Pattern

```java
try {
    Customer customer = customerRepo.findById(customerId);
    totalPrice -= customer.getDiscount();
} catch (CustomerNotFoundException e) {
    // Handle the "no customer" case here
    totalPrice -= 0;  // no discount
}

```

With the Special Case Petter

```java
// Regular Customer class
public class Customer {
    public double getDiscount() {
        return 10.0; // base discount
    }
}

// Special case: when customer is not found
public class NullCustomer extends Customer {
    @Override
    public double getDiscount() {
        return 0.0; // no discount
    }
}

// Repository returns either a real or a special-case customer
public class CustomerRepository {
    public Customer findById(String id) {
        Customer customer = databaseLookup(id);
        return (customer != null) ? customer : new NullCustomer();
    }

    private Customer databaseLookup(String id) {
        // pretend database lookup here
        return null; // simulate "not found"
    }
}
```

Now your main code is clean and free of `try-catch` blocks:

```java
Customer customer = customerRepo.findById("123");
totalPrice -= customer.getDiscount();
```

This approach is known as the **Special Case Pattern** (from Martin Fowler).
It involves creating a class or objects that represents a default or "normal" version of a special situation.

Benefits:

* Keeps business logic simple and free from exception clutter
* Encapsulates exceptional or missing conditions inside specialized objects
* Allows code to flow naturally without extra `try-catch` blocks

## 6. Don't Return Null

It happens a lot of times that many lines of code are used to check if a value is `null`. This happens because programmers often return `null` from a function. For example:

```java
public void printAllLines() {
    List<String> lines = readAndGetContentFromFile("filepath");
    if (lines != null) {
        for (String line : lines) {
            System.out.println(line);
        }
    }
}
```

But in this case we should refactor it by throwing an exception or returning a special case object instead. Or simply we could simply, we could return an empty list. In this way we can skip the check:

```java
public void printAllLines() {
    List<String> lines = readAndGetContentFromFile("filepath");
    for (String line : lines) {
        System.out.println(line);
    }
}
```

## 7. Don't Pass Null

It is dangerous to pass `null` values to a method in most cases, unless the method explicitly expects it $\rightarrow$ for example, when working on a public API and its description states that it can accept `null`.

Otherwise if we pass `null` to a method where we shouldn't, like:

```java
processOrder(null);
```

We get a `NullPointerException`.

The best thing to do is forbid passing `null` by default.
