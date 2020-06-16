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
