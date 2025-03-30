---
layout: post
title:  "My new post"
date:   2025-03-30 15:35:10 +0700
categories: my-post
---

# Good Code Isn't About Length—It's About Depth

Have you ever heard someone say, "Shorter methods are always better"? This is one of those "golden rules" that many new programmers are taught. But is it really the absolute truth?

---

## When Shorter Isn't Better

Imagine reading a book where each story is broken into tiny fragments, scattered across different pages. To understand a complete story, you have to constantly flip back and forth between pages. Exhausting, right?

Code works the same way. When we try to break everything down into super-short methods, we inadvertently create a maze of logic that readers must constantly "jump" through to understand the entire process.

![Fragmented methods = mental jumps](/assets/images/fragmented-methods-diagram.png)

---

## A Real-World Example

Consider a simple login function written in the "shorter is always better" style:

```java
public boolean login(User user) {
    return isValidUser(user) && authenticate(user);
}

private boolean isValidUser(User user) {
    return hasValidUsername(user.username) && hasValidPassword(user.password);
}

private boolean hasValidUsername(String username) {
    return username.length() >= 3;
}

private boolean hasValidPassword(String password) {
    return password.length() >= 8;
}

private boolean authenticate(User user) {
    return checkCredentials(user);
}

private boolean checkCredentials(User user) {
    Connection db = connectDatabase();
    String storedPassword = queryPassword(db, user.username);
    return comparePassword(user.password, storedPassword);
}

private Connection connectDatabase() {
    return DriverManager.getConnection(DB_URL);
}

private String queryPassword(Connection db, String username) throws SQLException {
    PreparedStatement stmt = db.prepareStatement(
        "SELECT password FROM users WHERE username = ?"
    );
    stmt.setString(1, username);
    ResultSet result = stmt.executeQuery();
    if (result.next()) {
        return result.getString("password");
    }
    return null;
}

private boolean comparePassword(String entered, String stored) {
    return entered.equals(stored);
}
```

Looks neat and tidy at first glance, doesn't it? Each method does just one thing, and the method names clearly describe what they do. But try to understand the actual logic:

- You start with `login()`
- Need to check `isValidUser()` and `authenticate()`
- To understand `isValidUser()`, you need to look at `hasValidUsername()` and `hasValidPassword()`
- To understand `authenticate()`, you need to check `checkCredentials()`
- To understand `checkCredentials()`, you need to follow `connectDatabase()`, `queryPassword()`, and `comparePassword()`

You've had to "jump" through **9 different methods** just to understand one simple login process!

---

## Depth: A Better Alternative

John Ousterhout, author of *A Philosophy of Software Design*, posed an interesting question:

> "Is it easier to read several short functions and understand how they work together than it is to read one larger function?"

The answer is often no.

He proposed the concept of **depth** in code—the idea that a method should do enough at the right level of abstraction, rather than being extremely short.

---

### Here's an improved version of the login function:

```java
public boolean login(User user) throws SQLException {
    // Validate input lengths directly
    if (user.username.length() < 3 || user.password.length() < 8) {
        return false;
    }

    // Establish a connection to the database
    Connection db = DriverManager.getConnection(DB_URL);

    // Prepare and execute query to fetch stored password
    PreparedStatement stmt = db.prepareStatement(
        "SELECT password FROM users WHERE username = ?"
    );
    stmt.setString(1, user.username);
    ResultSet result = stmt.executeQuery();

    // Check if user exists and compare passwords
    if (result.next()) {
        String storedPassword = result.getString("password");
        return storedPassword.equals(user.password);
    }

    // Return false if user does not exist
    return false;
}
```

Is this version longer? Yes. But is it easier to understand? Absolutely!

All the steps of the login process are presented clearly, in a logical order, with comments explaining each part. There's no need to jump between methods—the entire story is told in one place.

![Flat vs Deep Code](/assets/images/depth-vs-fragmentation.png)

---

## Benefits of Deep Code

When you write code with proper depth, you gain several benefits:

✅ Reduced fragmentation: No more jumping back and forth between many small methods  
✅ Better comprehension: The entire logic is presented clearly, in order  
✅ Reduced mental load: Readers don't have to keep track of too much context  
✅ Easier debugging: Errors are easier to spot when the entire process is in one place  

---

## Balance is Key

This doesn't mean you should write extremely long methods. Instead, focus on creating methods with the **right depth**:

- Design methods that do the right amount of work at the right level of abstraction  
- Ensure each method tells a complete and meaningful "story"  
- Then try to keep them short enough to be easily read—but not so short that they fragment the logic  

---

## Conclusion

As John Ousterhout said:

> "Depth is more important than length: first make functions deep, then try to make them short enough to be easily read. Don't sacrifice depth for length."

The next time you write code, don't worry too much about whether your methods are short enough. Instead, ask yourself:

**"Does this method tell a complete and meaningful story? Is it at the right level of abstraction?"**

Because good code isn't about length—it's about **depth**.

---

*Did you find this article helpful? Share it with your colleagues and leave a comment about how you apply the principle of "depth" in your own code!*

