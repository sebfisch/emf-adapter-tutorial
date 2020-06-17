---
title: Typed Adapters
weight: 42
---

# Step 4: Refactoring the adapters

[zip file]: https://github.com/sebfisch/emf-adapter-tutorial-code/archive/step3-object-adapter-v1.zip

In order to work on this task, check out the code for the third step from the git repository or download the corresponding [zip file].

    git checkout -b step3 step3-object-adapter-v1

This step contains the base class `ObjectAdapter` discussed previously.

Refactor the classes `FieldAccess`, `FieldUi` and `BoardUi` to extend the `ObjectAdapter` class instead of implementing the `Adapter` interface directly.

- Adjust the constructors of all adapters to call the superclass constructor with the correct class.
- Replace references to the wrapped target by calls to `getTarget`.
- Change the implementation of the static `from` methods to use the `from` method of the superclass.
- Add a method `fromContainer` to the `BoardUi` class that specializes the corresponding superclass method.
- Adjust the implementations of all adapters removing as much duplicated code as you can.

The [solution] of this task removes much more code than it adds.

[solution]: https://github.com/sebfisch/emf-adapter-tutorial-code/commit/c0c3966ec507f99f9c1e0fa4a4f150cd0cccbdd1

Next, implement a [winning check].

[winning check]: ../winning_check
