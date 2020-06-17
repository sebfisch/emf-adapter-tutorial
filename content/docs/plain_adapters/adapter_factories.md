---
title: Adapter Factories
weight: 26
---

# Creating and attaching adapters

To wrap up the second step in the tutorial, we now discuss how adapter factories can be used to create adapters and associate them with model instances.
We could just create adapter instances and add them to the list returned by the `eAdapters` method of model instances.
This would lead to `setTarget` being called on the adapter to complete the association.

Instead we will hide the creation of adapters in subclasses of the class `GameAdapterFactory` generated automatically with our model code.
Here are two subclasses -- one for access adapters and one for user interface adapters.

```java
// game.adapter.access.AccessAdapterFactory
public class AccessAdapterFactory extends GameAdapterFactory {
	@Override
	public FieldAccess createFieldAdapter() {
		return new FieldAccess();
	}
}

// game.adapter.ui.UiAdapterFactory
public class UiAdapterFactory extends GameAdapterFactory {
	@Override
	public BoardUi createBoardAdapter() {
		return new BoardUi();
	}
	@Override
	public FieldUi createFieldAdapter() {
		return new FieldUi();
	}
}
```

[static constructors]: ../../static_methods/static_constructors

We use these factories when initializing game-state model instances in the [static constructors] discussed in the first part of this tutorial.

```java
// constructors.GameConstructors
public class GameConstructors {

	private static final int FIELD_COUNT = 9;
	private static final UiAdapterFactory UI =
		new UiAdapterFactory();
	private static final AccessAdapterFactory ACCESS =
		new AccessAdapterFactory();

	public static Board emptyBoard() {
		final Board board = GameFactory.eINSTANCE.createBoard();
		board.setCurrentPlayer(Player.X);
		for (int index = 0; index < FIELD_COUNT; index++) {
			board.getFields().add(emptyField(index));
		}
		UI.adaptAllNew(board);
		return board;
	}

	public static Field emptyField(final int index) {
		final Field field = GameFactory.eINSTANCE.createField();
		field.setIndex(index);
		field.setMark(null);
		UI.adaptAllNew(field);
		ACCESS.adaptAllNew(field);
		return field;
	}

	public static Mark markFor(final Player player) {
		final Mark mark = GameFactory.eINSTANCE.createMark();
		mark.setPlayer(player);
		return mark;
	}
}
```

Compared to the previous version, the implementation is only extended with constants `ACCESS` and `UI` for the adapter factory instances and corresponding calls to their method `adaptAllNew`.
This method takes a model instance, creates an appropriate adapter and associates the two.

This concludes the second part of the tutorial.
We now review similarities in the defined adapter instances and extract them into a common [base class] to avoid repetition.

[base class]: ../../object_adapter
