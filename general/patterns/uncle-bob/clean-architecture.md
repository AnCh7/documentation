# The Clean Architecture

13 August 2012

![img](.clean-architecture-images/CleanArchitecture.jpg)

Dividing the software into layers. Each has at least one layer for business rules, and another for interfaces.

The outer circles are mechanisms. The inner circles are policies.

The Dependency Rule - source code dependencies can only point *inwards*.

Entities - encapsulate *Enterprise wide* business rules.

Use Cases - The software in this layer contains *application specific* business rules. 

Interface Adapters - a set of adapters that convert data from  the format most convenient for the use cases and entities, to the format most convenient for some external agency such as the Database or the Web.

The outermost layer is generally composed of frameworks and tools such as the Database, the Web Framework etc. This layer is where all the details go. The Web is a detail. The  database is a detail. We keep these things on the outside where they can do little harm.

