---
alias: OOP
---

- **Object-Oriented Programming**
    - A programming paradigm for designing programs using classes and objects.
    - Makes development and maintenance of programs easier
    - Provides data hiding unlike in a procedure-oriented programming.

- **Object**
    - Any physical or logical entity that has state and behavior. e.g. a car.
    - Can be defined as an instance of a class.
    - Take up space in memory.
    - All **objects** have:
        - ==Identity== - Separates one object from another
        - ==Attributes== - Properties
        - ==Behaviors== - Methods

- **Class**
    - Blueprint/template for creating objects.
    - Define object data types and methods.
    - Don't consume any space.
    - Components of a class:
        - ==Name / Type== - what it is
        - ==Attributes / Properties / Data== - what describes it
        - ==Behaviors / Operations / Methods== - what it can do
    - Classes allow us to create our own data types.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.z = 0

    def update_z(self, value):
        self.z = value

    def increment_y(self, num):
        self.y += num

    def display(self):
        print(f"({self.x}, {self.y}, {self.z})")

# Ways to instantiate objects,
# modify attributes and invoke methods

point_1 = Point(15, 25)

point_2 = Point(15, 25)
point_2.x = 25
point_2.y = 35
point_2.z = 45
point_2.display()

point_3 = Point(15, 25)
point_3.update_z(10)
```

## The Four Fundamental Pillars of OOP

### Abstraction

- Contextual model of a real-world object that represents its relevant details.
- The process of hiding the implementation / internal details and showing only essential functionality to the user.
- Involves reducing characteristics from something to a set of essential features.
- Achieved using:
    - Abstract classes
    - Interfaces

- **Generalization**
    - Extract shared characteristics from multiple classes and combine then into a superclass.

- **Specialization**
    - Create new subclasses from an existing class.

### Polymorphism

- Allows us to present the same interface for varying underlying forms.
- **Compile Time Polymorphism (Early Binding)**
    - _Static polymorphism_ uses method overloading.
- **Run Time Polymorphism (Late Binding)**
    - _Dynamic polymorphism_ uses method overriding.
    - _Dynamic Method Dispatch_

```java
class Computer {
    public void boot() {
        System.out.println("Computer Booting...")
    }
}

class Laptop {
    public void boot() {
        System.out.println("Laptop Booting...")
    }
}

public class Main {
    public static void main(String[] args) {
        Computer pc = new Computer();
        pc.boot();           // Computer Booting...

        pc = new Laptop();   // Valid reassignment ✅
        pc.boot();           // Laptop Booting...
    }
}
```

> [!note]
> For a Java object to be considered polymorphic, it has to pass more than one **IS-A** test.
>
> All Java objects pass the IS-A test for their own type and for the class Object. Hence, they are polymorphic.

#### Method Overloading

- Method overloading can be implemented in two different ways that involve implementing two or more methods with the same name.
- Occurs within a class.
- One difference is in **the numbers of arguments** the methods take.

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}
```

- Another difference is in **the types of the arguments** the methods take.

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }
}
```

- It’s also valid to define a method with both types of overloading:

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }

    public double add(double a, double b) {
        return a + b;
    }
}
```

> [!note]
> It’s not possible to have two method implementations that differ only in their return types.

- Overloading can also be applied to class [[Constructor|constructors]].

```java
public class Person {
    private String name;
    private int age;

    // No-argument constructor
    public Person() {
        name = "Unknown";
        age = 0;
    }

    // Constructor with name parameter
    public Person(String name) {
        this.name = name;
        age = 0;
    }

    // Constructor with name and age parameters
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

- Method overloading provides features such as type promotion and static binding.

> [!note]
> The Java `main()` method can be overloaded, but the JVM specifically calls `main(String[] args)`.

#### Method Overriding

- With method overriding, we can provide fine-grained implementations of methods in subclasses originally defined in a base class.
- Occurs in two classes with an **IS-A** relationship.

```java
public class Vehicle {
    public String accelerate(long mph) {
        return "Vehicle Accelerating at: " + mph + " MPH.";
    }

    public String stop() {
        return "Vehicle Stopped.";
    }
}
```

```java
public class ATV extends Vehicle {
    @Override
    public String accelerate(long mph) {
        return "ATV Accelerating at: " + mph + " MPH.";
    }
}
```

- **Covariant return types**
    - Changing the return type of a method is also possible provided that the overridden return type is a subtype of the original type.

> [!note]
> Since the compiler can’t determine what method to call at compile time (as both the base class and the subclasses define the same methods), the compiler needs to check the type of object to know what method should be called.
>
> This check happens at runtime, and because of that method overriding is a typical example of **dynamic binding**.

> [!important]
>
> - In Java, the access modifier of the overriding method cannot be more restrictive than the overridden method.
>     - The access modifier of the overriding method in the subclass must provide at least as much access as the overridden method in the superclass.
>     - e.g., if the superclass has a `protected` method, the overriding method in the subclass can be declared as either `protected` or `public`, but not `private` or default (package-private).

- It is possible to overload a method that has been inherited or overridden in Java.
    - When a subclass inherits a method from its superclass, it can overload that method by defining another method with the same name but a different parameter list (different number, types, or order of parameters).

```java
class SuperClass {
    public void method(int x) {
        System.out.println("SuperClass method(int)");
    }
}

class SubClass extends SuperClass {
    // Overriding the inherited method
    @Override
    public void method(int x) {
        System.out.println("SubClass method(int)");
    }

    // Overloading the inherited method
    public void method(int x, int y) {
        System.out.println("SubClass method(int, int)");
    }
}
```

##### Method Hiding

- A static method cannot be overridden.
- If a subclass implements the same static method as its parent, this is known as ==_method hiding_==.
    - It replaces the parent method in the calls defined in the child class.
    - Unlike method overriding, it is resolved at compile time based on reference type (not object type).

```java
class Parent {
    static void hi() {
        System.out.println("Static Method in Parent");
    }
}

class Child extends Parent {
    static void hi() {
        System.out.println("Static Method in Child");
    }
}

public class Main {
    public static void main(String[] args) {
        Parent p = new Child();

        p.hi();     // Calls Parent's static method

        Child.hi(); // Calls Child's static method
    }
}
```

### Inheritance

- A mechanism in which an object acquires all the properties and behaviors of a parent object.
- Existing attributes and methods get _inherited_.
- The existing class is usually known as a superclass or parent class or base class.
- The newly-created class is usually known as a subclass or child class or derived class.
- When we extend a class, we form an "IS-A" relationship.
- Allows us to reuse our code by basing a new object or class on an existing one.
- **Benefits**
    - Method overriding (Runtime polymorphism)
    - Code Reusability
- Can be single, where a class is derived from a one superclass (e.g. Java), or multiple, where a class is derived from multiple super classes (e.g. Python).

```java
class Car {
    protected String make;
    protected String model;
    protected int year;

    public Car(String make, String model, int year) {
        this.make = make;
        this.model = model;
        this.year = year;
    }

    public void drive() {
        System.out.println("Driving...");
    }
}

class Battery {
    private int size;

    public Battery() {
        this.size = 100;
    }

    public Battery(int size) {
        this.size = size;
    }

    public void describeSize() {
        System.out.print("The car has " + this.size + " kWh battery.");
    }
}

class EV extends Car {
    private int range;
    private Battery battery;

    public Car(String make, String model, int year) {
        super(make, model, year);
        this.range = 300;
        this.battery = new Battery();
    }

    public void drive() {
        System.out.println("Quietly Driving...");
    }

    public Battery getBattery() {
        return this.battery;
    }

    public void describeRange() {
        System.out.print("On a full charge, the " + this.year + " " + this.make + " " + this.model + " has a " + this.range + "mi range.")
    }
}
```

```java
EV rivian = new EV("Rivian", "R1T", 2022);

rivian.drive();
rivian.describeRange();
rivian.battery.describeSize();
```

> [!note]
> In Java, when an instance of a subclass is created, an instance of parent class is also created implicitly which is referred by `super` reference variable.

### Encapsulation

- Can be expressed in two ways:
    - Ability to _encapsulate_ state (attributes) and behavior (methods) into a single unit - an object.
    - Ability of an object to hide parts of its internal workings from external code.
        - e.g. using access modifiers
- **Benefits**
    - Control over data
    - Easy to test

## Composition

- OOP also involves **composition**, which defines a "HAS-A" relationship by storing an object as a variable inside another object.
    - e.g. an object of type `Car` can be assigned as a variable in an object of type `Person` to establish that `Person` has a `Car`.

## OOD

### UML Diagrams

- UML (Unified Modeling Language) diagrams are visual representations used in [[Object-Oriented Programming|OOP]] to design and document software systems.
- They help developers and stakeholders understand the structure, behavior, and relationships within a system.

> [!example]- UML Diagrams Cheat Sheet
> ![UML](assets/images/ood.uml-diagrams.png)
> **Source**: Khalil Stemmler

> [!example]- UML Diagram Example
> ![UML](assets/images/ood.uml-diagrams-example.png)
> **Source**: Lucid Software

---

## Further

### Reads 📄

- [Object-Oriented Programming: A Primer](https://aigents.co/data-science-blog/publication/object-oriented-programming-a-primer)
