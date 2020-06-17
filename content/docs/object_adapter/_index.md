---
title: Object Adapter
weight: 30
---

# Step 3: A base class for typed object adapters

Reviewing the `Adapter` instances discussed in the previous step, we can observe some similarities. All adapter classes have a static method `from`.
To make their similarities more apparent, here are the corresponding definitions.

```java
// gama.adapters.access.FieldAccess
public class FieldAccess {
	public static FieldAccess from(final Field field) {
		return field.eAdapters().stream() //
				.filter(FieldAccess.class::isInstance) //
				.map(FieldAccess.class::cast) //
				.findFirst().orElseThrow();
	} /* other definitions omitted */
}

// game.adapters.ui.FieldUi
public class FieldUi {
	public static FieldUi from(final Field field) {
		return field.eAdapters().stream() //
				.filter(FieldUi.class::isInstance) //
				.map(FieldUi.class::cast) //
				.findFirst().orElseThrow();
	} /* other definitions omitted */
}

// game.adapters.ui.BoardUi
public class BoardUi {
	public static BoardUi from(final Board board) {
		return board.eAdapters().stream() //
				.filter(BoardUi.class::isInstance) //
				.map(BoardUi.class::cast) //
				.findFirst().orElseThrow();
	} /* other definitions omitted */
}
```

[stream programming]: ../background/stream_programming

All of these methods use [stream programming] to query an adapter of the corresponding type from a model instance. The only difference is in the types and corresponding classes used to `map` and `filter` the stream of adapters.

Another observation is that all adapters wrap their target and provide access through the methods `getTarget` and `setTarget` with very similar definitions.

We can abstract away those similarities in a parameterized class `ObjectAdapter`.

```java
// util.ObjectAdapter
public class ObjectAdapter<T extends EObject> implements Adapter {
	private final Class<T> targetC;
	private T target;

	public ObjectAdapter(final Class<T> targetC) {
		this.targetC = targetC;
	}

	@Override
	public T getTarget() {
		return target;
	}

	@Override
	public void setTarget(final Notifier newTarget) {
		if (targetC.isInstance(newTarget)) {
			setTarget(targetC.cast(newTarget));
		}
	}

	public void setTarget(final T newTarget) {
		target = newTarget;
	}

	@Override
	public boolean isAdapterForType(final Object object) {
		return targetC.isInstance(object);
	}
	// more definitions follow
}
```

[type parameter]: ../background/type_parameters

The [type parameter] `T` is bounded by the `EObject` interface which extends `Notifier` and is implemented by generated object types automatically.
As a consequence, `ObjectAdapter` instances can only be created for types that implement `EObject`.

Like previously, the `getTarget` method has a more specific type than the overridden method from the `Adapter` interface to increase type safety when accessing the target.

The definition of `setTarget` uses the target class passed to the constructor to check if the new target has the correct type.
We provide a more specific `setTarget` method in addition to the original one.
It can be overridden to support additional processing when setting a target without repeating the type check.

There is also an implementation for `notifyChanged` along with a more specific variant that, by default, does nothing.

```java
// util.ObjectAdapter
@Override
public void notifyChanged(final Notification n) {
	if (n.isTouch()) {
		return;
	}
	notifyChanged(n.getEventType(), n.getFeatureID(targetC));
}

public void notifyChanged(final int eventType, final int featureId) {}
```

In many cases, the more specific version of `notifyChanged` is more convenient because it provides access to the event type and feature ID wrapped in the notification.
In cases where this information is not sufficient, the original variant can be overridden instead.

Finally, we look at the signature of the generalized static `from` method.

```java
// util.ObjectAdapter
public static <T extends EObject, A extends ObjectAdapter<? super T>> //
A from(final T object, final Class<? extends A> adapterC) {
	// stream programming
}
```

If this looks daunting, just note that we could call it like this, if `board` is an instance of `Board` and `BoardUi` would be a subclass of `ObjectAdapter<Board>`:

```java
final BoardUi boardUi = ObjectAdapter.from(board, BoardUi.class);
```

For convenience, the `ObjectAdapter` class provides two more static methods to query adapters.
The method `fromContents` queries adapters from direct or indirect contents of a given object.
The method `fromContainer` queries adapters from direct or indirect containers of a a given object.

The complete implementation of the `ObjectAdapter` class is in the [util package].

[util package]: https://github.com/sebfisch/emf-adapter-tutorial-code/tree/step3-object-adapter-v1/de.sebfisch.tictactoe/src/util
