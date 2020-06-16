---
title: Static Constructors
weight: 14
---

# Static methods constructing required instances

Let's take a look at the main class used to start the game app.

```java
public class TicTacToe {

	public static void main(final String[] args) {
		SwingUtilities.invokeLater(() -> new TicTacToe().show());
	}

	private static final String WINDOW_TITLE = "Tic Tac Toe";

	private final Board board;
	private final JFrame window;

	public TicTacToe() {
		board = GameConstructors.emptyBoard();
		window = UiConstructors.emptyWindow(WINDOW_TITLE);
		window.getContentPane().add(BoardHelpers.getComponent(board));
	}

	public void show() {
		window.pack();
		window.setVisible(true);
	}
}
```

The `main` method opens a window by creating an instance of the `TicTacToe` class and calling its `show` method on the user interface thread.
The lambda expression passed to `invokeLater` is converted into an instance of the functional interface `Runnable` automatically.
You can read about [Functional Interfaces] in the background section, if you are not familiar with functional interfaces or lambda expressions.

[functional interfaces]: ../../background/functional_interfaces

Our application does not yet use any EMF adapters.
The constructor of the `TicTacToe` class uses static methods of different classes to create required instances.

- The class `UiConstructors` is used to create user interface components that are configured in the way we need for our app.
- The class `GameConstructors` is used to create instances of types in our game-state model
- The class `BoardHelpers` defines additional functionality on `Board` model instances.

By defining static contructor methods, we can hide the required initialization of created instances.
For example, the `emptyWindow` method sets a default close operation on the created window:

```java
public static JFrame emptyWindow(final String title) {
  final JFrame frame = new JFrame(title);
  frame.setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE);
  frame.setResizable(false);
  return frame;
}
```

Similarly, the `emptyBoard` method initializes the created board with the first player to move and the correct number of unmarked fields:

```java
public static Board emptyBoard() {
  final Board board = GameFactory.eINSTANCE.createBoard();
  board.setCurrentPlayer(Player.X);
  for (int index = 0; index < FIELD_COUNT; index++) {
    board.getFields().add(emptyField(index));
  }
  return board;
}
```

You can inspect the complete implementation of the static constructors in the [constructors package].

[constructors package]: https://github.com/sebfisch/emf-adapter-tutorial-code/tree/step1-static-methods-v1/de.sebfisch.tictactoe/src/constructors
