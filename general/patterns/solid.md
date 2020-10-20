**Single-responsiblity principle**
A class should have one and only one reason to change, meaning that a class should have only one job.
(Модуль должен иметь только одну обязанность). 

**Open-closed principle**
Objects or entities should be open for extension, but closed for modification.
(Расширение поведения класса без его модификации через динамический полиморфизм).

**Liskov substitution principle**
Every subclass/derived class should be substitutable for their base/parent class.
(Функции, которые используют базовый тип, должны иметь возможность использовать подтипы базового типа, не зная об этом).

**Interface segregation principle**
A client should never be forced to implement an interface that it doesn't use or clients shouldn't be forced to depend on methods they do not use.
(Разделение интерфейса облегчает использование и тестирование модулей).

**Dependency Inversion Principle**
Entities must depend on abstractions not on concretions. It states that the high level module must not depend on the low level module, but they should depend on abstractions.
(Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций. Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций).