## Fundamentals

- Java is a general-purpose, class-based, [[Object-Oriented Programming]] language.
    - [[Statically-Typed Language|Strongly Typed]],
    - Platform-independent
    - Multi-threaded
- It combines the power of [[Compiled Language|compiled]] languages with the flexibility of [[Interpreted Language|interpreted]] languages.
- **Toolkit**
    - **JDK (Java Development Kit)** includes tools like Java compiler (`javac`), Java runtime (JRE), `javadoc` etc.
    - **JRE (Java Runtime Environment)** provides classes, libraries and the JVM needed to run Java programs.
    - **JVM (Java Virtual Machine)** is responsible for executing bytecode generated from Java source code.
        - Provides portability - Write Once, Run Anywhere (WORA)
        - Java Code (`.java`) -> Compiler (`javac`) -> Byte Code (`.class`) -> JVM -> JRE
            - `javac App.java` (Compile) -> `java App` (Execute)
    - **JShell** is the command line REPL for Java.

```java
// HelloWorld.java
public class HelloWorld {
	/* Entry Point of a Java Program */
	public static void main(String[] args) { /* ... */ }
}
```

### Primitives Types

- **Integers**
    - `byte` (1 byte)
    - `short` (2 bytes)
    - `int` (4 bytes)
    - `long` (8 bytes)
- **Characters** (Unicode)
    - `char` (2 bytes)
- **Floating Point Numbers**
    - `float` (4 bytes)
    - `double` (8 bytes)
- **Boolean**
    - `boolean` (`true` or `false`)

```java
byte a = 12;
short b = 89;
int dec = 9;
int bin = 0b101;
int hex = 0x7E;
long d = 345l;

char c = 'c';
c++; // 'b'

float num1 = 2.5f;
double num2 = 2.5;
double num3 = 2.5d;
double num4 = 2e10;

boolean isTrue = true;
```

> [!note]
>
> - Numeric ranges of a certain type are from $-2^{(n - 1)}$ to $-2^{(n - 1)} - 1$, where `n` is the number of bits.
> - To increase readability, numbers can be separated with `_`; trailing zeros can be shortened using exponentiation. e.g. `1_000_000`

- **Operators**
    - **Arithmetic**: `+`, `-`, `/`, `*`, `%`, `++`, `--`
    - **Comparison**: `<`, `<=`, `>`, `>=`, `==`, `!=`
    - **Logical**: `&&`, `||`, `!`

> [!note]
> [[Prefix vs. Postfix Increment|Prefix & postfix increment]] operations behave similarly to that of [[JavaScript]].

| Operators            | Precedence              |
| -------------------- | ----------------------- |
| postfix              | a++ b--                 |
| unary                | ++a --b +c -d ~ !       |
| multiplicative       | \* / %                  |
| additive             | + -                     |
| shift                | << >> >>>               |
| relational           | < > <= >= instanceof    |
| equality             | == !=                   |
| bitwise AND          | &                       |
| bitwise exclusive OR | ^                       |
| bitwise inclusive OR | \|                      |
| logical AND          | &&                      |
| logical OR           | \|                      |
| ternary              | ? :                     |
| assignment           | = += -= \*= /= %= &= ^= |

#### Wrapper Objects

- Special classes that "wrap" around primitive data types.
- Provide an object representation of those primitive types.
- Are [[immutable]].
- Allow primitive data types to be treated as objects, enabling them to be used in contexts where objects are required, such as in collections, generics, and method parameters.
- Purposes:
    - Provide object representation of primitive data types, allowing them to be used in OOP contexts e.g. in collections such as `ArrayList` and `HashMap`, and for use in generics.
    - Provide utility methods for converting between primitive types and their corresponding wrapper class objects (e.g., `Integer.parseInt()`, `Double.valueOf()`, etc.).
    - Provide nullability - a way to represent null values for primitive types.
        - This isn't available **in** primitive types.
    - Provide constants and methods related to the respective primitive data type (e.g., MIN_VALUE, MAX_VALUE, etc.).

- Java provides eight wrapper classes, one for each primitive data type:
    - `Boolean` for `boolean`
    - `Byte` for `byte`
    - `Character` for `char`
    - `Short` for `short`
    - `Integer` for `int`
    - `Long` for `long`
    - `Float` for `float`
    - `Double` for `double`
- Java also provides automatic boxing and unboxing mechanisms to simplify the conversion between primitive types and their corresponding wrapper class objects.

> [!note]
> **Boxing** is the conversion of a primitive value to its wrapper class object, while unboxing is the conversion of a wrapper class object to its primitive value. Java does _autoboxing_ in which it automatically performs boxing implicitly.

```java
// Boxing (Explicit)
Integer intObj = Integer.valueOf(42);
// OR Integer intObj = 42; (autoboxing)

// Unboxing (Explicit)
int primitiveInt = intObj.intValue();
// OR int primitiveInt = intObj; (autounboxing)

/* --------------------- */

Integer maxInt = Integer.MAX_VALUE;                // 2147483647
String binaryString = Integer.toBinaryString(42);  // 101010

/* --------------------- */

List<Integer> intList = new ArrayList<>();
intList.add(10);                  // Autoboxing
intList.add(Integer.valueOf(20)); // Explicit Boxing

for (Integer num : intList) {
	System.out.println(num);   // Autounboxing
}

/* --------------------- */

int parsedInt = Integer.parseInt("42");           // 42
double parsedDouble = Double.parseDouble("3.14"); // 3.14
```

### Non-Primitive Types

- aka Reference Types
- All reference types are instances of classes.
- Are nullable, thus can be assigned a value of `null`.
    - `null` represents an absence of value.
    - Accessing a `null` reference value will compile without errors, but will throw a runtime unchecked exception (`NullPointerException`).
- Inherit from the root `Object` class.
- When a reference type is assigned, a new object is created in the heap, and its memory address is stored in the reference variable.
    - Actual objects are stored in the heap memory area.
    - References are stored in the stack memory.
- When passed to a method, a copy of the reference is passed, not the actual object.
    - i.e. changes made to the object through the reference in the method will persist outside the method.
- The `==` operator compares if two references point to the same object in memory.
    - `equals()` method is used to compare the content of objects.
- A `NullPointerException` is thrown when trying to access a reference variable which is `null` but requires an object.

```java
int[] nums = null;

nums.Length;  // NullPointerException

if(arr != null) {
	System.out.println(arr.length);
} else {
	/* ... */
}
```

#### Strings

- By default, strings are [[immutable]] in Java.
- In Java, `String` doesn't use any null character for termination.
    - It is backed by character array.

```java
// Strings
String firstName = new String("John");
String lastName = "Doe";
```

- When Strings are created they are placed in a special location within the heap called the _String Pool_.
    - Two string variables that are created using String literals and contain the same value have the same reference, i.e. there are no duplicate values stored in the heap.

```java
String s1 = "Java";
String s2 = "Java";

System.out.print(s1 == s2); // true
```

- **Mutable strings** can be created using `StringBuffer` or `StringBuilder`.

> [!note] `StringBuffer` vs. `StringBuilder`
> There are no significant difference between `StringBuffer` & `StringBuilder`, except that `StringBuffer` is thread-safe while `StringBuilder` isn't. Because of that, `StringBuffer` is slower.

```java
StringBuffer s = new StringBuffer("Java");
```

#### Arrays

- In Java, arrays are [[Static Arrays|fixed-size]], and [[homogeneous]].

```java
// Creating Arrays
int[] nums = new int[3];           // [0, 0, 0]
boolean[] bools = new boolean[2];  // [false, false]
String[] strs = new String[2];     // [null, null]

int[] nums2 = {1, 2, 3, 4, 5};
int[] nums3 = new int[]{1, 2, 3, 4, 5};
String[] strs2 = {"John", "Jane"};

// Position of Square Brackets
int nums[] = {1, 2, 3};   // Also Valid Syntax

// 2D Array
int[][] matrix = new int[3][4];

// Jagged Array
int[][] jaggedArray = new int[2][];
jaggedArray[0] = new int[7];
jaggedArray[1] = new int[4];

// 3D Array
int[][][] matrix = new int[3][4][5];
```

#### Custom Class Objects

```java
Person john = new Person("John Doe");
Person jane = john;

john.name = "Jane Doe";

System.out.print(john.name);  // Jane Doe
System.out.print(jane.name);  // Jane Doe
```

### Type Casting / Conversion

- **Primitive Type Casting**
    - Type casting can be lossy.
        - e.g. A float casted to an integer will lose its decimal points.
    - **Widening Casting** (automatic) - convert a smaller type to a larger size type.
        - `byte` -> `short` -> `char` -> `int` -> `long` -> `float` -> `double`
    - **Narrowing Casting** (manual) - convert a larger type to a smaller size type.
        - `double` -> `float` -> `long` -> `int` -> `char` -> `short` -> `byte`

```java
byte a = 127;
int b = 14;

// Implicit (Conversion)
a = b; // ⛔
b = a; // ✅

// Explicit (Casting)
a = (byte)b;
```

- **Type Promotion**

```java
byte a = 10;
byte b = 20;

int c = a * b; // 200 (Outside byte range)
```

#### Object Type Casting

- Upcasting and downcasting allow for flexible and polymorphic behavior when dealing with objects of different class types.

- **Upcasting** - Casting an object of a subclass to its parent or superclass type.
    - Allows an instance of a subclass access to the methods defined in the superclass. However, methods specific to the subclass cannot be accessed through the upcast reference.
    - Implicit and safe operation that does not require an explicit cast.

```java
class Vehicle {
	void drive() {
		System.out.println("Driving a vehicle...");
	}
}

class Car extends Vehicle {
	void accelerate() {
		System.out.println("Speeding up a car...");
	}
}
```

```java
public class Main {
	public static void main(String[] args) {
		Car car = new Car();     // Object of subclass
		Vehicle vehicle = car;   // Upcasting - Treating Car as Vehicle
		// vehicle.drive();      // ✅, drive() present in Vehicle
		// vehicle.accelerate(); // ⛔, accelerate() specific to Car

		goForARide(vehicle);
		goForARide(car);

		/*
		Output:
			Driving a vehicle...
			Driving a vehicle...
		*/
	}

	public static void goForARide(Vehicle v) {
		v.drive();
	}
}
```

> [!important]
>
> - The _type_ of variable determines which methods can be called.
>     - e.g. `drive()` can be called on any `Vehicle`.
> - The specific type of the object a variable is referring to determines which specific implementation of a method will be used when it's called.
>     - e.g. If `drive()` has been overridden from the subclass `Car`, `Car`'s implementation of `drive()` will be used at run time.

- **Downcasting** - Casting an object of a superclass type to its child or subclass type.
    - An explicit operation that requires an explicit cast.
        - Can lead to runtime exceptions if not performed correctly.
    - Should be performed with caution, as it can lead to a `ClassCastException` if the object being downcast is not an instance of the target subclass type.
        - It is recommended to use the `instanceof` operator to check the object's type before downcasting.

```java
public class Main {
	public static void main(String[] args) {
		Vehicle vehicle = new Car();  // Upcasting
		Car car = (Car) vehicle;      // Downcasting
		// car.drive();               // ✅, drive() present in Vehicle
		// car.accelerate();          // ✅, accelerate() specific to Car

		goForARide(vehicle);
		goForARide(car);

		/*
		Output:
			Driving a vehicle...
			Speeding up a car...
			Driving a vehicle...
			Speeding up a car...
		*/
	}

	public static void goForARide(Vehicle v) {
		v.drive();

		if (v instanceof Car) {
			Car c = (Car) v;
			c.accelerate();
		}
	}
}
```

---

> [!note]
>
> - Primitives are predefined in Java, while non-primitives are not (except for `String`).
>     - They are created by the developer.
> - Non-primitives have methods for performing certain operations, while primitives don't.
> - A primitive always has a value, while a non-primitive can be `null` (absence of value).
> - A primitive type starts with a lowercase letter, while a non-primitive type starts with an uppercase letter.

> [!important] [[Pass by Reference vs. Pass by Value|Pass by Reference or by Value]]
> Java always passes parameters by value, not by reference. When a parameter is passed to a method, a copy of the value is passed, not a reference to the original variable.

### Control Flow

- As with [[JavaScript]], it's possible to define additional scopes anywhere using `{}`.

```java
void test() {
	{
		int num = 0;
	}

	num++; // Compiler Error, num is out of scope
}
```

**If/Else**

```java
if (condition) {
	/* Code Block */
} else if (anotherCondition) {
	/* Code Block */
} else {
	/* Code Block */
}
```

- As with [[JavaScript|JS]], curly brackets are optional for single-line expressions inside conditionals.

**Switch Statements**

- Variables used in a `switch` statement can only be convertible integers (byte, short, int, char), strings and enums.
- `switch` statements support *fall-through* logic.
    - Whatever case is met first, all other cases below it will execute unless `break` is used to exit a particular case.

```java
switch (condition) {
	case caseOne:
		// Code Block
		break;
	case caseTwo:
		// Code Block
		break;
	default:
		// Code Block

switch (condition) {
	case caseOne: {
		// Code Block
		break;
	}
	case caseTwo: {
		// Code Block
		break;
	}
	default: {
		// Code Block
	}
}
```

**Ternary Operator**

```java
int num = (condition) ? expressionTrue : expressionFalse;
```

**For**

```java
// for (initialization; boolean expression; optional body) {}
for (int i = 0; i < nums.length; i++) { /* Code */ }

// Enhanced For Loop
for (int num : nums) { /* Code */ }
```

**While**

```java
while (condition) { /* Code */ }
```

**Do While**

```java
do {
	/* Code */
} while (condition);
```

- Java provides a way to branch in an arbitrary and unstructured manner.
    - Labels are used to identify a block of code.
    - In addition to exiting a loop or terminating a `switch` statement, `break` is used as a form of goto to navigate to specific sections of code.

```java
first:
	for (int i = 0; i < 3; i++) {
		second:
			for (int j = 0; j < 3; j++) {
				if (i == 1 && j == 1) {
					break first;
				}
				System.out.println(i + " " + j);
			}
	}
```

### Exceptions

- Types of errors:
    - **Compilation errors** - detected by the compiler & prevent program compilation. e.g. syntax errors
    - **Run-time errors** (exceptions) - occur during program execution.
    - **Logical / semantic errors** - caused by incorrect logic.

> [!note]
> Exceptions are never thrown during the compilation process - they can only be thrown when the code is executing (running).

```java
int a = 0;
int b = 0;

try {
	b = 10/0;
} catch(ArithmeticException e) {
	System.out.print("Can't Divide By Zero!");
} catch(AnotherSpecificException e) {
	System.out.print("A Specific Exception Occured!");
} catch(Exception e) {
	System.out.print("Something went wrong!");
}
```

- Only critical statements (statements likely to throw a run-time error) should be placed inside the `try` block of a `try`/`catch`.

- It's also possible to add an optional `finally` block.
    - Typically used for cleanup tasks, such as closing resources like files, database connections, or network sockets.
    - It is considered a good practice to use it when appropriate to ensure proper resource cleanup.
    - There can be only one `finally` block for each `try` block, but there can be multiple `catch` blocks.
    - The `finally` block will not be executed if the JVM exits while the `try` or `catch` code is being executed, such as when the `System.exit()` method is called.
- If the resources used in a `try` block implement the `AutoClosable` interface, try-with-resources feature can be used to automatically close the resources after the execution of the block. This removes the need for a `finally` block.
    - Multiple resources can be declared in a try-with-resources block by separating them with a semicolon inside `try()`.
    - Final & effectively final variables declared outside `try()` can be used inside a try-with-resources block.

```java
/* Try-Catch-Finally */
Scanner s = null;
try {
	s = new Scanner(new File("file.txt"));
	while (s.hasNext()) {
		System.out.print(s.nextLine());
	}
} catch (FileNotFoundException e) { /* Catch Block Code */ }
finally {
	if (scanner != null) {
		scanner.close();
	}
}

/* Try-with-Resources */
try (Scanner s = new Scanner(new File("file.txt"))) {
	while (s.hasNext()) {
		System.out.print(s.nextLine());
	}
} catch (FileNotFoundException fnfe) { /* Catch Block Code */ }
```

> [!note]
> `throw` is used to invoke an exception explicitly.

```java
int a = 0;
try {
	a = 0/10;

	if (a == 0) {
		throw new ArithmeticException("Quotient is Zero!");
	}
} catch (ArithmeticException e) {
	System.out.print(e);
}
```

#### Exception Hierarchy

- The `Exception` superclass is a member of `java.lang`.

> [!quote]- Exception Hierarchy Diagram
> ![Exception Hierarchy](java.exception-hierarchy.jpg)
> **Source**: Javatpoint

#### Checked Exceptions

- Checked by the compiler at compile-time.
- Handling these exceptions using `try-catch` blocks or _ducking_ them (by declaring them in the method signature using the `throws` clause) is required.
    - If not handled, the compiler cannot proceed with the compilation of code, resulting in a _compilation error_.
- Not derived from the `RuntimeException` class.
- Examples: `IOException`, `SQLException`, `ClassNotFoundException`, etc.

```java
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;

public class CheckedExceptions {
	public static void main(String[] args) {
		try {
			readFile("file.txt");
		} catch (FileNotFoundException e) {
			System.out.println("File Not Found: " + e.getMessage());
		} catch (IOException e) {
			System.out.println("IO Error: " + e.getMessage());
		}
	}

	public static void readFile(String fileName) throws FileNotFoundException, IOException {
		FileInputStream fis = new FileInputStream(fileName);
		// Do File Reading & Processing
		fis.close();
	}
}
```

#### Unchecked Exceptions

- Are not checked by the compiler at compile-time.
- Handling these exceptions or declaring them in the method signature is not required.
- Derived from the `RuntimeException` class.
- Examples: `NullPointerException`, `ArrayIndexOutOfBoundsException`, `IllegalArgumentException`, `RuntimeException`, etc.

```java
public class UncheckedExceptions {
	public static void main(String[] args) {
		int[] numbers = {1, 2, 3};

		try {
			int result = divideByZero(10, 0);
			System.out.println("Result: " + result);

			// ArrayIndexOutOfBoundsException
			int value = numbers[3];
		} catch (ArithmeticException e) {
			System.out.println("Error: " + e.getMessage());
		}
	}

	public static int divideByZero(int a, int b) {
		// ArithmeticException
		return a / b;
	}
}
```

#### Custom Exceptions

- Useful for
    - adding specific attributes or methods to the exception.
    - grouping and differentiating application-specific errors.
    - providing more context about the error than standard exceptions offer.
- Can be created by extending the `Exception` superclass or one of its subclasses.
    - Extending `Exception` creates a checked exception.
    - Extending `RuntimeException` creates an uchecked exception.

```java
class MyException extends Exception {
	public MyException() {
		super();
	}

	public MyException(String message) {
		super(message);
	}

	public MyException(String message, Throwable cause) {
		super(message, cause);
	}
}
```

#### `throws`

- Used to _duck_ exceptions.
- Appended to the method signature to declare that a method can throw one or more exceptions.
- Specifies the type of exceptions that a method might throw during its execution.
- The responsibility of exception handling is passed onto the calling method.
    - The method that calls a method which `throws` an exception must either
        - handle the exception using a `try-catch` block, or
        - declare that it `throws` the exception as well.

```java
public static void readFile(String fileName) throws FileNotFoundException {
	FileReader reader = new FileReader(fileName);
}

public static void main(String[] args) {
	try {
		readFile("file.txt");
	} catch (FileNotFoundException e) {
		System.out.println("Exception caught: " + e.getMessage());
	}
}
```

#### `Error`

- Represents a serious problem that the application should not try to handle, such as `OutOfMemoryError` or `StackOverflowError`.
- Typically not handled by the application but rather by the JVM.

### Debugging

- **Assertions**
    - Monitor a program's state, and when things go wrong, they terminate the program in a fail-fast manner (causing immediate failure).
    - Should be used only in development to debug code.
    - If the boolean expression after `assert` evaluates to false, an error is thrown.
    - The program must be run using the `-ea` flag. (`java -ea App`)

```java
class People {
	public static void main(String[] args) {
		Person p = new Person("John", -1);
	}
}

class Person {
	String name;
	int age;

	public Person(String name, int age) {
		assert (age >= 0) : "Invalid Age";
		this.name = name;
		this.age = age;
	}
}
```

- Modern IDEs come with some debugging features built in.
    - Breakpoints are used to stop program execution at certain points in the execution of the program. We can then continue its execution by stepping through the program.

## OOP

- [[Object-Oriented Programming]] 📄

### Classes

```java
// Person.java
class Person {
	// Instance Variables
	String firstName;
	String lastName;
	boolean isAdult;

	// Static Variable
	static String role;

	// Constructor
	Person(String fName, String lName, boolean isAdult) {
		this.firstName = fName;
		this.lastName = lName;
		this.isAdult = isAdult;
	}

	// Method
	public void greet() {
		System.out.print("Hello, my name is " + this.firstName + ".");
	}
}
```

```java
// Main.java
public class Main {
	public static void main(String[] args) {
		Person john = new Person("John", "Doe", true);
		Person.role = "USER";
	}
}
```

- Unlike methods, constructors in Java
    - Are invoked implicitly.
    - Must have no explicit return type.
    - Must have the same name as the class name.
    - Can't be `abstract`, `static`, `final`, and synchronized.
- If a class has _no_ constructors defined, the java compiler creates a default one with no arguments.
    - If any kind of constructor is implemented, a default constructor is not provided.

> [!important] Naming Files
>
> - There can be only one public class per source code file.
> - If there is a public class in a file, the name of the file must match the name of the public class.
>     - For example, a class declared as `public class Person { }` must be in a source code file named Person.java.
> - This also applies towards interfaces.

> [!note] Keywords
>
> - The `new` keyword is required to dynamically allocate memory for objects at runtime.
>     - It is used to create instances of both regular classes and array objects.
>     - In Java, all class objects are dynamically allocated.
> - The `this` keyword is not necessary to access properties and methods within a class as long as there's no ambiguity (e.g. naming conflicts).
>     - `this` is a **reference variable** that refers to the current object.
>         - The compiler adds `this` by default if not provided in code.

- Any class that doesn't have an `extends` clause implicitly inherits `Object`.
- `Object` provides the following methods:
    - `toString()`
    - `equals()`
    - `hashCode()`
    - `finalize()` - called by the garbage collector when the object is destroyed.
- When overriding `equals()`, we must ensure that the method is:
    - reflexive - `obj.equals(obj) == true`
    - symmetric - `obj1.equals(obj2) == obj2.equals(obj1)`
    - transitive - if `obj1.equals(obj2)` and `obj2.equals(obj3)`, then `obj1.equals(obj3)`
    - consistent
- If `equals()` is overridden, `hashCode()` must also be overridden.

- **Immutable Classes**
    - The state of an instance object cannot be changed once it is created.
    - Some examples of immutable classes:
        - `java.lang.String`
        - Wrapper classes like `Integer`, `Long`, `Double`, etc.
        - `java.math.BigInteger` and `java.math.BigDecimal`
        - Unmodifiable collections like `Collections.singletonMap()`
        - Java 8 Date Time API classes like `LocalDate`, `LocalTime`, etc.
    - Guidelines for creating immutable classes:
        - The class should be declared as final so it cannot be extended.
        - All fields should be private and final so they can only be initialized once.
        - Setter methods shouldn't be provided to change the object state.
        - Only getter methods should be provided that return copies of mutable fields to avoid direct access.
        - A constructor should be used to initialize all the fields.
    - Key benefits of immutable classes:
        - **Predictability** - The state of the object will never change
        - **Thread-safety** - Immutable objects are inherently thread-safe
        - **Cacheability** - Results can be cached since the state never changes
        - **Simplicity** - Immutable objects are simpler to construct, test, and use

### Access Modifiers

> [!important]
> The order of access modifiers from most restrictive to least restrictive is: private, default, protected, public.

- ==`private`==
    - Restricts access of members to only the class itself.
    - This provides the highest level of encapsulation and data hiding.

- ==Default Access==
    - If an access modifier isn't specified, the method's visibility is limited to the package it's defined in.
        - This is more restrictive than `public`, but less restrictive than `private` or `protected`.
    - The method will have the default "package-private", "private-protected" or "friendly" access level, which means the method is accessible within the same package (the package where the class is defined), but not from other packages, even if those packages contain subclasses of the class containing the method.

- ==`protected`==
    - Makes members accessible within the same package and to subclasses of the class in other packages.
    - This is used to achieve inheritance across packages.

- ==`public`==
    - Makes members accessible from anywhere, both within the same package and from different packages.
    - Public classes, interfaces, and members form the public API of a library or application

> [!note] Noteworthy
>
> - Access modifiers cannot be applied to local variables within methods, but they can be applied to classes, interfaces, variables, methods, and constructors.
> - In Java, it's not required to explicitly declare the access modifier of methods.
> - In general, it's considered good practice to explicitly specify the intended access level for methods (and other class members) using the appropriate access modifier keywords.
> - The choice of access modifier depends on the level of encapsulation and accessibility required for a particular member. It's generally recommended to use the most restrictive access modifier that meets the requirements, following the principle of least privilege.

### Non-Access Modifiers

- **==`static`==**
    - The `static` keyword can be used to define [[static properties & methods]].
    - To initialize static properties of a class, we can do so in a `static` block.
    - Static block and static variables are executed in the order they are present in a program.

```java
class Person {
	/* ... */
	static String role;

	static {
		role = "USER";
	}

	public static void printRole() {
		System.out.print("Role: " + role);
	}

	/* ... */
}
```

```java
class Vehicle {
	static String brand = findBrand();

	static {
		System.out.println("Static Block");
	}

	static String findBrand() {
		System.out.println("findBrand()");
		return "Generic";
	}

	public static void main(String[] args) {
		System.out.println("Main method");
	}

	/*

	Execution Order:
	- findBrand()
	- Static Block
	- Main method

	*/
}
```

- **==`abstract`==**
    - An abstract method is a method that is declared without an implementation, using the `abstract` keyword.
    - Abstract classes provide a base class that can be extended by subclasses, allowing for code reuse and the enforcement of a common interface or behavior.
    - If a class has any abstract methods, the class must be declared as abstract.
    - Subclasses of an abstract class must either provide implementations for all abstract methods or be declared as abstract themselves.
    - Abstract classes can have both abstract and non-abstract (concrete) methods. They can also have constructors, instance variables, and static methods.
    - Since Abstract classes are meant to be extended, they cannot be `final`.
    - Classes that have full implementation for all of their methods, including any abstract methods inherited from superclasses or interfaces are known as _concrete classes_.
        - Concrete classes can be instantiated directly using `new.

> [!important]
> Abstract classes cannot be instantiated directly, but they can be subclassed (extended).

```java
// Abstract Class
abstract class Vehicle {
	protected String model;

	public Vehicle(String model) {
		this.model = model;
	}

	public abstract double getFuelEfficiency();
}

// Concrete Class
class Car extends Vehicle {
	private double fuelEfficiency;

	public Car(String model, double fuelEfficiency) {
		super(model);
		this.fuelEfficiency = fuelEfficiency;
	}

	@Override
	public double getFuelEfficiency() {
		return fuelEfficiency;
	}
}

public class Main {
	public static void main(String[] args) {
		Car car = new Car("Camry", 45.6);

		System.out.println("Car fuel efficiency: " + car.getFuelEfficiency());
	}
}

```

### Other Keywords

- **==`this()`== & ==`super()`==**
    - By default, when instantiating an object of a subclass, both constructors of the subclass and the superclass are called.
        - When an instance of a subclass is created, an instance of parent class is also created implicitly which is referred by `super` reference variable.
        - Either a `super()` or a `this()` call **must** be the first line in a constructor.
    - `super` is a reference variable which is used to refer immediate parent class object.
        - It can be used:
            - to refer immediate parent class instance variable. (e.g. `super.firstName`)
            - to invoke immediate parent class method. (e.g. `super.getFirstName()`)
    - `super()` is always executed first thing in a constructor and it calls the constructor of a super class (if one exists).
        - When called explicitly, `super()` calls the constructor of the super class whose parameters match to the ones passed to it.
    - `this()` executes the constructor of the same class with matching parameter list (similar to `super()`).

```java
class X {
	public X () {
		System.out.print("Called from X");
	}
	public X (int n) {
		System.out.print("Called from X: " + n);
	}
}

class Y extends X {
	public Y () {
		System.out.print("Called from Y");
	}
	public Y (int n) {
		super(n);
		System.out.print("Called from Y: " + n);
	}
}
```

```java
Y point1 = new Y();
/*
Called from X
Called from Y
*/

Y point2 = new Y(5);
/*
Called from X: 5
Called from Y: 5
*/
```

> [!important]
> Every class in Java extends `Object`. That means it inherits methods defined on `Object` such as `toString()` (which is called when printing an instance of a class) and `equals()`.

- **==`final`==**
    - can be used with a variable, a method or a class.
        - Marking a class `final` makes it so that it _can't be inherited or extended_.
        - Marking a method `final` makes it so that it _can't be overridden_.
        - Marking a variable `final` makes it constant so that it _can't be reassigned_.
    - Performing those tasks on `final` variables, methods and classes causes a compile-time error.
    - An uninitialized final variable can only be initialized in a constructor.
    - The position of the `final` keyword does not matter with respect to access modifiers.

```java
public final class FinalClass { /*...*/ }
final public class FinalClass { /*...*/ }

public final void finalMethod() { /*...*/ }
final public void finalMethod() { /*...*/ }

public final int finalVar = 42;
final public int finalVar = 42;
```

### Interfaces

- Blueprints or contracts that define a set of abstract methods and constants.
    - Specifies the behavior of a class without providing the implementation details.
    - Declared using the `interface` keyword.
    - Can only contain `abstract` methods, which are methods without any implementation.
        - Can also have default and static methods (Java 8), and `private` and `private static` methods (Java 9).
        - All the methods in an interface are implicitly public and abstract.
    - Can only have static final variables (constants), and all variables are implicitly `public`, `static`, and `final`.
        - i.e. they have to be assigned a value at definition.
    - Used to achieve abstraction.
    - Allow a class to implement multiple interfaces, providing a way to achieve [[multiple inheritance]], which is not possible with classes.
    - Promote loose coupling between classes by defining a contract that classes must follow, without specifying the implementation details.
    - Cannot be instantiated directly.
        - They are _implemented_ by classes, which provide the actual implementation of the abstract methods.
    - Using `default`, a method can have a default implementation that is used if it's not overridden.

```java
public interface Printable {
	void print(String message);

	default void printUppercase(String message) {
		System.out.println(message.toUpperCase())
	}
}

public class Printer implements Printable {
	@Override
	public void print(String message) {
		System.out.println("Printing: " + message);
	}
}

public class Main {
	public static void main(String[] args) {
		Printable printer = new Printer();
		printer.print("Hello, World!");
	}
}
```

- An interface can extend multiple other interfaces.
    - It inherits the abstract method definitions from all the parent interfaces.
    - A class that implements the child interface must then implement all the methods defined in the parent interfaces as well as the child interface.

```java
interface AddCalc {
	void add();
}

interface SciCalc extends AddCalc, SubCalc { /*...*/ }

class MyClass implements SciCalc { /*...*/ }
```

- It's possible for a class to implement multiple interfaces with identical method definitions.

```java
interface X {
	void commonMethod();
}

interface Y {
	void commonMethod();
}

class MyClass implements X, Y {
	@Override
	public void commonMethod() {
		System.out.println("Common Method Implemented!");
	}
}
```

- Unlike abstract classes, adding new methods to an interface (using default or static methods) maintains backward compatibility, as existing implementing classes do not need to be modified.

> [!important]
>
> - By default, a class that doesn't full implement an interface is an abstract class.
> - A class cannot implement multiple interfaces with the same methods having the same signature but different return types. It results in an error. But, it's possible to implement interfaces with methods of the same name and return type but different parameter list.

- **Marker Interface**
    - Contains no methods.

#### **Functional Interface**

- Contains exactly one abstract method.
- Also known as Single Abstract Method (SAM) interfaces.
- Can be annotated using `@FunctionalInterface`.
- Enable a more [[Functional Programming]] style in Java.
- Allow for the use of lambda expressions and method references to provide implementations of the SAM.

### Abstract Classes vs. Interfaces

- Both cannot be instantiated.
- Abstract classes can have both abstract methods (without implementation) and concrete methods (with implementation), while interfaces can only have abstract methods (before Java 8), but since Java 8, they can also have default and static methods.
- Abstract classes can have instance variables and can have any access modifier, while Interfaces can only have static final variables (constants), which are implicitly `public`, `static`, and `final`.
- A class can extend only one abstract class, but it can implement multiple interfaces.
    - Interfaces support multiple inheritance, while classes do not.
- Adding new methods to an abstract class can break existing subclasses, because they need to implement the new methods.
    - Adding new methods to an interface (using default or static methods) maintains backward compatibility; Existing implementing classes do not need to be modified.
- Abstract classes are used when you want to provide some common functionality and state, and allow subclasses to extend and override the behavior. Interfaces are used to define a contract or a set of methods that a class must implement, without any implementation details.
- Abstract classes can have `final` methods.

### Anonymous Objects

- Anonymous objects are instances of a class that aren't assigned to a variable.

```java
new Person();

new Person("John").greet();
```

### Inheritance

- If an instance variable in a superclass has `public`, `protected`, or default (no modifier) access, a subclass can directly access and use that variable (without `this`).
- The `super` keyword can also be used to access instance variables of a superclass, even if they are hidden by variables with the same name in the subclass.

```java
class SuperClass {
	int x = 10;
}

class FirstSubClass extends SuperClass {
	void accessSuperClassVar() {
		System.out.println(x);
	}
}

class SecondSubClass extends SuperClass {
	// Hides variable from SuperClass
	int x = 20;

	void printX() {
		System.out.println(x);        // Prints 20
		System.out.println(super.x);  // Prints 10
	}
}
```

- While it is possible to have an empty subclass in Java, it is not valid to have an empty subclass that extends a superclass with a default parameterized constructor and does not provide a constructor itself.

```java
class Car {
	protected String make;

	public Car(String make) {
		this.make = make;
	}
}

// ⛔
class Gasoline extends Car {
	// 'super()' called by default, but there is no
	// constructor in Car matching its parameter signature
}

// ✅
class Gasoline extends Car {
	public Gasoline(String make) {
		super(make);
	}
}
```

- If a parent class and child class have an instance variable with the same name, the variable does not override the parent's variable. Instead, the subclass object has two separate instance variables - one inherited from the parent class and one defined in the subclass itself. The code accesses the appropriate variable based on the reference type.

> [!important] Multiple Inheritance
>
> - Java does not support [[multiple inheritance]] of classes.
>     - A class in Java cannot extend more than one class directly.
>     - This is to avoid [[the diamond problem]], where a subclass could inherit conflicting implementations of the same method from multiple parent classes.
> - However, Java supports multiple inheritance through interfaces.
>     - A class can implement multiple interfaces, effectively inheriting the methods declared in those interfaces.

```java
interface A {
	void method1();
}

interface B {
	void method2();
}

class C implements A, B {
	// Class C now has methods method1() and method2()
	// from interfaces A and B respectively
}

class C extends Z implements A, B { /*...*/ }
```

### Nested Classes

- A **nested class** or an **inner class** is a class that is a member of another class.
- The class that contains the inner class is called the outer class.
- Typically useful for creating helper classes, implementing callbacks, and organizing related code within a larger class structure.
- There are several types of nested classes in Java:
    - **Non-static Nested Class**
        - Not declared static.
        - Associated with an _instance_ of the outer class.
        - Can access all the static and non-static members (variables and methods) of the outer class, including private members.
            - This allows for better encapsulation and organization of related code.
        - To create an instance of an inner class, you need an instance of the outer class first.
    - **Static Nested Class**
        - Declared with the `static` modifier.
        - Are static members of the outer class.
        - Are not associated with an _instance_ of the outer class.
        - Can only access static members of the outer class, including private static members.
    - **Local Class** / **Method-Local Inner Class**
        - Defined within a method of the outer class.
        - Its scope is limited to the method in which it is defined.
            - Have access to the variables of the method, including local variables.
    - **Anonymous Inner Class**
        - A special type of nested class that does not have a name.
        - Defined and instantiated in the same expression.
        - Can be useful to create a single instance of a class that is modified such as overriding methods, without having to define a separate named class.
        - Has some limitations compared to named classes:
            - Cannot have constructors.
            - Cannot have static members, except for static final constants.
            - Cannot access local variables in their enclosing scope unless those variables are final or effectively final.

```java
public class OuterClass {
	private int outerVar = 10;
	private static int outerStaticVar = 20;

	// Non-static Nested Class
	class InnerClass {
		private int innerVar = 5;

		public void accessOuterVar() {
			System.out.println(outerVar);
		}
	}

	// Static Nested Class
	static class StaticNestedClass {
		public void accessOuterStaticVar() {
			System.out.println(outerStaticVar);
		}
	}

	// Local Inner Class
	public void methodWithInnerClass() {
		int localVar = 25;

		class LocalInnerClass {
			public void accessLocalVar() {
				System.out.println(localVar);
			}
		}

		LocalInnerClass lc = new LocalInnerClass();
		lc.accessLocalVar();
	}

	// Anonymous Inner Class
	public void useAnonymousInnerClass() {
		// MyInterface can be an interface, an
		// abstract class, or a concrete class.
		MyInterface obj = new MyInterface() {
			@Override
			public void doSomething() {
				System.out.println("Anonymous Inner Class in action!");
			}
		};
		obj.doSomething();
	}

	public static void main(String[] args) {
		// Accessing the Non-static Nested Class (Inner Class)
		OuterClass outerObj = new OuterClass();
		OuterClass.InnerClass innerObj = outerObj.new InnerClass();
		innerObj.accessOuterVar();

		// Accessing the Static Nested Class
		OuterClass.StaticNestedClass nestedObj = new OuterClass.StaticNestedClass();
		nestedObj.accessOuterStaticVar();

		// Accessing the Method-local Inner Class
		outerObj.methodWithInnerClass();

		// Accessing the Anonymous Inner Class
		outerObj.useAnonymousInnerClass();
	}

	interface MyInterface {
		void doSomething();
	}
}
```

## Packages

- A collection of classes, interfaces, and enums in a hierarchical manner.
- Must correspond to folders in the file system.
- Enable developers to keep their classes separate from the classes in the Java API.
- Allow classes to be reused in other applications.
- Allow classes to be distributed to others.

```java
package myJavaApp;
```

- When publishing a Java project to the public, it's must have a unique package name for imports. A common approach is to use a project's domain in reverse.

```java
package com.myJavaApp;
```

- `java.lang` is a core package that provides fundamental classes and features of the Java language.
    - e.g. `String`, `Integer`, `Object`
    - Classes in `java.lang` are auto-imported in every program.
- `java.util` is a utility package that provides a wide range of useful classes and features for more advanced programming tasks.
    - e.g. `ArrayList`, `HashMap`, `Scanner`
    - Classes in `java.util` need to be explicitly imported.

- **Useful Packages**
    - `Random` class from `java.util.Random` and `Math.random()` from `java.lang.Math` can be used for generating random values.
    - [[Logging]] functionality is available from `java.util.logging.*`.

```java
import java.util.logging.Logger;
import java.util.logging.Level;

public class LogLevelExample {
	private static final Logger LOGGER = Logger.getLogger(LogLevelExample.class.getName());

	public static void main(String[] args) {
		LOGGER.info("INFO: Application Starting Up...");
	}
}
```

### Date / Time

- `java.time` (introduced in Java 8) contains classes to work with date/time.
    - `java.time.LocalDate` - date without a time-zone
    - `java.time.LocalDateTime` - date-time without a time-zone
- All date-time classes are immutable. Operations return new instances.

```java
LocalDateTime currDateTime = LocalDateTime.now();

System.out.println("Current date and time: " + currentDateTime);
// e.g., 2024-10-01T14:30:15.123

LocalDateTime customDateTime = LocalDateTime.of(2024, Month.JUNE, 1, 14, 30);

System.out.println("Specific date and time: " + specificDateTime);
// 2024-10-01T14:30
```

```java
// Manipulation
LocalDate today = LocalDate.now();
LocalDate tomorrow = today.plusDays(1);
LocalDate lastWeek = today.minusWeeks(1);

LocalTime now = LocalTime.now();
LocalTime twoHoursLater = now.plusHours(2);
LocalTime thirtyMinutesAgo = now.minusMinutes(30);

// Parsing & Formatting
LocalDate date = LocalDate.now();
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");
String formattedDate = date.format(formatter);
System.out.println("Formatted date: " + formattedDate); // e.g., 01/10/2024

String dateString = "01/10/2024";
LocalDate parsedDate = LocalDate.parse(dateString, formatter);
System.out.println("Parsed date: " + parsedDate); // 2024-10-01

// Comparing
LocalDate date1 = LocalDate.of(2024, 10, 1);
LocalDate date2 = LocalDate.of(2024, 11, 1);

System.out.println("date1 is before date2: " + date1.isBefore(date2)); // true
System.out.println("date1 is after date2: " + date1.isAfter(date2)); // false
System.out.println("date1 is equal to date2: " + date1.isEqual(date2)); // false

// Period (date) & Duration (time)
LocalDate start = LocalDate.of(2024, 1, 1);
LocalDate end = LocalDate.of(2024, 12, 31);
Period period = Period.between(start, end);
System.out.println("Period: " + period); // P11M30D (11 months, 30 days)

LocalTime time1 = LocalTime.of(9, 0);
LocalTime time2 = LocalTime.of(17, 30);
Duration duration = Duration.between(time1, time2);
System.out.println("Duration: " + duration); // PT8H30M (8 hours, 30 minutes)
```

### Imports

```java
import java.util.Date;
// import java.sql.Date; // Duplicate import not allowed ⛔

public class Main {
	public static void main(String[] args) {
		Date date = new Date();

		// Explicit class usage
		java.sql.Date dateSql = new java.sql.Date();
	}
}
```

> [!note]
> Using `*` imports all files in a package, not folders.
>
> ```java
> import java.lang.*;
> ```

- `import static` can be used to import static members of a class directly, without having to qualify them with the class name.

```java
import static java.lang.Math.PI;

import static java.lang.Math.*;

// Instead of Math.pow(2, 3)
double power = pow(2, 3);
```

**`import` vs. `import static`**:

- Regular `import` provides access to classes and interfaces, while `static import` provides access to static members of a class.
- Regular imports are applied to all types, while static imports are applied only to static members.

## I/O

- I/O Streams represent an input source and an output destination.
    - **Input Streams** - used to read data from a source, one item at a time.
    - **Output Streams** - used to write data to a destination, one item at a time.
    - e.g. `InputStream`, `OutputStream`, `Reader`, `Writer`

- **Byte Streams** - used to read and write a single byte (8 bits) of data.
    - Derived from the abstract classes `InputStream` and `OutputStream`.
- **Character Streams** - used to read and write a single character of data.
    - Derived from the abstract classes `Reader` and `Writer`.

### File I/O

- `java.io` package provides classes for file I/O operations.
    - Examples: `FileInputStream`, `FileOutputStream`, `FileReader`, and `FileWriter`.
        - `FileReader` - used to read character data from a file.
        - `FileWriter` - used to write character data to a file.

**FileWriter** & **FileReader**

```java
// FileWriter
FileWriter writer = new FileWriter("output.txt");
writer.write("Hello, World!");
writer.close();

// FileReader
FileReader reader = new FileReader("input.txt");
int c;
while ((c = reader.read()) != -1) {
	System.out.print((char) c);
}
reader.close();
```

**Buffered Streams**

```java
import java.io.BufferedWriter;
import java.io.BufferedReader;

try {
	BufferedWriter writer = new BufferedWriter(new FileWriter("output.txt"));
	writer.write("Hello, Java.");
	writer.write("\nKeep Coding!");
} catch (IOException e) { /*...*/ }
finally {
	writer.close();
}


try {
	BufferedReader reader = new BufferedReader(new FileReader("output.txt"));
	String line;

	while((line = reader.readLine()) != null)
		System.out.println(line)

	reader.readLine();
} catch (IOException e) { /*...*/ }
finally {
	reader.close();
}
```

#### Serialization

- Converting an object into a stream of bytes to store the object or transmit it over a network.
- Deserialization is converting the stream of bytes back into an object.
- The `java.io.Serializable` interface is used to mark a class as serializable.
    - It has not methods to implement.
- The `ObjectOutputStream` and `ObjectInputStream` classes are used to perform serialization and deserialization, respectively.
- The `transient` keyword is used to indicate that a field should not be serialized.
- When a class implements `Serializable`, all its subclasses are automatically serializable.
- **Use Cases**
    - Persist object state to files and databases
        - e.g. saving configurations and user preferences, caching data for performance
    - Easy data transfer over networks or between different systems
        - e.g. sending data between client and server apps
    - Creating deep clone of objects
    - Caching
    - Convert objects to different formats (e.g. JSON) for REST APIs
    - Cross-platform data exchange

```java
import java.io.Serializable;

public class Employee implements Serializable {
	private String name;
	private int id;
	private transient String tempPassword;

	public Employee(String name, int id) {
		this.name = name;
		this.id = id;
	}
}


// Serialization
Employee emp = new Employee("John Doe", 1001);

try (FileOutputStream fileOut = new FileOutputStream("employee.ser");
	 ObjectOutputStream out = new ObjectOutputStream(fileOut)) {
	out.writeObject(emp);
	System.out.println("Employee object serialized");
} catch (IOException e) {
	e.printStackTrace();
}

// Deserialization
try (FileInputStream fileIn = new FileInputStream("employee.ser");
	 ObjectInputStream in = new ObjectInputStream(fileIn)) {
	Employee empDeserialized = (Employee) in.readObject();
	System.out.println("Employee deserialized: " + empDeserialized.getName());
} catch (IOException | ClassNotFoundException e) {
	e.printStackTrace();
}
```

> [!note]
>
> - Static fields are not serialized as they belong to the class, not the object.
> - If a serializable class has a reference to a non-serializable class, a `NotSerializableException` will be thrown.
> - Deserialization can be a security risk if accepting untrusted data.
>     - Always validate deserialized objects.
> - Serialization can be slower and produce larger byte streams compared to custom binary formats.

### User Input

**BufferedReader**

```java
import java.io.BufferedReader;

System.out.println("Enter your age: ");

InputStreamReader in = new InputStreamReader(System.in);
BufferedReader bf = new BufferedReader(in);

int age = Integer.parseInt(bf.readLine());
System.out.println(age);

bf.close();
```

**Scanner**

```java
import java.util.Scanner;

Scanner scanner = new Scanner(System.in);
System.out.println("Enter your name: ");

String name = scanner.nextLine();
System.out.println("Hello, " + name);

System.out.println("How old are you?");
int age = scanner.nextInt();
System.out.println("You are " + age + " years old.");
```

## Functional Programming

- Java supports [[Functional Programming|FP]] concepts such as:
    - [[First-Class Functions]]
    - [[Pure Functions]]
    - [[Immutable|Immutability]]
    - [[Declarative Programming]]
    - Lazy Evaluation
    - [[Higher-Order Functions]]
    - [[Currying]]

```java
Function<Integer, Integer> cube = x -> x * x * x;

`Function<Integer, Function<Integer, Integer>> currySum = a -> b -> a + b;`
```

- A Consumer (`java.util.function.Consumer`) is a functional interface and it represents an operation that accepts a single input argument and returns no result.
    - Useful to perform an operation on an input value, without the need to return anything.

```java
List<String> names = Arrays.asList("John", "Jane", "Bob", "Alice");

Consumer<String> consumer = new Consumer<>() {
	public void accept(String n) {
		System.out.println(n)
	}
};

names.forEach(consumer);
// OR simply
names.forEach(n -> System.out.println(n));
```

### Stream API

- Process collections of objects in functional style.
- Operate on a source such as a collection, array or I/O channel.

- A stream represents a sequence of elements that supports various methods to perform ops like filtering and mapping.
    - Can only be used once.
    - Allow method chaining (similar to array methods in [[JavaScript]])

```java
List<String> names = Arrays.asList("John", "Jane", "Bob", "Alice");
Stream<String> s = names.stream();

List<String> uppercaseNames = s
	.filter(name -> name.startsWith("J"))
	.map(String::toUpperCase)
	.toList();

System.out.print(uppercaseNames);
// Output: [JOHN, JANE]
```

- Parallel streams can take advantage of multiple CPU cores to process data more efficiently.

```java
List<String> names = Arrays.asList("John", "Jane", "Bob", "Tim", "Megan", "Sam");

long count = names.parallelStream()
	 .filter(name -> name.length() > 3)
	 .count();

System.out.print(count);
// Output: 6
```

- Commonly used Stream API methods:
    - `filter()`
    - `map()`
    - `flatMap()`
    - `reduce()`
    - `collect()`
    - `skip()`
    - `sum()`
    - `min()` and `max()`
    - `forEach()`
    - `sorted()`

## Generics

- Allow us to create classes that can accommodate different types.
- Don't work with primitive types.

```java
class Printer<T> {
	T item;

	public Printer(T item) {
		this.item = item;
	}

	public void print() {
		System.out.print(item);
	}
}

Printer<Double> pi = new Printer<>(3.14);
pi.print();
```

- Generic methods work with any data type, even if the containing class is not generic.

```java
public class Utils {
	public static <T> void printArray(T[] array) {
		for (T element : array) {
			System.out.println(element);
		}
	}

	public static <K, V> void printKeyValuePair(K key, V value) { /*...*/ }
}

Integer[] intArray = {1, 2, 3, 4, 5};
Utils.<Integer>printArray(intArray);
```

- Generics can be bounded to a specific type or its subclasses using `extends`.
    - A generic type can extend a single class due to [[multiple inheritance]].
    - When extending a class and interfaces, the class must appear first.

```java
public class GenericClass <T extends MyClass & MyInterface> {}
```

```java
public static <T extends Number> double sum(List<T> numbers) {
	double total = 0.0;
	for (T number : numbers) {
		total += number.doubleValue();
	}
	return total;
}

List<Integer> intList = Arrays.asList(1, 2, 3, 4, 5);
double intSum = sum(intList);
```

- The wildcard (`?`) can be used to represent an unknown type in generics.
    - 3 main types:
        - **Upper Bounded Wildcards**: `? extends Type`
            - Allows any subtype of the specified type to be used.
            - e.g. `List<? extends Number>`
        - **Lower Bounded Wildcards**: `? super Type`
            - Allows any supertype of the specified type to be used.
            - e.g. `List<? super Integer>`
        - **Unbounded Wildcards**: `?`
            - Represents an unknown type, with no restrictions.
            - e.g. `List<?>`
    - Guidelines for using wildcards:
        - Use `? extends Type` for "in" variables (input parameters).
        - Use `? super Type` for "out" variables (output parameters).
        - Use unbounded wildcards `?` when the code doesn't depend on the type parameter.

## Collection API

- The Java Collections Framework is a unified architecture for representing and manipulating collections in Java.
- It provides a set of interfaces, implementation classes, and algorithms to work with collections of objects.

### `Collection`

- `Collection` is the root interface of the Java Collections Framework.
- Work with wrapper class types.
    - e.g. `Integer`, `String`
- Implementations of the `java.util.Collection` interface:
    - `List`
        - `ArrayList`
        - `LinkedList`
        - **Methods**
            - `add(element)`
            - `get(index)`
            - `set(index, element)`
            - `remove(index)`
            - `lastIndexOf(element)`
            - `subList(fromIndex, toIndex)`
    - `Queue`
        - `DeQueue`
        - `PriorityQueue`
        - **Methods**
            - `add(element)`
            - `remove()`
            - `element()`
            - `peek()`
            - `size()`
            - `offer(element)`
            - `poll()`
    - `Set` - no indexes; no duplicates.
        - `HashSet`
        - `TreeSet`
        - `LinkedHashSet`
        - **Methods**
            - `add(element)`
            - `remove(element)`
            - `contains(element)`
            - `size()`
            - `iterator()`

- `Collections` is a utility class that provides static methods to operate on collections, such as sorting, searching, and synchronizing.

```java
List<Integer> arr = new ArrayList<>();

arr.add(1);
arr.add(2);
arr.add(3);

Collections.sort(arr);
```

- The `Comparator` interface is a functional interface that allows us to define custom sorting logic for objects.
    - It defines a single method `compare(T o1, T o2)` which must be implemented.

```java
class Person {
	String name;
	int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
}

List<Person> people = new ArrayList<>();
people.add(new Person("Alice", 25));
people.add(new Person("Bob", 33));
people.add(new Person("Charlie", 28));

Comparator<Person> personComparator = (i, j) -> (i.age > j.age) ? 1 : -1;

Collections.sort(people, personComparator);
```

- `Comparable` is a functional interface that gives classes the ability to implement their own natural sorting logic.
    - It defines a single method `compareTo(T o)` which must be implemented in the class.

```java
class Person implements Comparable<Person> {
	private String name;
	private int age;

	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public int compareTo(Person other) {
		return Integer.compare(this.age, other.age);
	}
}

List<Person> people = new ArrayList<>();
people.add(new Person("Alice", 25));
people.add(new Person("Bob", 33));
people.add(new Person("Charlie", 28));

Collections.sort(people);
```

- Collections implement `Iterator`.

```java
Iterator<Integer> values = arr.iterator();

while (values.hasNext())
	System.out.println(values.next());
```

### `Map`

- A set of key-value pairs.
    - `HashMap`
    - `Hashtable` - synchronized
- `null` is not allowed in a map object.

```java
Map<String, Integer> pairs = new HashMap<>();

pairs.put("a", 1);
pairs.put("b", 2);
pairs.put("c", 3);

System.out.println(pairs);
System.out.println(pairs.get("a"));

for (String k : pairs.keySet()) {
	System.out.println(key + ": " + pairs.get(key));
}
```

## Lambda Expressions

- Concise way to represent anonymous functions.
- Consist of:
    - Zero or more parameters enclosed in parenthesis (which is optional for single parameter methods)
    - An arrow token (`->`)
    - A body (single expression or block of statements)
- Used to provide an implementation of a functional interface.
- Similar to [[JavaScript|JS]] arrow functions in use case and syntax.

```java
@FunctionalInterface
interface Calc {
	void add(int a, int b);
}

// Anonymous Inner Class
Calc c = new Calc() {
	@Override
	public void add(int a, int b) {
		System.out.println(a + b);
	}
};

// Lambda Expression
Calc c = (int a, int b) -> {
	System.out.println(a + b);
};
// OR
Calc c = (int a, int b) -> System.out.println(a + b);
// OR
Calc c = (a, b) -> System.out.println(a + b);
```

- `return` can be omitted when implementing non-void methods with single-line return statements.

```java
Calc c = (a, b) -> a + b;
```

- **Method Reference**
    - `::` (method reference operator)
        - Introduced in Java 8
        - Provides a concise way to refer to a method by its name without actually invoking it, or defining a lambda expression to represent the same functionality.
    - **Types of Method References**:
        - Static Methods - e.g., `ClassName::staticMethodName`
        - Instance Methods of an object - e.g., `objectRef::instanceMethodName`
        - Instance Methods of a particular type - e.g., `ContainingType::methodName`
        - Constructors - e.g., `ClassName::new`
    - Method references are equivalent to lambda expressions, but with a more readable and compact syntax
        - Useful to pass a method as a parameter, without any additional logic.
    - The output from the previous expression needs to match the input parameters of the referenced method signature.

```java
// Lambda Expression
list.forEach(item -> System.out.println(item));

// Method Reference
list.forEach(System.out::println);

list.forEach(String::concat);

/* --- */

public EV(String brand) { this.brand = brand; }

List<String> evBrands = Arrays.asList("Rivian", "Tesla", "Polestar");

evBrands.stream()
	.map(EV::new)
	.toArray(EV[]::new);
```

## Threads

- Every thread must have a `run()` method.

```java
class A extends Thread {
	public void run() { /*...*/ }
}

class B extends Thread {
	public void run() { /*...*/ }
}

A a = new A();
B b = new B();

a.start();
b.start();
```

- `Thread` implements the `Runnable` interface, so it's possible to create and run threads by implementing `Runnable`.
    - Since extending multiple classes is not allowed, this is useful when the thread class needs extend another class.

```java
class A implements Runnable {
	public void run() { /*...*/ }
}

class B implements Runnable {
	public void run() { /*...*/ }
}

Runnable a = new A();
Runnable b = new B();

Thread t1 = new Thread(a);
Thread t2 = new Thread(b);

t1.start();
t2.start();
```

### Synchronization

- Ensures thread safety by controlling access of multiple threads to shared resources.
    - `synchronized` can be applied to methods or blocks of code to prevent [[race conditions]] when using threads.
        - Synchronized methods and blocks of code can be `static`.
- Enables inter-thread communication using methods like `wait()`, `notify()`, and `notifyAll()`.

```java
class Counter {
	int count;

	// Synchronized Method
	public synchronized void increment() { count++; }

	public void decrement() {
		// Synchronized Block
		synchronized (this) {
			count--;
		}
	}
}

Counter c = new Counter();

Runnable a = () -> {
	for (int i = 0; i < 1000; i++) {
		c.increment();
	}
};
Runnable b = () -> {
	for (int i = 0; i < 1000; i++) {
		c.increment();
	}
};

Thread t1 = new Thread(a);
Thread t2 = new Thread(b);

t1.start();
t2.start();

t1.join();
t2.join();

System.out.print(c.count);
```

## Ecosystem

- Apache Commons and Google Guava are open-source libraries in the Java ecosystem that provide reusable components and utilities.
    - Apache Commons offers a range of general-purpose libraries.
    - Google Guava extends the Java Collections Framework and includes additional features like caching, string processing, and concurrency utilities.

### Unit Testing: JUnit

- Popular open-source unit testing framework for [[Java]].
- Follows the principles of [[Software Testing#Test-Driven Development (TDD)|TDD]].
- Provides annotations like `@Test` to identify test methods and assertions like `assertEquals()` to verify expected results.
- Encourages writing tests first, leading to better code readability and quality.
- Supports test runners for running tests and generating reports.
- Automated test execution and easily interpretable results (green for passing, red for failing) provide immediate feedback.
- Supports organizing tests into suites, allowing for efficient test execution and management.
- Integrates well with popular IDEs like Eclipse and build tools like Maven and Gradle.
- `@Test` (`org.junit.jupiter.api.Test`) is applied over methods to mark them as tests.
    - Informs test engine what method needs to run.
    - In JUnit 5, `@Test` annotated methods can be `public`, `protected` or default, unlike in JUnit 4 where they must be `public`.
- Assertions are static methods accessible via:

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class CalcTest {
	private Calc c;

	@BeforeEach
	void setUp() {
		c = new Calc();
	}

	@AfterAll
	static void cleanUp() {
		c = null;
	}

	@Test
	@DisplayName("Add Two Integers")
	void testAdd() {
		int sum = c.add(2, 3);
		assertEquals(5, sum);
	}

	@Test
	@DisplayName("Multiply Two Integers")
	void testMultiply() {
		int product = c.multiply(2, 3);
		assertEquals(6, product);
	}
}
```

- JUnit 5 provides annotations to define methods that will be executed at specific points in a test's lifecycle.
    - **Setup Phase**
        - `@BeforeAll`
            - A static method that is executed once before all test methods in the class.
            - Used for expensive setup operations that should be done only once for the entire test class.
        - If the test class has a constructor, it runs before `@BeforeEach`.
        - `@BeforeEach`
            - A method that is executed before each test method (`@Test`) in the class.
            - Used to initialize objects or reset the state required for each test method.
    - **Test Execution Phase**
        - `@Test`
            - Methods that are the actual test cases that contain the test logic and assertions.
    - **Teardown Phase**
        - `@AfterEach`
            - A method executed after each test method (`@Test`) in the class.
            - Used for cleanup operations like releasing resources acquired in the `@BeforeEach` method.
        - `@AfterAll`
            - A static method executed once after all test methods in the class.
            - Used for cleanup operations that should be done only once for the entire test class, like closing database connections.
    - The execution order of these lifecycle methods is as follows:
        1. `@BeforeAll`
        2. `@BeforeEach`
        3. `@Test`
        4. `@AfterEach`
        5. `@AfterAll`

> [!example]- Test Lifecycle
> ![Test Lifecycles](assets/images/java.test-lifecycles.png)
>
> - **Source**: Hyperskill

- The `@TestInstance` annotation is used to configure the lifecycle of test instances for a test class or test interface.
    - Useful to share state or perform expensive setup/teardown operations across multiple test methods in the same test class.
    - Can introduce dependencies between test methods, potentially violating the principle of test isolation.
    - `@TestInstance(Lifecycle.PER_CLASS)`
        - A single instance of the test class is created and reused for all test methods in that class.
        - Can improve performance and simplify test code.
    - `@TestInstance(Lifecycle.PER_METHOD)`
        - The default mode if `@TestInstance` is not specified.
        - A new instance of the test class is created for each test method.

- `fail()` is used to explicitly fail a test.
    - It is useful
        - when a test is incomplete or not yet implemented.
        - when an exception is expected or to test whether code throws a desired exception or not.
        - when an unexpected exception is likely to be thrown.
        - for signaling an undesirable outcome.

```java
// Expected Exception
String str = null;
try {
	System.out.print(str.length());  // should throw exception
	fail("Expected Exception Not Thrown");
} catch (NullPointerException e) {
	assertNotNull(e);
}
// OR
String str = null;
assertThrows(NullPointerException.class, () -> str.length()); // Test Passes

// --------------------

// Unexpected Exception
try {
	Calc c = new Calc();
	c.divide(5, 0);
} catch (ArithmeticException e) {
	fail("Division by Zero!")
}
```

- Other assertion libraries can be used with JUnit.
    - A popular assertion library is AssertJ. AssertJ provides assertion methods that are specific to the passed data type.

```java
assertThat(product).isEqualTo(6);
```

- Parameterized tests allow to run the same test multiple times with different input data.
    - Useful for testing the same logic with various sets of arguments or input values.
    - Test method is executed once for each set of arguments provided.

```java
@ParameterizedTest
@ValueSource(ints = {13, 15, 20})
void testIsEven(int num) {
	boolean result = Calculator.isEven(num);

	assertThat(result).isTrue();
}

// For tests requiring multiple arguments
@ParameterizedTest(name = "{0} + {1} = {2}")
@CsvSource({"1, 1, 2", "2, 3, 5", "42, 57, 99"})
void testAdd(int a, int b, int expected) {
	assertEquals(expected, Calculator.add(a, b));
}
```

- Test methods or classes can be annotated using one or more `@Tag` (e.g. `@Tag("unit")`).
    - This information can be used when running tests from a CLI or CI/CD pipeline to specify which tests should run.

```bash
mvn test -Dgroups=unit
```

- For projects setup using Maven, the Surefire plugin can be used during the `test` phase of the build lifecycle to execute the unit tests of an application.
    - This allows running test without relying on an IDE.
    - It generates reports in two different file formats: `*.txt` and `*.xml`.

### Mock Testing: Mockito

- Java-based mocking framework designed for unit testing Java applications.
- Works seamlessly with tools like JUnit.
- Two main ways to create mocks in Mockito:
    - Using the `mock()` method.
    - Using the `@Mock` annotation.

```java
// mock()
List mockedList = Mockito.mock(List.class);

// @Mock
@Mock
List mockedList;

@BeforeEach
void setUp() {
	MockitoAnnotations.openMocks(this);
}
```

```java
public class User {
	private String id;
	private String name;

	public User(String id, String name) {
		this.id = id;
		this.name = name;
	}

	/* ... */
}

public interface UserRepository {
	User findById(String id);
	void save(User user);
}

public class UserService {
	private UserRepository repository;

	public UserService(UserRepository repository) {
		this.repository = repository;
	}

	public User getUserById(String id) {
		return repository.findById(id);
	}

	public void updateUserName(String id, String newName) {
		User user = repository.findById(id);
		if (user != null) {
			user.setName(newName);
			repository.save(user);
		}
	}
}
```

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class UserServiceTest {
	@Mock
	private UserRepository userRepository;

	private UserService userService;

	@BeforeEach
	void setUp() {
		MockitoAnnotations.openMocks(this);
		userService = new UserService(userRepository);
	}

	@Test
	void testGetUserById() {
		// Arrange
		String userId = "123";
		User expectedUser = new User(userId, "John Doe");
		when(userRepository.findById(userId)).thenReturn(expectedUser);

		// Act
		User actualUser = userService.getUserById(userId);

		// Assert
		assertEquals(expectedUser, actualUser);
		verify(userRepository).findById(userId);
	}

	@Test
	void testUpdateUserName() {
		// Arrange
		String userId = "123";
		String newName = "Jane Doe";
		User existingUser = new User(userId, "John Doe");
		when(userRepository.findById(userId)).thenReturn(existingUser);

		// Act
		userService.updateUserName(userId, newName);

		// Assert
		verify(userRepository).findById(userId);
		verify(userRepository).save(argThat(user ->
			user.getId().equals(userId) && user.getName().equals(newName)
		));
	}
}
```

### Build Tooling: Maven

- Maven is a dependency manager and build automation tool for Java programs.
- It's used for building and managing Java-based projects.

- **POM** - Project Object Model
    - Maven uniquely identifies projects through _project coordinates_ defined in a `pom.xml` file:
        - `group-id` - e.g. `com.oneminch`
        - `artifact-id` - e.g. `java-app`
        - `version` - e.g. `0.0.1-SNAPSHOT`
    - Important tags
        - `<project>` - Root tag of the `pom.xml` file
        - `<modelVersion>` -which version of the page object model to be used
        - `<name>` - project name
        - `<properties>` - project-specific settings
        - `<dependencies>` - Java dependencies.
            - Each one needs a `<dependency>`, which has:
                - `<groupId>`
                - `<artifactId>`
                - `<version>`
        - `<plugins>` - 3rd party plugins that work with Maven

```xml
<project>
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.oneminch</groupId>
	<artifactId>java-app</artifactId>
	<version>1</version>

	<dependencies>
		<dependency>
			<groupId>org.apache.maven</groupId>
			<artifactId>maven-artifact</artifactId>
			<version>${mavenVersion}</version>
		</dependency>
		<dependency>
			<groupId>org.apache.maven</groupId>
			<artifactId>maven-core</artifactId>
			<version>${mavenVersion}</version>
		</dependency>
	</dependencies>
</project>
```

- When building a project, Maven goes thru several steps called _phases_.
- The Maven Build Lifecycle is a well-defined sequence of phases that a Maven build goes through.
- By default, these are:
    1.  `validate` - Ensure project is correct and all necessary information is available
    2.  `compile` - Compile source code
    3.  `test` - Test all compiled code
    4.  `package` - Package all compiled code to WAR/JAR file
    5.  `integration` - Perform all integration tests on WAR/JAR
    6.  `verify` - Run any checks on the results of integration tests
    7.  `install` - Install WAR/JAR to local repository
    8.  `deploy` - Copy final WAR/JAR to the remote repository
- Each phase is composed of plugin goals which represent a specific task that contributes to the process.

```bash
# Runs every phase prior to 'package'
mvn package

# Execute a specific goal using 'plugin:goal' syntax
mvn dependency:copy-dependencies
```

- Plugins can help configure the build lifecycle of a project.
    - e.g. For a project with external dependencies, `mvn package` won't work as intended in building the project into a JAR executable. The `maven-assembly-plugin` can be used to integrate all dependencies in the build process.

- The `resources` folder in a Maven project is a designated directory for storing non-code files that are required by the application at runtime.
    - Typically located at `src/main/resources` for the main codebase and `src/test/resources` for test resources.
    - Holds configuration files (e.g., properties files, XML files), data files, images, and other static assets needed by the application.
    - Allows for separating application code from non-code resources.
        - This promotes better organization and maintainability of the project structure.
    - During the build process, Maven copies the contents of the resources folder into the root of the compiled output (e.g., JAR or WAR file), making them accessible to the application's classpath.
    - Maven provides flexibility in specifying additional resource directories other than the default `src/main/resources` location.
        - Can be done by configuring the `<resources>` element in the project's `pom.xml` file.

#### Workflow

- **Create a Maven project using the `mvn` CLI**

```bash
mvn archetype:generate -DgroupId={group.id} -DartifactId={artifact-id} -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

```bash
# Project Structure
# (groupId = com.example, artifactId = my-app)
my-app
├── pom.xml
├── src
│   ├── main
│   │   └── java
│   │       └── com/example
│   │           └── App.java
│   └── test
│       └── java
│           └── com/example
│               └── AppTest.java
└── target
	├── classes
	├── test-classes
	├── surefire-reports
	└── my-app-1.0-SNAPSHOT.jar

```

- **Compile and package your project into a JAR file**
    - The generated JAR file will be located in the `target` directory.

```bash
mvn package
```

- **Run the application**

```bash
java -jar target/{artifact-id}-1.0-SNAPSHOT.jar
```

- **Compile the project**

```bash
mvn compile
```

- **Run unit tests**

```bash
mvn test
```

- **Clean the project**
    - Removes the target directory with all the build data.

```bash
mvn clean
```

- **Install the package**
    - Compile, test, package code and copy it to the local repository.

```xml
<dependencies>
	<dependency>
		<groupId>com.example</groupId>
		<artifactId>package-artifact-id</artifactId>
		<version>1.0.0</version>
	</dependency>
</dependencies>
```

```bash
mvn install
```

- **Clean project and performs a fresh install**

```bash
mvn clean install
```

- **Check Maven version**

```bash
mvn --version
```

- **Generate project documentation**

```bash
mvn site
```

### Databases

#### JDBC

- Java Database Connectivity
- A standard Java API for database-independent connectivity between Java applications and a wide range of databases.
- The most low-level way to accessing databases in Java.
- Provides a set of interfaces and classes that enable Java programs to interact with different DBMSs in a uniform way.
- Has a two-layer architecture:
    - **API layer**
        - Provides interfaces and classes for database operations like connecting, querying, updating, etc.
        - `java.sql`
    - **Driver layer**
        - Consists of database-specific JDBC drivers that implement the JDBC interfaces and communicate with the actual database
            - JDBC drivers act as a bridge between the Java application and the database. They translate JDBC calls into database-specific protocol.
                - e.g. `org.xerial.sqlite-jdbc`
- Key components include:
    - `DriverManager` - loads drivers
    - `Connection` - represents a database session
    - `Statement`/`PreparedStatement` - executes SQL
    - `ResultSet` - holds query results

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

// ...
String dbUrl = "jdbc:sqlite:./src/main/resources/Users.db";
try (Connection c = DriverManager.getConnection()) {
	// Create
	String createSql = "INSERT INTO Users (name) VALUES (?)";
	PreparedStatement createPs = c.prepareStatement(createSql);
	createPs.setString(1, "John");
	createPs.executeUpdate();

	// Read
	String readSql = "SELECT * FROM Users WHERE name = ?";
	PreparedStatement readPs = c.prepareStatement(readSql);
	readPs.setString(1, "John");
	ResultSet rs = readPs.executeQuery();

	while (rs.next()) {
		int userId = rs.getInt("id");
		int userName = rs.getString("name");

		System.out.println(userId + " - " + userName);
	}

	// Update
	String updateSql = "UPDATE Users SET name = ? WHERE name = ?";
	PreparedStatement updatePs = c.prepareStatement(updateSql);
	updatePs.setString(1, "Jane");
	updatePs.setString(2, "John");
	updatePs.executeUpdate();

	// Delete
	String deleteSql = "DELETE FROM Users WHERE name = ?";
	PreparedStatement deletePs = c.prepareStatement(deleteSql);
	deletePs.setString(1, "Jane");
	deletePs.executeUpdate();
} catch (SQLException e) {
	e.printStackTrace();
}
```

- [[Connection Pool|Connection pools]] keep a small number of database connections open.
    - When a connection is needed, instead of opening a new connection, the connection pool can give one of the connections it has already opened.
    - `HikariCP` is a popular tool used to achieve this.

```java
String dbUrl = "jdbc:sqlite:./src/main/resources/Users.db";

DataSource ds = new HikariDataSource();
ds.setJdbcUrl(dbUrl);
// ds.setUsername("...");
// ds.setPassword("...");

try (Connection c = ds.getConnection()) {
	// Create
	String createSql = "INSERT INTO Users (name) VALUES (?)";
	PreparedStatement createPs = c.prepareStatement(createSql);
	createPs.setString(1, "John");
	createPs.executeUpdate();
} catch (SQLException e) {
	e.printStackTrace();
}
```

#### JPA

- Java Persistence API
- A specification that provides a standardized way to manage relational data in Java applications.
- Acts as a bridge between Java objects/classes and relational databases.
- Can be used to:
    - Map Java objects directly to database tables using annotations.
    - Perform CRUD (Create, Read, Update, Delete) operations on Java objects without writing SQL.
    - Query Java objects using JPQL (Java Persistence Query Language) instead of SQL.
- Eliminates the need to write low-level JDBC code and SQL queries.
- At the core of JPA are entities - Java classes that represent database tables.
    - These classes are annotated to define their mapping to database structures.
- Can be implemented with different [[ORM]] tools.
    - Allows switching between different JPA implementations (e.g., Hibernate, EclipseLink) with minimal changes.
    - Reduces vendor lock-in compared to using specific ORM frameworks directly.
- Offers caching mechanisms to reduce database queries, lazy loading and query optimization features in many JPA implementations.
- Can introduce some performance overhead.
- **Annotations**
    - `@Entity` marks a class as a JPA entity.
        - `@Table` specifies the database table it maps to.
    - `@Id` designates the primary key.
        - `@GeneratedValue` specifies how it's generated.
    - `@Column` customizes the mapping of a field to a database column.

```java
@Entity
@Table(name = "students")
public class Student {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Column(name = "first_name", length = 50, nullable = false)
	private String firstName;

	// Class body
}
```

- JPA provides annotations to define [[Databases#Relationships / Multiplicity|relationships]] between entities.

```java
/* --- One-to-One --- */

@OneToOne(cascade = CascadeType.ALL)
@JoinColumn(name = "address_id", referencedColumnName = "id")
private Address address;

/* --- One-to-Many --- */

// In the Course class
@OneToMany(mappedBy = "course")
private List<Student> students;

// In the Student class
@ManyToOne
@JoinColumn(name = "course_id")
private Course course;

/* --- Many-to-Many --- */

@ManyToMany
@JoinTable(
	name = "student_projects",
	joinColumns = @JoinColumn(name = "student_id"),
	inverseJoinColumns = @JoinColumn(name = "project_id")
)
private Set<Project> projects;
```

- JPA provides an `EntityManager` as the primary interface for interacting with the persistence context.
    - Used to handle loading and persisting data automatically.

```java
@PersistenceContext
private EntityManager entityManager;

public void saveStudent(Student student) {
	entityManager.persist(student);
}
```

- JPQL (Java Persistence Query Language) is used to write database-independent queries.

```java
TypedQuery<Student> query = entityManager.createQuery(
	"SELECT s FROM Student s WHERE s.firstName = :name", Student.class);
query.setParameter("name", "John");
List<Student> students = query.getResultList();
```

- JPA transactions ensure [[database consistency]].

```java
@Transactional
public void updateStudentName(Long id, String newName) {
	Student student = entityManager.find(Student.class, id);
	student.setFirstName(newName);
}
```

- JPA allows embedding complex types within entities:

```java
@Embeddable
public class Address {
	private String street;
	private String city;
	private String zipCode;
}

@Entity
public class Student {
	@Embedded
	private Address address;
}
```

##### [[ORM]]: Hibernate

- Maps Java objects to database tables.
- Manages the storage and retrieval of Java objects in databases.
- Simplifies database interactions by allowing developers to work with objects instead of SQL.
- Reduces boilerplate code compared to traditional JDBC.
- Provides an object-oriented query language similar to SQL called HQL.
- Features caching and batch processing for improved performance.
- Compatible with Java Persistence API (JPA) annotations.

```java
@Entity
@Table(name = "Employees")
public class Employee {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Long id;

	@Column(name = "first_name")
	private String firstName;

	@Column(name = "last_name")
	private String lastName;

	// Getters and setters
}
```

```java
// Saving an Object
Session session = sessionFactory.openSession();
Transaction tx = null;
try {
	tx = session.beginTransaction();
	Employee e = new Employee();
	e.setFirstName("John");
	e.setLastName("Doe");
	session.save(employee);
	tx.commit();
} catch (Exception e) {
	if (tx != null) tx.rollback();
	e.printStackTrace();
} finally {
	session.close();
}

// Retrieving an Object
Session session = sessionFactory.openSession();
try {
	Employee e = session.get(Employee.class, 1L);
	String fname = e.getFirstName();
	String lname = e.getLastName();
	System.out.println("Employee: " + fname + " " + lname);
} finally {
	session.close();
}

// HQL
Session session = sessionFactory.openSession();
try {
	Query<Employee> query = session.createQuery("FROM Employee WHERE lastName = :lastName", Employee.class);
	query.setParameter("lastName", "Doe");
	List<Employee> employees = query.list();
	for (Employee emp : employees) {
		System.out.println(emp.getFirstName());
	}
} finally {
	session.close();
}
```

### [[Logging]]: Log4j

```java
// Application Startup Logging
import org.apache.log4j.Logger;

public class Application {
	private static final Logger logger = Logger.getLogger(Application.class);

	public static void main(String[] args) {
		logger.info("Application starting up...");
		logger.info("Loaded configuration from: " + configPath);
		logger.info("Application started successfully");
	}
}

// Error Handling and Debugging
public class UserService {
	private static final Logger logger = Logger.getLogger(UserService.class);

	public User getUserById(int id) {
		try {
			// Code to fetch user from database
			return user;
		} catch (DatabaseException e) {
			logger.error("Failed to fetch user with ID: " + id, e);
			throw new ServiceException("User retrieval failed");
		}
	}
}

// Performance Monitoring
public class DataProcessor {
	private static final Logger logger = Logger.getLogger(DataProcessor.class);

	public void processLargeDataSet(List<Data> dataSet) {
		long startTime = System.currentTimeMillis();
		logger.info("Starting to process data set of size: " + dataSet.size());

		// Process data...

		long endTime = System.currentTimeMillis();
		logger.info("Data processing completed in " + (endTime - startTime) + " ms");
	}
}

// Audit Logging
public class UserActionLogger {
	private static final Logger auditLogger = Logger.getLogger("AuditLog");

	public void logUserAction(String username, String action) {
		auditLogger.info("User: " + username + " performed action: " + action);
	}
}

// Configuration Changes
public class ConfigurationManager {
	private static final Logger logger = Logger.getLogger(ConfigurationManager.class);

	public void updateConfiguration(String key, String value) {
		logger.info("Updating configuration: " + key + " = " + value);
		// Update configuration
		logger.info("Configuration updated successfully");
	}
}

// External Service Interactions
public class ExternalAPIClient {
	private static final Logger logger = Logger.getLogger(ExternalAPIClient.class);

	public Response callExternalAPI(String endpoint, String payload) {
		logger.info("Calling external API: " + endpoint);
		logger.debug("Request payload: " + payload);

		// Make API call

		logger.info("API call completed. Response code: " + response.getStatusCode());
		return response;
	}
}
```

- Learn more 🧠: [Logging (Hyperskill)](https://hyperskill.org/knowledge-map/359?track=17)

### Documentation: Javadoc

- A documentation generator tool for Java source code.
- Generates API documentation in HTML format from Java source code by parsing the code and extracting the documentation comments (known as "doc comments") written in a specific format.
- All modern versions of the JDK provide the Javadoc tool.

```java
/**
 * This is a doc comment
 */
```

- Javadoc comments can be placed above a class, a method, or a field.
    - May contain HTML tags.
    - Commonly made up of two sections:
        - A description
        - Standalone (block) tags (marked with the `@` symbol)
    - Tags provide specific and structured meta-data.
        - _Block tags_ - placed on their own line.
            - e.g. `@version`, `@since`
        - _Inline tags_ - used within descriptions.
            - e.g. `{@link}`, `{@code}`

```java
/**
 * This package contains utility classes for mathematical operations.
 */
package com.example.math;

/**
 * This class represents a simple calculator.
 *
 * @author John Doe
 * @version 1.0
 */
public class Calculator {
	/** An instance variable */
	private String ops;

	/**
	 * Adds two numbers.
	 *
	 * @param a the first number
	 * @param b the second number
	 * @return the sum of a and b
	 */
	public int add(int a, int b) {
		return a + b;
	}

	/**
	 * This method is deprecated and should not be used.
	 *
	 * @deprecated Use {@link #add()} instead.
	 */
	@Deprecated
	public void oldAdd() { /* implementation */ }
}
```

- Javadoc supports various tags to provide specific information:
    - `@author`: Specifies the author of the code
    - `@version`: Indicates the version of the code
    - `@param`: Describes a method parameter
    - `@return`: Explains the return value of a method
    - `@throws`: Documents exceptions that a method may throw
    - `@see`: Provides a reference to another element in the documentation
    - `@since`: Specifies when an API element was introduced
    - `@deprecated`: Explains why and when code was deprecated, and suggests alternatives.

- To generate Javadoc for all Java source files in the current directory:

```bash
javadoc *.java
# Result stored in 'index.html' file in the 'doc' folder
```

> [!note]
> Private fields don't have Javadoc generated for them by default. To do so, we need to explicitly pass the `-private` option to the Javadoc command.

- The Javadoc Maven plugin can be used for complex document generation.

### Web Development: Javalin

- A lightweight, interoperable and flexible web framework for Java and Kotlin.
- Servlet-based.
- Supports modern features such as HTTP/2, WebSocket, and asynchronous requests.
- Considered as a library rather than a framework.
    - It sets no requirements for application structure.
    - There are no annotations, and no reflection.
- Supports OpenAPI.
- Runs on top of a fully configurable, embedded Jetty server.
    - If the embedded jetty-server is configured, Javalin will attach it’s own handlers to the end of the chain.

```java
import io.javalin.Javalin;

public class HelloWorld {
	public static void main(String[] args) {
		var app = Javalin.create(/*config*/)
			.get("/", ctx -> ctx.result("Hello, Javalin!"))
			.start(7070);
	}
}
```

### Enterprise Java

- Jakarta EE is a set of specifications for enterprise Java development
    - Provides a standardized platform for building large-scale, multi-tiered, scalable, and secure enterprise apps.
    - Includes APIs for:
        - Web development (Jakarta Servlet)
        - Security (Jakarta Security)
        - Concurrency (Jakarta Concurrency)
        - Persistence (Jakarta Persistence)
        - Messaging (Jakarta Messaging)
        - Web Services

#### Servlets

- Are Java classes that run on the server-side to handle client requests and generate dynamic responses.
- Form the foundation of Java web apps.
- **Lifecycle**:
    - _Initialization (`init()`)_
        - Called when the servlet is first created or loaded into memory.
        - Used for one-time initialization of resources.
            - e.g. opening database connections, loading config files
    - _Request Handling (`service()`)_
        - Called for each client request.
        - Handles the request and generates the response.
    - _Destruction (`destroy()`)_
        - Called when the servlet is unloaded from memory.
        - Used for cleanup of resources.
            - e.g. closing database connections, releasing any held resources, saving state information
- All servlets must implement the `Servlet` interface either directly or, more commonly, by extending a class that implements it (e.g., `HttpServlet`).
- Can be configured using either web.xml or annotations.

- `ServletContext` can be used to share information across all servlets.
- _Filters_ intercept requests before they reach the servlet. They serve a similar function to [[middleware]].

```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/myservlet")
public class MyServlet extends HttpServlet {
	public void doGet(HttpServletRequest req, HttpServletResponse res)
			throws ServletException, IOException {
		String name = req.getParameter("name");

		PrintWriter out = res.getWriter();

		res.setContentType("text/html");
		out.println("<html><body>");
		out.println("<h1>Hello, " + name + "!</h1>");
		out.println("</body></html>");
	}

	public void doPost(HttpServletRequest req, HttpServletResponse res)
			throws ServletException, IOException {
		// Handle POST requests
	}

	public void doPut(HttpServletRequest req, HttpServletResponse res)
			throws ServletException, IOException {
		// Handle PUT requests
	}
}
```

#### JSP

- Jakarta Server Pages
- Typically contains HTML with embedded Java code.
- Typically compiled into servlets.
- Provides several implicit objects that are available for use without explicit declaration: request, response, session, etc.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
	<title>My First JSP</title>
</head>
<body>
	<h1>Hello, JSP!</h1>
	<%
		String name = "World";
		out.println("Hello, " + name + "!");
	%>
</body>
</html>
```

```jsp
<!-- Scripting Elements -->

<!-- Declarations -->
<%!
	int count = 0;
	void incrementCount() {
		count++;
	}
%>

<!-- Scriptlets -->
<%
	incrementCount();
	String message = "You are visitor number: ";
%>

<!-- Expressions -->
<p><%= message + count %></p>
```

#### JAX-RS

- Jakarta RESTful Web Services

```java
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;

@Path("/api")
public class HelloResource {
	@GET
	@Produces(MediaType.TEXT_PLAIN)
	public String hello() {
		return "Hello, RESTful World!";
	}
}

```

## Miscellany

### Data Fetching

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URI;
import java.net.URL;

public class FetchData {

	public static void main(String[] args) {
		String urlString = "https://jsonplaceholder.typicode.com/todos";
		try {
			// Create a URI object & Convert to a URL
			URI uri = new URI(urlString);
			URL url = uri.toURL();

			// Open a connection to the URL
			HttpURLConnection connection = (HttpURLConnection) url.openConnection();

			// Set the request method (GET, POST, etc.)
			connection.setRequestMethod("GET");

			// Get the response code
			int responseCode = connection.getResponseCode();
			System.out.println("Response Code: " + responseCode);

			// Read the response
			BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
			String inputLine;
			StringBuffer content = new StringBuffer();
			while ((inputLine = in.readLine()) != null) {
				content.append(inputLine);
			}

			// Close the connections
			in.close();
			connection.disconnect();

			// Print the fetched data
			System.out.println("Fetched Data: " + content.toString());
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

### Deployment (Examples)

#### Simple

```
src
└── com
	└── example
		├── HelloWorld.java
		└── MessageUtil.java
```

```java
// MessageUtil.java
package com.example;

public class MessageUtil {
	public static String getGreeting() {
		return "Hello, World!";
	}
}
```

```java
// HelloWorld.java
package com.example;

import java.util.logging.Logger;
import java.util.logging.Level;

public class HelloWorld {
	private static final Logger LOGGER = Logger.getLogger(HelloWorld.class.getName());

	public static void main(String[] args) {
		LOGGER.info("Application starting");
		String message = MessageUtil.getGreeting();
		System.out.println(message);
		LOGGER.info("Application ending");
	}
}
```

- Compile java files using `javac`.
    - The command below compiles all Java files in the `src/com/example` directory and puts the resulting `.class` files in the `bin` directory.

```bash
javac -d bin src/com/example/*.java
```

- Create a manifest file (`MANIFEST.MF`) in a `META-INF` directory.

```
Manifest-Version: 1.0
Main-Class: com.example.HelloWorld
```

- Use the `jar` command to create a JAR file.
    - The command below creates a JAR file named `HelloWorld.jar` with the manifest file and all compiled classes in the `bin` directory.
    - For this simple application, the `HelloWorld.jar` file is the deployment artifact.
        - In a real-world scenario, you might also include:
            - A `README` file with instructions
            - Any necessary configuration files
            - A startup script (e.g., a shell script or batch file)

```bash
jar cvfm HelloWorld.jar META-INF/MANIFEST.MF -C bin .
```

- Test the JAR file locally.

```bash
java -jar HelloWorld.jar
```

- To deploy:
    - Transfer the `HelloWorld.jar` file to the production server.
    - Ensure the target server has the appropriate Java Runtime Environment (JRE) installed.
    - Create a simple shell script (`run.sh`) to execute the JAR
    - Make the script executable

```bash
# run.sh

#!/bin/bash
java -jar HelloWorld.jar
```

```bash
chmod +x run.sh
```

```bash
./run.sh
```

#### [[Docker]]

```dockerfile
FROM maven:3.8.4-openjdk-17-slim AS build

WORKDIR /app

COPY pom.xml .

COPY src ./src

RUN mvn clean package -DskipTests


FROM openjdk:17-jdk-slim

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

CMD ["java", "-jar", "app.jar"]
```

### Enumerations (Enums)

- A special data type in Java that represent a group of named constants.
    - The constants are typically named in uppercase letters.

```java
enum Shape { CIRCLE, SQUARE, RECTANGLE }
```

- Essentially a special class in Java that cannot be extended or inherited.
    - They are implicitly `public`, `static`, and `final`.
- Commonly used to represent a fixed set of values, such as days of the week, months, directions, etc.
    - They help ensure type safety and prevent invalid values.
- Can be used in `switch` statements, and the `values()` method can be used to iterate over all the enum constants.
- Values are zero-indexed, and their indexes can be accessed using the `ordinal()` method.

```java
enum Color {
	RED, BLUE, GREEN;
}

Color c = Color.RED;
System.out.print(c.ordinal());  // 0
```

- Enums Can have their own methods, variables, and constructors, just like regular classes.

```java
enum EV {
	RIVIAN(80000), LUCID(100000), TESLA;

	private int msrp;

	private EV() {
		this.msrp = 50000;
	}

	private EV(int msrp) {
		this.msrp = msrp;
	}

	public int getPrice() { /*...*/ }
	public void setPrice() { /*...*/ }
}

Color c = Color.RED;
System.out.print(c.ordinal());  // 0
```

### Varargs

- Variable-length arguments
- Allow methods to accept an arbitrary number of arguments of the same type.
- Declared using three dots (`...`) after the parameter type.
    - e.g. `public static void methodName(int... numbers)`
- Must be the last parameter in a method's parameter list.
- Can take zero or more arguments.
- Internally implemented as arrays.
- Only one varargs parameter is allowed per method.
- Backwards compatible with methods that accept arrays.
- Considered best practice to:
    - Use varargs sparingly and only when the benefit is compelling.
    - Avoid overloading varargs methods to prevent ambiguity.

```java
public static void printNums(int... nums) {
	for (int num : nums) {
		System.out.print(num + " ");
	}
	System.out.println();
}

printNums(1, 2, 3);
printNums(4, 5, 6, 7, 8);
printNums();
```

### Annotations

- A form of metadata that provide additional information about a program, but do not directly affect its execution.
    - Used to provide supplemental information or instructions to the compiler, development tools, frameworks, or the JVM, and they can be applied to various program elements, including classes, interfaces, methods, fields, parameters, and local variables.
    - Start with the `@` symbol, followed by the annotation name and optional elements or values.
    - Widely used in various Java frameworks, libraries, and tools, such as JUnit for testing, Hibernate for [[ORM]], and Spring for dependency injection.
- While they do not change code behavior, but they can be processed and utilized by various tools and libraries.
    - They can be accessed and processed at runtime using reflection or annotation processors.
- Java provides several built-in annotations, such as `@Override`, `@Deprecated`, `@SuppressWarnings`, and `@FunctionalInterface`.
- Can have elements (members) that can be assigned values when the annotation is used.
- Can be classified into different categories:
    - Marker annotations - e.g. `@Override`
    - Single-value annotations
    - Full annotations
    - Type annotations
    - Repeating annotations
- Custom annotations can also be defined by creating an annotation type.

### Build Path

- Used by an IDE like Eclipse to determine where to find the source code files, libraries, and other resources required to build (compile) a Java project.
- An IDE-specific concept and is not used directly by the Java compiler or runtime.
- Specifies the locations of:
    - Source code folders containing .java files
    - External libraries/JARs required for compilation
    - Other projects that the current project depends on
- IDEs use the build path to construct the appropriate classpath for compilation.

### Regular Expressions

- Functionality provided through `java.util.regex`.

```java
import java.util.regex.*;

String emailRegex = "^[a-zA-Z0-9_+&*-]+(?:\\.[a-zA-Z0-9_+&*-]+)*@(?:[a-zA-Z0-9-]+\\.)+[a-zA-Z]{2,7}$";
String emailAddr = "example@example.com";

Pattern emailPattern = Pattern.compile(emailRegex);
Matcher emailMatcher = emailPattern.matcher(emailAddr);

if (emailMatcher.matches())
	System.out.println("Valid email address: " + email);
else
	System.out.println("Invalid email address: " + email);
```

### New Features

#### LVTI

- Local Variable [[Type Inference]]
    - `var` can be used to initialize a local variable.
    - Available since Java 10
    - It's not possible to use `var` on a variable without initializer.
    - `var` can be used as both a variable name and a keyword.

```java
var nums = new ArrayList();

int a; // Valid ✅
var b; // Invalid ⛔
```

#### Records

- A concise way to define immutable data classes, reducing the boilerplate code typically required for classes that simply hold data (such as getters, setters, constructors, `equals()`, `hashCode()`, and `toString()`).
- Introduced in Java 14.
- Implicitly final and immutable by default.
    - All fields are `public`, `final`, and accessible via methods named after the fields.

```java
record Person(String name, int age) {
	public void greet() {
	System.out.print("Hi, my name is " + name + ".");
	}
}

/* ... */
Person person = new Person("John Doe", 33);

// Accessing record fields
System.out.println("Name: " + person.name());
System.out.println("Age: " + person.age());

// Calling a record method
person.greet();

// Printing the record
System.out.print("Person: " + person);
```

- Common methods like `equals()`, `hashCode()`, and `toString()` are automatically generated.
- Can have additional methods and constructors defined within the record body, allowing for custom behavior if needed.
- Useful for creating simple data carrier classes, also known as Plain Old Java Objects (POJOs) or Data Transfer Objects (DTOs), where the focus is on containing and transporting data rather than complex logic.

### JVM

- The JVM is the core of the Java ecosystem, enabling Java-based software programs to run on any machine that has a JVM installed.
- The JVM creates an isolated space on a host machine, allowing Java programs to execute regardless of the platform or operating system of the machine, which is a key feature that supports the "write once, run anywhere" approach.
- Java code is first compiled into bytecode, which is then interpreted by the JVM on the target machine. This allows Java programs to be platform-independent.

#### Architecture

- **Class Loader** is responsible for loading Java classes into the JVM.
    - It reads the bytecode files (.class files), verifies them, and loads them into the JVM.
- **Runtime Data Areas** are the memory areas allocated by the JVM for the execution of Java programs.
    - Key areas include the heap (for dynamic memory allocation), the method area (for storing class and method data), and the stack (for storing local variables and partial results).
- **Execution Engine** executes the bytecode. It can use an interpreter or a [[Just-In-Time Compilation|Just-In-Time]] (JIT) compiler to convert bytecode into machine language instructions for execution.
    - The JIT compiler improves performance by compiling bytecode into native machine code at runtime.
        - **Heap** is a region of memory used for dynamic memory allocation.
            - It is where objects are allocated and deallocated.
        - **Stack** contains frames, each of which corresponds to a method invocation.
            - It stores local variables and partial results.
        - **Method Area** is where the JVM stores class and method data, including the runtime constant pool, field and method data, and the code for methods and constructors.
- **Native Method Interface (JNI)** allows Java code to call and be called by native applications and libraries written in other languages such as C, C++, and assembly.

#### Class Path

- Used by the Java compiler (`javac`) and the JVM to locate the `.class` files required for compilation and execution respectively.
- A core Java concept and is used directly by `javac` and the JVM.
- Typically specified using the `-cp` or `-classpath` command line option, or the `CLASSPATH` environment variable.
- Specifies the locations of:
    - The root directories or JAR files containing `.class` files
    - External libraries/JARs required at runtime
- The IDE constructs the appropriate class path based on the configured build path when compiling or running a Java program.

#### Stack vs. Heap

![](assets/images/java.stack-vs-heap.png)

#### Garbage Collection

![# Java's Garbage Collection Explained](https://www.youtube.com/watch?v=Mlbyft_MFYM)

## Best Practices

### Naming Conventions

- Variables and methods should start with a lowercase letter and be camel cased.
- Constants should be defined in all uppercase letters.
- Classes and interfaces should start with a uppercase letter and be camel cased.

---

## Further

### Reads 📄

- [Java Practices -> Package by feature, not layer](http://www.javapractices.com/topic/TopicAction.do?Id=205) ⭐

### Resources 🧩

- [Java & Databases](https://www.marcobehler.com/guides/java-databases) ⭐
