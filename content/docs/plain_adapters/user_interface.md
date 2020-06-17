---
title: User Interface
weight: 24
---

# User interface adapters

[game.adapters.ui package]: https://github.com/sebfisch/emf-adapter-tutorial-code/tree/step2-plain-adapters-v1/de.sebfisch.tictactoe/src/game/adapters/ui

We now turn the static helper methods for boards and fields that have been used to manage corresponding user interface components into adapters in the [game.adapters.ui package].

## Field UI

Apart from the wrapped `Field` instance the `FieldUi` adapter stores a button that can be clicked to mark the represented field.

```java
// game.adapters.ui.FieldUi
public class FieldUi implements Adapter {

	private static final int LABEL_SIZE = 90;
	private static final Dimension FIELD_DIMENSIONS = new Dimension(100, 100);

	private Field field;
	private final JButton button;

	FieldUi() {
		button = new JButton("");
		button.setPreferredSize(FIELD_DIMENSIONS);
		button.addActionListener(_event -> FieldAccess.from(field).makeMove());
	}

	public JComponent getUnmarkedComponent() {
		return button;
	}

	// more methods follow
}
```

Unlike the wrapped target, the button is assigned in the constructor.
It has an attached action listener that uses the `FieldAccess` adapter for the wrapped field to register a corresponding move in the game state.
Note that unlike in the previous version where the action listener also updated the user interface to reflect the move, this time the action listener does not update the user interface directly.

Instead we use the `notifyChanged` method to update the user interface whenever a mark is placed on the wrapped field.

```java
// game.adapters.ui.FieldUi
@Override
public void notifyChanged(final Notification notification) {
	if (notification.isTouch()) {
		return;
	}
	if (notification.getEventType() == Notification.SET &&
			notification.getFeatureID(Field.class) == GamePackage.FIELD__MARK) {
		updateMark();
	}
}
```

We use several methods from the `Notification` class to determine that indeed the `mark` reference has been modified.
If that is the case, we call `updateMark` defined as follows.

```java
// game.adapters.ui.FieldUi
public void updateMark() {
	FieldAccess.from(field).getMarkingPlayer().ifPresent(player -> {
		final JComponent child =
			UiConstructors.boldLabel(player.toString(), LABEL_SIZE);
		BoardUi.from(field.getBoard()).replaceChild(field.getIndex(), child);
	});
}
```

The implementation of this function is similar to its static version.
Instead of taking a field as argument it delegates calls to the wrapped `field`, and instead of using static methods `getMarkingPlayer` and `replaceChild` it uses appropriate methods of corresponding adapters.

## Board UI

The user interface adapter for boards is similar to the adapters for fields discussed previously in that it wraps a target instance and provides a static `from` method to query adapters associated to boards.
Similar to the `FieldUi` class, `BoardUi` also wraps a user interface component `panel` created in the constructor, so the `replaceChild` does not need to take it as argument.

```java
// game.adapters.ui.BoardUi
public void replaceChild(final int index, final JComponent child) {
	panel.remove(index);
	panel.add(child, index);
	panel.revalidate();
	panel.repaint();
}
```

Another noteworthy method is `setTarget` to set the wrapped `Board` instance.

```java
@Override
public void setTarget(final Notifier target) {
	if (target instanceof Board) {
		board = (Board) target;
		panel.removeAll();
		board.getFields().forEach(field -> {
			panel.add(FieldUi.from(field).getUnmarkedComponent());
		});
	}
}
```

In addition to setting the wrapped `board` member if the `target` type matches, the panel is reset and initialized with buttons for all contained fields.

Some methods with implementations that are very similar across different adapters have been omitted.
Take a look at the [game.adapters.ui package] to inspect the complete source code.
Later we will look at a shared [base class] for adapters that can be used to avoid repetition when defining adapters.
But first, let's see how adapters can be created and associated with model instances using [adapter factories].

[base class]: ../../object_adapter
[adapter factories]: ../adapter_factories
