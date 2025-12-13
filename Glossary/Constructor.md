- A constructor is a function that gets called at the time of creating an object instance from a class.

```java
public class Person {
    private String name;
    private int age;

    // Constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Person john = new Person("John Doe", 35);
    }
}
```
