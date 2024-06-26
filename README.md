# Design Patterns and good coding practices

Who am I [José Teodoro da Silva](https://www.linkedin.com/in/jteodorosilva/)

## Langs

Python, Java, Golang, Javascript

## Tools

Reuse, maintenance, readability, previsibility, brain loading, safe.

All of that is created to be easier, and safer to code and build software.

* dont touch function parameters;
* Big O notation O(n.logn) comparison based. Rather do in place;

* filter, map, reduce: keep ordering; ( new arrays / lists )

## We choose the trade-off that matters most.

```java
// filling lazy fields changing the input parameters
public void activeUsersInPlace(List<User> users) {}

// immutable objects leading to huge memory consumtion
public List<User> activeUsers(List<User> users) {}

// fluent code, but not immutable with side effects
User.withName("user name").withId(100).withPassword('batatinha').build();
{
    return this
}

```

## About concurrency

Concurrency is crucial when studying design patterns because ignoring it can lead to subtle and devastating errors.  Without proper concurrency management, your applications may suffer from race conditions, deadlocks, or unpredictable performance.  Many design patterns inherently involve complex concurrency challenges; failing to address them will result in unreliable and potentially disastrous software.

[Concurrency](./concurrency.md)

## About Objects

Object-oriented programming (OOP) centers around the concept of "objects" that encapsulate data (attributes) and the code that manipulates it (methods). This organization promotes code reusability, modularity, and easier understanding of complex systems. OOP emphasizes breaking down problems into self-contained objects that interact with each other.

[POO](./oo.md)

## About good practices

Good coding practices promote modularity, reusability, and maintainability – principles that align perfectly with object-oriented programming (OOP). OOP provides structures (classes, inheritance) to implement these practices. Design patterns offer proven solutions to common problems, often within a concurrent context.  Understanding good practices alongside OOP and design patterns is essential for crafting robust, scalable, and efficient software.

[Good Practices](./good-practices.md)

## About Patterns

Design patterns in object-oriented programming (OOP) provide reusable, time-tested solutions to common software design problems. These patterns offer blueprints for structuring objects and their interactions, promoting flexibility, maintainability, and code clarity. Understanding design patterns is a valuable asset for any OOP developer striving to create well-structured applications.

[Design Patterns](./design-patterns.md)

## About architectural patterns

Architectural patterns offer high-level strategies for organizing large-scale software systems. They define fundamental structures and relationships between components, addressing concerns like scalability, performance, and modularity.  Examples include layered architecture, MVC (Model-View-Controller), and microservices. Understanding architectural patterns helps you make informed decisions about the overall structure of your application.

[Architectural Patterns](arch-patterns.md)


## Others

### Versionamento Semantico

// nao surpreender os amiguinhos
// honrar contratos
v<break_change>.<add_or_change_feature>.<fix_bug>

v8.0.0
v8.100.99 // pra ca funciona
v12.0.0 // nao funciona

## References

[patterns para js](https://www.patterns.dev/)

[refactoring.guru](https://refactoring.guru/)

[Source Making](https://sourcemaking.com/design_patterns/)

[Improving the Design of Existing Code by Martin Fowler](https://martinfowler.com/books/refactoring.html)

[GoF - Design patterns](https://www.amazon.com.br/Padr%C3%B5es-Projetos-Solu%C3%A7%C3%B5es-Reutiliz%C3%A1veis-Orientados/dp/8573076100)

[Clean Code](https://www.amazon.com.br/dp/B001GSTOAM)