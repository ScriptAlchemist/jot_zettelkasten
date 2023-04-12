i# Object-oriented Design

Object-oriented Design: Outline

* Quick review of five key defining priciples of Object-oriented
  programming
* Introduction to a strategy for solving OOD problems
* Development of use cases of and OOD problem
* Introduction to OOD class diagrams
* Suggestions for developing class diagrams for an OOD problem
* Conclusion of the OOD strategy

## Key OOP Principles

* Object-oriented Encapsulation
  - Store data with the procedures the operate on that data
  - Core feature of the class structure present in most modern languages

Example
```java
class Card {
  String suit;
  String value;
  String toString() {
    return (value + ' of' + suit)
  }
}
```

* Information hiding/abstraction
  - Can hide both data and procedures that should not be visible outside
    the encapsulation/class
  - private keyword in languages such as Java and C#

* Inheritance
  - Can specialize an existing class without modifying the original
  - Syntax varies; extends keyword in Java, : operator in C#

* Object-based Polymorphism
  - A single line of code calling a method can behave differently in
    different contexts
  - Powerful feature when writing programs, but can lessen ability to
    debug, maintain, and understand
  - Based on overriding a method (or implementing an abstract method)
    * Syntax of overrides varies
  - An instance of a subclass (inheriting class) "is-a" member of the
    superclass in Java.
  - For instance, a Joker can be used anywhere a Card is expected.

* Composition/Aggregation
  - The data encapsulated by a class can include instances of the dame
    of other classes
    - We say that the containing class is in a "has-a" relationship
      with the contained classes

## OOD Strategy

* Understand the problem (5-10 minutes)
  - Verify that the primary concern is functionality, not
    performance/scaling (performance and system design)
  - Brainstorm possible funcitonalities that the system might include


## Use Cases

* Problem: Design Amazon
* Some possible use cases:
  - New visitor to www.amazon.com is shown the amazon homepage

### Minimun Viable Product

 


