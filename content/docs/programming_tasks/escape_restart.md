---
title: Escape Restart
weight: 46
---

# Step 6: Implementing restart functionality

[zip file]: https://github.com/sebfisch/emf-adapter-tutorial-code/archive/step5-winning-check-v2.zip

In order to work on this task, check out the code for the fifth step from the git repository or download the corresponding [zip file].

    git checkout -b step5 step5-winning-check-v2

This step contains the solutions for the previous tasks.

To avoid having to reopen the app after every game, it would be useful to be able to restart games.
Implement the possibility to restart games by pressing the escape key.

- Refactor the `BoardUi` adapter to implement the `KeyListener` interface.
- Override an appropriate `KeyListener` method to restart the game when the escape key is pressed.
- Make sure to add the `BoardUi` adapter to every user interface component created by the app.

Compare your code to my [solution] of this final step.

[solution]: https://github.com/sebfisch/emf-adapter-tutorial-code/commit/1de6836eff96549d2dc07a7d3db15e395f2388c0
