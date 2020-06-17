---
title: Winning Check
weight: 44
---

# Step 5: Implementing the winning condition

[zip file]: https://github.com/sebfisch/emf-adapter-tutorial-code/archive/step4-typed-adapters-v1.zip

In order to work on this task, check out the code for the fourth step from the git repository or download the corresponding [zip file].

    git checkout -b step4 step4-typed-adapters-v1

This step contains the solution for the previous task.

If you looked closely, you might have noticed that the animation of the app shown in the beginning was different from the version implemented so far.
When one player has won the game, the buttons in the remaining unmarked fields disappeared in the animation.

Implement this functionality to remove remaining buttons when one player has won the game.

- Add a new subclass `BoardAccess` of `ObjectAdapter` to the `game.adapters.access` package.
- Implement a static `from` method for this adapter based on the corresponding superclass method.
- Implement a method `hasWinner` that returns a boolean result indicating if one player has won the game.
- Override the method `createBoardAdapter` in the `AccessAdapterFactory` returning an instance of the new adapter class.
- Use the `AccessAdapterFactory` in `GameConstructors.emptyBoard` to associate the new adapter to the created `Board` instance.
- Add a static method to the `UiConstructors` that creates a blank component.
- Add a method `disableUnmarkedFields` to `BoardUi` that replaces all user interface components for unmarked fields with a blank component.
- Override the more specific `notifyChanged` method in `BoardUi` to disable unmarked fields when a field is marked and the game has a winner.

Inspect a [solution] to see what code to add to solve this task.

[solution]: https://github.com/sebfisch/emf-adapter-tutorial-code/commit/349f8d4e2a56c21bd4997b33182bcd6325ba38f3

Next, add the ability to [restart] the game.

[restart]: ../escape_restart
