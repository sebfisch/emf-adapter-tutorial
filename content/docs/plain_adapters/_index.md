---
title: Plain Adapters
weight: 20
---

# Step 2: Refactoring the app using EMF adapters

An adapter can be wrapped around an object to change its interface.
For example, if you travel abroad and the plug of your hair dryer does not fit into the socket next to the bathroom mirror, you can plug your hair dryer into a travel adapter that matches the foreign sockets.

[emf adapters]: https://download.eclipse.org/modeling/emf/emf/javadoc/2.4.2/org/eclipse/emf/common/notify/Adapter.html

Similarly, [EMF adapters] can be wrapped around EMF model instances to implement methods not present on the original objects.

[zip file]: https://github.com/sebfisch/emf-adapter-tutorial-code/archive/step2-plain-adapters-v1.zip

If you want to follow along, check out the code for the second step from the git repository or download the corresponding [zip file].

    git checkout -b step2 step2-plain-adapters-v1

[game.adapters package]: https://github.com/sebfisch/emf-adapter-tutorial-code/tree/step2-plain-adapters-v1/de.sebfisch.tictactoe/src/game/adapters

It contains a new [game.adapters package] with definitions replacing the classes in the `game.helpers` package of the previous version.
We will define adapters for `Board` and `Field` objects.
Where appropriate, we will separate user interface functionality and model access functionality by defining multiple adapters for the same model type.

Before we inspect hand written adapter implementations, we look at the interface they implement.

```java
public interface Adapter {
	Notifier getTarget();
	void setTarget(Notifier target);
	boolean isAdapterForType(Object object);
	void notifyChanged(Notification notification);
}
```

The first two methods provide access to the so called target of the adapter.
In our case, the targets will be instances of the generated `Board` and `Field` types which implement the mentioned `Notifier` interface automatically.

The method `isAdapterForType` is used to check if an adapter can be applied to objects of specific types. We will override it by testing if the given object is an instance of the correct class for the target.

Finally, the method `notifyChanged` is called whenever the registered target is changed.
We can override it to update the user interface triggered by game-state changes regardless of where they originate.

Let's start by looking at an adapter implementing [model access] for fields.

[model access]: model_access
