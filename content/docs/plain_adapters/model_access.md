---
title: Model Access
weight: 22
---

# Access adapters

[game.adapters.access package]: https://github.com/sebfisch/emf-adapter-tutorial-code/tree/step2-plain-adapters-v1/de.sebfisch.tictactoe/src/game/adapters/access

When reviewing the `FieldHelpers` class we observed that it contains methods related to user interface components as well as methods related to querying and modifying game-model data.
For access to the game model we will implement adapters in the [game.adapters.access package].
For now, it only contains an adapter for `Field` instances because the app did not have any board helpers unrelated to user interfaces.

We implement the field access adapter in a class `FieldAccess` that wraps a `Field` instance.

```java
// game.adapters.access.FieldAccess
public class FieldAccess implements Adapter {
	private Field field;

	FieldAccess() {}

	@Override
	public Field getTarget() {
		return field;
	}

	@Override
	public void setTarget(Notifier target) {
		if (target instanceof Field) {
			field = (Field) target;
		}
	}

	// additional methods follow
}
```

[adapter factories]: ../adapter_factories

The constructor is package private because creating adapters will be handled by [adapter factories] later.
The target is not passed to the construtor but will be set by `setTarget`.
Note that we override the `getTarget` method with a more specific return type to increase type safety when accessing the target of the adapter.
The definitions of the other two `Adapter` methods are omitted.
We override `isAdapterForType` using the same `instanceof` check used by `setTarget`.
The `notifyChanged` method is overriden with an empty implementation.

Let's get to the interesting parts actually implementing the required field access.
The static methods `makeMove` and `getMarkingPlayer` are turned into instance methods.
Their implementation is unchanged but instead of taking a `field` instance as argument, they delegate calls to the wrapped `field` instance.

```java
// game.adapters.access.FieldAccess
public void makeMove() {
	final Board board = field.getBoard();
	final Player player = board.getCurrentPlayer();
	field.setMark(GameConstructors.markFor(player));
	board.setCurrentPlayer(Player.X.equals(player) ? Player.O : Player.X);
}

public Optional<Player> getMarkingPlayer() {
	return Optional.ofNullable(field.getMark()).map(Mark::getPlayer);
}
```

EMF adapters are associated with their targets which provide a method `eAdapters` to access a list of all associated adapters.
To query a `FieldAccess` adapter from the list of adapters associated with a `Field` instance, we define a static method `from` in the adapter class.

```java
// game.adapters.access.FieldAccess
public static FieldAccess from(final Field field) {
	return field.eAdapters().stream() //
			.filter(FieldAccess.class::isInstance) //
			.map(FieldAccess.class::cast) //
			.findFirst().orElseThrow();
}
```

[stream programming]: ../../background/stream_programming

The method uses [stream programming] to find an adapter of the correct type.
It throws an exception if none is found.
We expect to only call this method if we know a matching adapter is associated with the `field`, so it should be an error to call this method with fields that do not have one.

[user interface]: ../user_interface

This completes the implementation of our first hand written adapter. Next we discuss adapters managing the [user interface] of our game-state types.
