- In the context of [[Object-Oriented Programming]], static properties and methods are part of the class itself rather than instances of the class.
- They provide a way to define behavior and data that is shared across all instances.

- **Static Methods**
    - Can be called either directly on a class or on an instance of a class.
    - Can access and modify static variables of the class.
    - Can only directly call other static methods.
    - Can't access or modify non-static (instance) variables or methods of the class, as they are not associated with any instance.
    - Can't refer to the `this` or `super` keywords in any way.
    - Are typically used for utility or helper functions that do not require any instance-specific data.

- **Static Variables**
    - Only one copy of the static variable shared among all instances of the class.
    - Are initialized only once when the class is loaded into memory.
    - Can be accessed and modified by both static and non-static methods of the class.
    - Are often used for maintaining state or data that is shared across all instances of the class, such as a counter or a configuration option.

## Examples

**Python**

```python
class Car:
    @staticmethod
    def drive():
        print("Vroooom!")


Car.drive()
Car().drive()
drive()
```

**C#**

```cs
class Car {
    public static void drive() {
        System.Console.WriteLine("Vroooom!");
    }
}

class Program {
    static void Main() {
        Car.drive();
    }
}
```
