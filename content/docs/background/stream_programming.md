---
title: Stream Programming
weight: 56
---

# Programming with streams

[parameterized type]: ../type_parameters
[functional interfaces]: ../functional_interfaces

The `Stream` type is a [parameterized type] representing multiple elements.
It provides methods to transform or filter contained elements which can be used to search elements with certain properties.
The adapters we wrote have a static method `from` that can be used to search for adapters of their own type.
Let's revisit one ofe them.

```java
public static FieldAccess from(final Field field) {
	return field.eAdapters().stream() //
			.filter(FieldAccess.class::isInstance) //
			.map(FieldAccess.class::cast) //
			.findFirst().orElseThrow();
}
```

[predicate]: ../functional_interfaces#functions-and-predicates
[method reference]: ../functional_interfaces#method-references

Here, the `stream` method is used to create a `Stream<Adapter>` from the `List<Adapter>` returned by `eAdapters`.

Then `filter` is applied to select only those adapters of type `FieldAccess`.
In this case, we expect there to be exactly one such adapter, but in general the result of `filter` can represent many elements or none at all.
The `filter` method takes a [predicate] as argument which we supply using a [method reference].

[optional values]: ../type_parameters#optional-values
[function]: ../functional_interfaces#functions-and-predicates

Afterwards, `map` is called to transform the remaining (in this case presumably exactly one) element(s), casting them to the `FieldAccess` class.
We have already seen a `map` method for [optional values].
The one for streams is similar but transforms all represented elements using the given [function].

Finally, `findFirst` processes the stream and returns an optional value representing the first (in this case presumably the only) element of the stream it is called on.
The stream is only processed by `findFirst` until the first element is found.
Subsequent elements are not tested by `filter` or transformed by `map`, although those methods are called before `findFirst`.

The are other ways to create, transform or process streams.
The static cosntructor `Stream.of` can be used to create a stream from given elements.
Similar to optional values, streams provide a method `flatMap` that combines `map` with an additional step flattening nested streams.
For processing, there are methods `anyMatch` and `allMatch` wich take a predicate as argument and return a boolean result indicating if any or all elements of the stream pass the given test.

[stream api]: https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/stream/package-summary.html

I encourage you to inspect the [Stream API] to find out more about stream programming in Java.

[winning check]: ../../programming_tasks/winning_check

Streams can be used to implement algorithms that would traditionally be implemented using loops and conditionals.
As an example consider the following method of the `BoardAccess` class implementing a [winning check] for the game.

```java
public boolean isWinner(final Player player) {
	return lines().anyMatch(line -> //
		line.allMatch(fa -> //
			fa.getMarkingPlayer().map(player::equals).orElse(false)));
}
```

Here, the `lines` method returns a result of type `Stream<Stream<FieldAccess>>` representing a nested stream of field access adapters.
The definition of `isWinner` captures the idea that a `player` has won the game if on any of the lines of three consecutive fields, they have marked all fields themselves.

Here is an alternative implementation using loops and conditionals.

```java
public boolean isWinner(final Player player) {
	final Iterable<Stream<FieldAccess>> iterLines = () -> lines().iterator();
	for (final Stream<FieldAccess> line : iterLines) {
		final Iterable<FieldAccess> iterFields = () -> line.iterator();
		boolean allMarkedByPlayer = true;
		for (final FieldAccess fa : iterFields) {
			allMarkedByPlayer &=
				fa.getMarkingPlayer().map(player::equals).orElse(false);
		}
		if (allMarkedByPlayer) {
			return true;
		}
	}
	return false;
}
```

It is apparent that the original version makes good use of the Stream API to implement the winning check in a way that is both concise and easy to understand.
