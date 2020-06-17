---
title: Type Parameters
weight: 54
---

# Type safe generic programming

[consumers]: ../functional_interfaces#consumers

We can use type parameters to implement functionality for different types without sacrificing type safety.
Let's revisit the functional interface `Consumer`.

```java
interface Consumer<T> {
  void accept(T t);
}
```

We saw it in the background section on [consumers] where we discussed the following application of the `forEach` method.

```java
board.getFields().forEach(field -> {
  FieldHelpers.addUnmarkedFieldComponent(field, panel);
});
```

The `accept` method can take arguments of arbitrary (object-) types.
The argument type is reflected in the type parameter `T` which is also mentioned with the name of the interface.

Let's compare the above definition of the `Consumer` interface with the definition below.

```java
interface ObjectConsumer {
  void accept(Object o);
}
```

[lambda expression]: ../functional_interfaces

Here, the `accept` method can also take arguments of arbitrary (object-) types.
But the argument type is not mentioned with the name of the interface.
As a consequence, when implementing the accept method (for example with a [lambda expression]) there are no compile time guarantees about the type of its argument (other than that it is an instance of `Object`.)

In the application of `forEach` above, the type parameters of `Iterable<T>` and `Consumer<T>` are linked in such a way that, when implementing the `accept` method, we can be sure that the argument is of type `T`. A type cast at run-time is not necessary because we get a compile-time guarantee that the type `T` equals `Field`.

We will see more examples of how type parameters can be used to express compile-time guarantees about types.
Those guarantees help avoid programming errors which would result in compiler errors rather than exceptions like, for example, class cast exceptions.

If you came here via the [static helpers] section, I recommend you go back there now and come back here later when more examples are referenced from the tutorial.

[static helpers]: ../../static_methods/static_helpers

## Optional values

[optional class]: https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Optional.html

Optional values can be either empty or present and the [Optional class] provides methods `isEmpty` and `isPresent` to test which of the two is the case.
The method `orElse` can be used to access the wrapped value providing a default value which is returned in the empty case, and `orElseThrow` is similar but throws an exception if it is called on an empty value.
The `Optional` class has a type parameter for the type of the nested value.
For example, the following statements access a value of type `Optional<String>` to output the wrapped value if it is present.

```java
if (optionalString.isPresent()) {
	System.out.println(optionalString.orElseThrow());
}
```

Howewer, those methods are often avoided in favor of other methods to transform or access optional values.
We can rewrite the above statements using the method `ifPresent` which looks much cleaner:

```java
optionalString.ifPresent(System.out::println);
```

The method `ifPresent` takes a consumer as argument.
The `accept` method of the consumer is called with the wrapped value if the optional value is present; otherwise `accept` is not called.
In this example, we pass a [method reference].
Method references (like lambda expressions) are converted to instances of functional interfaces automatically.

[method reference]: ../functional_interfaces#method-references

As another example, the following expression of type `Optional<Player>` computes the `Player` who marked a given `Field` without an explicit `null`- or `isEmpty`-check.

```java
Optional.ofNullable(field.getMark()).map(Mark::getPlayer)
```

We use the static constructor method `ofNullable` to create an optional value from the mark of a field that may be `null` in our game-state model.
The result of `ofNullable` is empty if the argument is `null` and present otherwise.

On the result, we call the `map` method which transforms the optional value using the given function if one is present.
On empty values the `map` method returns an empty result and does not call the provided function.
Again, we use a [method reference] as argument -- this time to access the player attribute of the optional mark.

Another useful method on optional values is `flatMap`.
The `flatMap` method combines the `map` operation with a subsequent flattening step.
For example, we can pass the identity function to `flatMap` to flatten nested optional values.

```java
static Optional<T> flat(Optional<Optional<T>> nested) {
	return nested.flatMap(optional -> optional);
}
```

In this case we do not need the mapping functionality of `flatMap`.
Usually, `flatMap` is used to apply multiple functions with optional results in a row.
Chaining functions with optional results using `flatMap` helps avoid null-pointer exceptions compared to chaining methods that may return `null`.
