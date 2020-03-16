---
path: LSP
date: 2020-03-12T23:50:33.286Z
title: Liskov Substitution Principle promotes more maintainable applications
description: A blog about the Liskov Substitution Principle
---
I used to think of modules as a key part of composition. Include them in other objects as needed. As Sandi Metz describes in “Practical Object-Oriented Design in Ruby”, composition combines distinct parts into a complex whole. Use object oriented composition to combine simple, well-defined, and independent objects into larger, more complex objects. Inject these small, self-contained objects with clear interfaces into any object as needed. These injected parts have no defined relationship with the larger object, but form an integral part of the object, promoting a flexible and maintainable design.

On the other hand, inheritance describes a defined relationship between objects. This hierarchical relationship between a subclass (i.e. the child) and a superclass (i.e. the parent) allows the classes to share code. For example, if a subclass receives a message it does not understand (i.e. a method call), it automatically delegates the message to the superclass (i.e. look for the method on the superclass). See a diagram of the generalized method lookup for a HumanPlayer class, a Player superclass, and associated modules.

![](https://docs.google.com/drawings/u/0/d/sqkAetLGsDy8KiXtTvAcr2A/image?w=135&h=492&rev=1&ac=1&parent=1sTdbBxbiyCENHvzH71p0yt4NegvXKiQYUWGMz7P3GDo)



This relationship adds complexity to an object. An object’s behaviors span across its methods, and those of its modules, superclass, and superclass’ modules. As a result, this object is no longer self-contained, and well-defined. Modules expand the set of messages to which an object responds, increasing complexity even more. As Sandi Metz describes, the total set of messages an object responds to includes:

* Those it implements
* Those implemented in all objects above it in the hierarchy
* Those implemented in any module added to it
* Those implemented in all modules added to any object above it

Modules align more with the inheritance design pattern than the composition design pattern. An application built without following good practices to manage inheritance soon becomes complex, confusing, and brittle (i.e. change inspires fear because of uncertainty around an object’s behavior).

To use inheritance effectively, and have a flexible and maintainable application, follow the Liskov Substitution Principle (LSP), the “L” in[SOLID](https://en.wikipedia.org/wiki/SOLID). LSP, named after its creator, Barbara Liskov, states, “Let q(x) be a property provable about objects x of type T. Then q(y) should be true of objects y of type S where S is a subtype of T.” In other words, subclasses must be substitutable for their superclasses. Subclasses must respond to every message in their superclasses’ interface, taking the same kinds of inputs, and returning the same kinds of outputs. As a result, the methods in a subclass define the subclass. Unexpected behavior does not reside in the superclass or modules of the superclass. The superclass contains public methods already in the subclass. A subclass not following LSP does not relate to its superclass, questioning the hierarchical relationship. Following LSP creates more maintainable applications with well-defined classes.
