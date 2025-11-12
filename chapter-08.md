*This chapter is a summary based on “Clean Code” by Robert C. Martin. All rights reserved by the original author.*

# External Code

We could use internally external code, but we always try to integrate it with our existing code.

## 1. Using Third-Party Code Wisely

There's a natural **tension between library designers and application developers**.
Third-party libraries aim to be general-purpose and flexible, while your application usually needs a focused, limited interface that fits
its specific business rules.
If you use a third-party interface directly throughout your system, this mismatch can create confusion, errors, and tight coupling to the external library.

* Problem Example $\rightarrow$ Using a Broad Interface:

    Imagine you're building a game and use a third-party `List` to store player objects:

    ```java
    List players = new ArrayList();
    ```

    Everywhere in your code, you have to do:

    ```java
    Player p = new (Player) players.get(i);
    ```

    Anyone with access to the list can also call `players.clear()` or `players.add("NotAPlayer")`, potentially breaking the logic.
    Even if you switch to generics (`List<Player>`), the interface still expose too many methods that your code should't use.

* Better Solution $\rightarrow$ Wrap the Third-Party Interface

    Instead of passing `List<Player>` everywhere, create a focused class that encapsulates it:

    ```java
    public class PlayerRoster {
    private List<Player> players = new ArrayList<>();

    public void addPlayer(Player p) {
        players.add(p);
    }

    public Player getByIndex(int i) {
        return players.get(i);
    }

    // No removeAll(), no add(Object) — only what your app needs
    }
    ```

    Now your code uses:

    ```java
    PlayerRoster roster = new PlayerRoster();
    roster.addPlayer(new Player("Alice"));
    Player p = roster.getByIndex(0);
    ```

    this hides the third-party list and prevents misuse, while also allowing you to replace or modify the underlying implementation later (for example, switching from `ArrayList` to a database or an API call), **without changing the rest of your system**.

## 2. Exploring Third-Part Libraries

Using third-party libraries can save time, but is often difficult to understand and integrate.
Writing *learning tests* (small experiments that verify how the library behaves) helps developers explore and confirm their understanding before using it in production code.

## 3. Perform Learning Tests

Learning tests are quick, low-cost experiment that help developers understand and verify third-party APIs. They double as safety checks, when the library updates, these tests reveal any behavior changes. This keeps integrations stable and reduces upgrade risks.

## 4. Placeholder Interface

When a subsystem or API doesn't yet exist, define a placeholder interface you need. This keeps your code
clean, testable and independent, allowing you to connect to the real implementation later via adapter.

## 5. Clean Boundaries

Boundaries in software are where change is most likely. To protect your code and reduce future maintenance:

* Keep clear separation between your code and third-party or external code.
* Minimize direct dependencies on external interfaces.
* Use wrappers or adapters to translate between your clean controlled interface and the external code.
