---
title: Functional Interfaces
weight: 52
---

# Functional interfaces and lambda expressions

[default methods]: https://docs.oracle.com/javase/tutorial/java/IandI/defaultmethods.html

Functional interfaces are interfaces with exactly one method (and possibly additional [default methods].)
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
In this example we only save five lines of code.
But those lines add up.
Programs containing lambda expressions can be significantly more concise than programs without them, as we will see in other examples.

If you came here via the [static constructors] section, I recommend you go back there now and come back here later when more examples are referenced from the tutorial.

[static constructors]: ../../static_methods/static_constructors

## Consumers

[function package]: https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/function/package-summary.html

Consumers are defined in the [function package] that contains many pre-defined functional interfaces.
Unlike the `run` method in `Runnable`, the `accept` method of `Consumers` takes an argument.

```java
interface Consumer<T> {
	void accept(T t);
	// default methods omitted
}
```

[type parameters]: ../type_parameters

Note that the `Consumer` interface has a type parameter for the argument type of `accept`.
You can learn more about [type parameters] in another background section.

[static helpers]: ../../static_methods/static_helpers

Consumers can be passed as arguments to the `forEach` method of the `Iterable` interface as we have seen in the [static helpers] section:

```java
board.getFields().forEach(field -> {
	FieldHelpers.addUnmarkedFieldComponent(field, panel);
});
```

Here, the lambda expression has an argument.
It is transformed into an anonymous class just like before:

```java
board.getFields().forEach(new Consumer<Field>() {
	@Override
	public void accept(final Field field) {
		FieldHelpers.addUnmarkedFieldComponent(field, panel);
	}
});
```

## Functions

Another frequently used functional interface from the [function package] is the `Function` interface.

```java
interface Function<Arg,Res> {
	Res apply(Arg arg);
	// omitting default methods
}
```

Unline the `accept` method of consumers, the `apply` method of functions returns a result.
That's why the `Function` interface has two type parameters, one for the argument and one for the result.
Instances of `Function` are supplied in calls to `map` and `flatMap` methods which can be used to transform optional or streamed values as we will see later.

If you came here via the [static helpers] section, I recommend you go back there now and come back here later when more examples are referenced from the tutorial.

## Method references

[method references]: https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html
[optional values]: ../type_parameters#optional-values

[Method references] are an alternative syntax to lambda expressions that is also converted into instances of functional interfaces automatically.
If you already read about [optional values] in the background section on type parameters you came accross the following example.

```java
optionalString.ifPresent(System.out::println);
```

This statement means the same as the following statement which uses a lambda expression instead of a method reference.

```java
optionalString.ifPresent(string -> System.out.println(string));
```

In both statements, the argument to `isPresent` is converted into an instance of the `Consumer` interface similar to the following.

```java
optionalString.ifPresent(new Consumer<String>() {
	@Override
	public void accept(String string) {
		System.out.println(string);
	}
});
```

Method references are transformed into instances of any functional interface, not just consumers.
Let's reconsider the computation of the optional player whose mark is on a given `field`.

```java
Optional.ofNullable(field.getMark()).map(Mark::getPlayer)
```

In this example, the method reference `Mark::getPlayer` means the same as the following lambda expression.

```java
Optional.ofNullable(field.getMark()).map(mark -> mark.getPlayer())
```

In both cases the argument to `map` is converted into an instance of `Function` similar to the following.

```java
Optional.ofNullable(field.getMark()).map(new Function<Mark,Player>() {
	@Override
	public Player apply(Mark mark) {
		return mark.getPlayer();
	}
})
```

As you can see, method references can be created using objects (like `System.out` in the previous example) as well as classes (like `Mark` in this example) and type parameters are determined automatically by the compiler.
You can read more about what exactly different kinds of [method references] correspond to in external documentation.
