---
title: On using adapters with EMF models
type: docs
---

# On using adapters with EMF models

[eclipse modelling framework (emf)]: https://www.eclipse.org/modeling/emf/
[adapter interface]: https://download.eclipse.org/modeling/emf/emf/javadoc/2.11/org/eclipse/emf/common/notify/Adapter.html

The [Eclipse Modelling Framework (EMF)] provides an [Adapter Interface] that can be implemented to observe model instances or extend their functionality. EMF provides mechanisms to generate adapters, for example for editing. Here, however, we will focus on writing adapters ourselves and associating them with model instances.
