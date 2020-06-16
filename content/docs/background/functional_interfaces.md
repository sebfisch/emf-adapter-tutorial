---
title: Functional Interfaces
weight: 52
---

# Functional interfaces and lambda expressions

Functional interfaces are interfaces with exactly one method (and possibly additional methods with default implementations.)
We have seen that `Runnable` is a functional interface.
It contains exactly one method `run` without arguments that does not return a result:

```java
interface Runnable {
  void run();
}
```

We used a lambda expression to create an instance of this interface in the static `main` method of the `TicTacToe` class:

```java
SwingUtilities.invokeLater(() -> new TicTacToe().show());
```

A more verbose way of writing this line would be to explicitly assign a variable containing the `Runnable` instance.

```java
final Runnable showNewWindow = () -> new TicTacToe().show();
SwingUtilities.invokeLater(showNewWindow);
```

The lambda expression is transformed into a `Runnable` instance automatically, because `Runnable` is a functinal interface and the lambda expression represents its `run` method.

A more verbose way to define the lambda expression would be:

```java
final Runnable showNewWindow = () -> {
  new TicTacToe().show();
};
SwingUtilities.invokeLater(showNewWindow);
```

We could also define the instance explicitly using an anonymous class.

```java
final Runnable showNewWindow = new Runnable() {
  @Override
  public void run() {
    new TicTacToe().show();
  }
};
SwingUtilities.invokeLater(showNewWindow);
```

By using lambda expressions we let the compiler take care of this instance declaration.
In this example we onle save five lines of code.
But those lines add up.
Programs containing lambda expressions can be significantly more concise than programs without them, as we will see in other examples.

If you came here from the [Static Constructors] section, I recommend you go back there now and come back here when more examples are referenced from the tutorial.

[static constructors]: ../../static_methods/static_constructors
