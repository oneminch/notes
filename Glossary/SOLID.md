- The SOLID principles are best practices to follow when working with [[Object-Oriented Programming|OOP]] languages.

## Single Responsibility Principle (SRP)

- Each class should have only one clear responsibility.
    - It should have only one reason to change

> [!example]
> A `UserManager` class should be responsible for user-specific tasks like registration and deletion, rather than authentication.

## Open/Closed Principle (OCP)

- Software entities should be open for extension but closed for modification.
- New functionality can be added to a system without modifying existing code.
- In [[Java]], this can be accomplished using interfaces, abstract classes & polymorphism.

> [!example]
> A `PaymentProcessor` interface can be implemented to specific needs like `CreditCardProcessor` and `PayPalProcessor`

## Liskov Substitution Principle (LSP)

- Any instance of a base class should be able to be replaced by an instance of a subclass without affecting the correctness of the program, or subclasses created should be able to be used in place of their superclass.
- The subclass should not change the behavior of the superclass.

> [!example]
> In a banking system, `SavingsAccount` & `CheckingAccount` inherit from a base `Account` class. Common features such as deposits and withdraws should remain consistent regardless of the specific account type. According to LSP, a system expecting an instance of `Account` should seamlessly work with any instance of a subclass.

## Interface Segregation Principle (ISP)

- Interfaces should be small and focused, and clients should only depend on the interfaces they need.
- Avoid creating large, general-purpose interfaces that are used by many clients. Instead, create multiple small interfaces that are each focused on a specific set of functionality, so that clients can then depend on only the interfaces they need.
- This helps prevent classes from being forced to implement methods they do not use.

> [!example]
> In a messaging system, instead of having a single interface with multiple methods (e.g. `sendMessage`, `receiveMessage`, etc.), create separate interfaces (e.g. `Sender`, `Receiver`) that clients can implement to match their specific needs.

## Dependency Inversion Principle (DIP)

- High-level modules should not depend on low-level modules. But, both should depend on abstractions.
- Abstractions should not depend on details. Details should depend on abstractions.
- Use interfaces or abstract classes to define the behavior of your classes, and then use dependency injection to provide the specific implementations.

> [!example]
> In an e-commerce system, an `OrderProcessor` class should depend on a `PaymentGateway` interface instead of a specific payment gateway implementation. This will allow for easier switching between different payment gateway providers without impacting core logic.
