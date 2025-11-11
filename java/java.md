## What is IDE ?
- stands for Integrated Development Environment
- can type code , run code , compile code, debug code

### Layers
- JDK (inside it we have)  -> JRE (inside it we have)  -> JVM

## JDK (Java Development Kit)
- you need a compiler to your machine can understand that
- so in java terms we need to download JDK
- allows LTS (Long Term Support) version

## How java work
- use JVM and JVM itself is platform dependent
- java is WORA (write once run anywhere)

## Object oriented
- java is Object oriented means everything should be an object and to create object we need to class

## JRE (Java Runtime Environment)
- To have all extra files  you need to extra classes 
## JVM (Java Virtual Machine)
- its part of JRE 
- if you don't have JRE also you don't have JVM
####
- why need JDK?
  - to convert my code into byte code to run it in machine.
- which machine run on it ? run code on JVM
  - cc 
---

## Primitive and Wrapper Types

| Primitive | Wrapper   | Bits | Default (fields) | Notes / Typical Range               |
|-----------|-----------|-----:|------------------|-------------------------------------|
| boolean   | Boolean   |  —   | false            | true/false (size JVM-dependent)     |
| byte      | Byte      |    8 | 0                | -128 … 127                          |
| short     | Short     |   16 | 0                | -32,768 … 32,767                    |
| int       | Integer   |   32 | 0                | -2^31 … 2^31-1                      |
| long      | Long      |   64 | 0L               | -2^63 … 2^63-1                      |
| float     | Float     |   32 | 0.0f             | ~7 decimal digits precision         |
| double    | Double    |   64 | 0.0d             | ~15–16 decimal digits precision     |
| char      | Character |   16 | '\u0000'         | UTF-16 code unit: 0 … 65,535        |

*Note: “Default” applies to fields/array elements (locals must be initialized).*
- Use primitives for speed/memory. 
- Use wrappers when null or generics are needed. 
- Use StringBuilder for repeated concatenation. 
- Use BigDecimal for money. 
- Prefer enum/record for clear domain models. 
- Remember .equals() for object content comparison.
- String - is combination of different characters.

## Literal
- Literal in Java = a fixed value written directly in your code (not a variable, not a calculation).


## 🧮 Type Conversion and Casting in Java

### what is conversion ?
- Conversion is an automatic type change done by Java (also called type promotion or implicit conversion).

### what is casting
- Casting is an explicit type change done by the programmer.

- Example with a range of byte
```java
int a = 12;
byte b = (byte) a;

// b = 12 because 12 is within the byte range (-128 to 127)
```

- what happens when you have a bigger value ? Example with a bigger value
```java
// 

int a2 = 257; // 257 is out of byte range
byte b2 = (byte) a;

// Result: b2 = 1
// Why? Because 257 % 256 = 1 (byte range is 256 values)
// because divide 257 by byte range which is 256 to find remainder which is 1
```

### Type Promotion 
- What do you mean by type promotion in Java? 
  - conversion of data from lower to higher data type 

```java
    byte a = 10;
    byte b = 30;
    
    int result = a * b; 
// This is called type promotion because the result of a * b 
// exceeds the byte range, so Java automatically promotes it to int.


```



## 8. Increment and Decrement Operators
- Pre-Increment (++a)
  - The value is increased first, then used in the expression.
```java
    int a = 5;
    int b = ++a; // a = 6, then b = 6
    System.out.println(a); // 6
    System.out.println(b); // 6
```

- Post-Increment (a++)
  - The value is used first, then increased.
 ```java
    int a = 5;
    int b = a++; // b = 5, then a = 6
    System.out.println(a); // 6
    System.out.println(b); // 5
```


## 9. Arithmetic Operators / Relational Operators / Logical Operators

### 10. Ternary Operator (if else use question mark & colon)
- int result = true ? 10 : 20;


### 11. Ternary

### 12. Switch statements
- do something multiple time based on condition

### 13. Loops (for | while | do while| forEach)
- `while loop` - if condition is true , it will execute block otherwise it will not execute block
- `do while loop` -  it will execute block at least once even if condition is false

```java
   public static void main(String[] args) {

  int age = 25;


  while (age < 30){
    System.out.println("age = " + age);
    age++;
  }

  do {
    System.out.println("age = " + age);
    age++;
  }while (age < 30);

  for (int i = 1; i <= 5; i++) {
    System.out.println("i = " + i);
  }
}
```
### 14. Object 
- what object knows ? variables , data 
- what object does? methods
- to create object say to JVM , I want new object use `new` after get reference can use to call methods



### 15. Method Overloading
- create two methods with same name but have different parameters
- int add(int n1) 
- int add(int n1 , int n2)
- int add(double n1 , int n2)  can change parameters
- double add(int n1 , int n2) -- can not change return if it's same
- can change parameters but can not change return if it's same
### 16. Method Overriding
- overrides the method of parent in child class
- same name + same type parameter + same return + anther bhevier
- more about inherent

# 17. String
- it's class , not a primitive type 
- string only use plus operator to concatenate 
- if its class so why don't use as new String("mohammed") ? because behind the seen it will create object if assign value like = "mohammed"
- 

- string in heep memory it's create new object with address like 105
- so if change value or append new value what happened?
- you are not changing the existing string if value its chane will create new reference with new value like = "mmmm"
- if create new variable with value = "mohemmd" if is exited so no crate new reference just assing reference







# 17. String
- String is a class, not a primitive type like int or char.
- You can use + to join (concatenate) strings
```java
   public static void main(String[] args) {

    String name = "Mohammed";
    //But normally we just write "Mohammed", because Java automatically creates the object behind the scenes.
    String s = new String("Mohammed");
}
```

### Memory and Behavior
- Strings are stored in heap memory.
- Example: "Mohammed" is stored at some memory address (like 105).
- When you change or add something to a string, Java creates a new object — it doesn’t change the old one. 
- This is called immutability — strings cannot be changed.

### 🔁 String Pool (Reuse)
- If you create another variable with the same value, Java doesn’t create a new object — it reuses the same one:

```java
   public static void main(String[] args) {
    String a = "Mohammed";
    String b = "Mohammed";  // points to the same object

}
```

## StringBuilder &  StringBuffer
- its mutable can chang it
- it's give you buffer size (16 bytes extra)
- you can change value using append("")
- wht 
  - only one difference string buffer is thead safe and string builder is not 
  - what is thread safe ? means that we

- StringBuilder: mutable, not thread-safe → fastest for single-thread code
- StringBuffer: mutable, thread-safe (methods are synchronized) → a bit slower, use it when multiple threads may edit the same builder.

#### When to use which?
- Building text in a loop (e.g., concatenating many pieces): use StringBuilder.
- Building text in a multi-threaded context where the same builder is shared: use StringBuffer.


- Both mutable and faster than String.
- Use StringBuilder for normal code (single-thread).
- Use StringBuffer when many threads share the same object.
- Both are mutable, meaning you can change their value after creation.
- hey have a buffer (extra space) — usually 16 extra characters - so they don’t need to create new memory every time you add text.
  ou can change value using: append(), insert(), delete(), replace(), reverse()

### ⚙️ Main Difference
StringBuilder : Not thread-safe , Faster
StringBuffer : Thread-safe , Slower (because synchronized)

### What is “Thread-safe”?
```java
   public static void main(String[] args) {
    StringBuilder sb = new StringBuilder("Hello");
    sb.append(" World");  // changes same object, no new one created


}
```



### Inherits
- Every class in java extend class Object by defaults

### Access Modifiers
- Access modifiers control who can see or use a class, method, or variable.
- There are three main access modifiers: public, private, and protected.
  - `public`: 🔓 Accessible from `everywhere` — any class, any package. Used when you want something to be completely open.
  - `privet`: 🔒 Accessible only inside the `same class`.Used to hide data and protect it from outside access.
  - `protected`: 🧩 Accessible within the `same package` and by `subclasses` (even if they are in another package).

### Polymorphism (means "many forms")
- It's poly (many) and morphism (behaves)
- many behavior 





- it allows one object to behave in different ways depending on the situation.
- It is one of the main pillars of Object-Oriented Programming (OOP) along with inheritance, encapsulation, and abstraction.
#### Types of Polymorphism
- Compile-time Polymorphism (Method Overloading)
  - Happens when multiple methods have the same name but different parameters (different number or type).
  - The compiler decides which method to call at compile time.
- Runtime Polymorphism (Method Overriding)
  - Happens when a subclass provides a new version of a method that already exists in its parent class.
  - The method to run is decided at runtime, based on the actual object type.

  

### Final keyword
- `Class`: to stop inheritance
- `variable`: to make it constant
- `method`: to stop method overriding




### 30. Abstract (class + method)
- Used for common behavior with different implementations in subclasses
- If a class has an abstract method → the class must be abstract
- You can’t create an object of an abstract class 
- You can have normal (non-abstract) methods in abstract class


### 31. Inner class 
- It’s a class defined inside another class.
- Used to group logic that belongs only to the outer class.
- Static keyword can be used only with inner class

### 32.Anonymous liner class 
- when create object can provide method 

### 33. Interface  - (so use with implements keyword) 

#### 1️⃣ What is an Interface?
- It’s a blueprint (design) for classes.
- It defines what methods a class must have, but not how to do them.
- You use it when you want to say:“Any class that implements me must follow these rules.”
- ⚙️ Rules
  - All methods are public and abstract by default
  - You cannot create an object of an interface
  - All variables are public static final (constants)
  - An interface does not have its own memory in the heap
  - Use implements to connect a class with an interface
  - Use extends to connect one interface to another
  

#### 🧩 Types of Interfaces in Java (3 main types)
- `Normal Interface`: Has more than one abstract method -> `interface Animal { void eat(); void sleep(); }`
- `Functional Interface` : Has only one abstract method (used with Lambda Expressions) -> Ex: `interface Runnable { void run(); }`
- `Marker Interface` : Has no methods at all (used to give a “mark” to a class) -> Ex : `interface Serializable { }`


```java
interface Animal {
    void eat();   // public abstract by default
    void sleep();
}
class Dog implements Animal {  // 👈 use 'implements'
  public void eat() {
    System.out.println("Dog is eating...");
  }
  public void sleep() {
    System.out.println("Dog is sleeping...");
  }
}
//--------
interface Pet extends Animal {   // 👈 interface extends another interface
  void play();
}

```



- every method in interface is public abstract by default
- can't instantiate 
- just give design and you have ti literally take a walk
- I will tell you what are the method you need, but I will not implement them it's your job to implement.
- all variable in interface are final and static
- interface don't have their own memory in teh heap.

- `class` to `class` -> extends
- `class` to `interface` -> implement
- `interface` to `interface` -> extends






### 34.Enums
#### 1️⃣ What is an Enum?
- s a special type in Java used to define a set of fixed constants (values that don’t change).
- Example: days of the week, order status, directions, etc.
- Enum is a special class that represents fixed constants
- Enum extends java.lang.Enum automatically (so you can’t extend another class)

```java
enum Day {
  MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

enum Status {
    NEW("Not started"),
    IN_PROGRESS("Work ongoing"),
    DONE("Completed");

    private final String description;

    Status(String description) {
        this.description = description;
    }

    public String getDescription() {
        return description;
    }
}
```

### 35.Annotation
#### What is an Annotation?
- Annotation is like a special note or metadata added to your code.
- It gives instructions to the compiler or runtime, not directly part of the logic.
- Used for information, compiler checks, or framework configuration (like in Spring Boot).

#### 🧠 Common Java Annotations
- `@Override` :Used when a method overrides a method from a parent class or interface. Helps the compiler check correctness. 
- `@Deprecated` :Marks something as old — you can still use it, but it’s not recommended.



### 36. Exception
### Type of error 
- Compile time error
- Runtime Error : everything is working but the code just stopped working (Exception is runtime error)
- Logical error : everything is working but the output is wrong (bags)

- catch 
  - catch block will be executed only in case of exception
  - can write mutable catch for one try
  - for save side allows add Exception to handles everything in the end 
  - the sequence is whenever you have child and parent make sure that parent is the bottom



- Ducking exception 
- trows 
- trow
- custom exception should be extend Exception class or RuntimeException class

### 36. Exception

- 🔹 What is an Exception?
  - An Exception is an unexpected problem that occurs during program execution (runtime).
    When it happens, the program stops unless we handle it properly.

#### ⚙️ Types of Errors
![img_4.png](img_4.png)


#### ⚡ Example of Runtime Exception

```java

public class Test {
    
    public void print(){
      try { // Code that might cause an exception
        int x = 10 / 2;
      }
      catch (ArithmeticException e) { // Code that handles the exception
        System.out.println("Math problem!");
      } catch (Exception e) { // 👈 Always keep parent Exception last
        System.out.println("Error!");
      } finally {
          //Code that always runs & Usually used to close connections, files, etc.
        System.out.println("Always executes");
      }
    }
}
```
### Throw vs Throws


```java
public class Test {
  // throw : Used to throw an exception manually Inside method
  public void checkAge(int age) {
    if (age < 18) {
      throw new ArithmeticException("Not allowed");
    }
  }

  // throws : Used to declare an exception (warning that method may throw it) In method signature
  public void readFile() throws IOException {
    FileReader fr = new FileReader("data.txt"); //// doesn’t handle, just “ducks” it upward
  }

}
```
### 🦆 Ducking Exception
- Passing the exception to another method (parent) (using throws)

### 🧰 Custom Exception
- You can create your own exception class — extend either
  - `Exception` → checked exception
  - `RuntimeException` → unchecked exception

```java
class InvalidAgeException extends Exception {
    public InvalidAgeException(String message) {
        super(message);
    }
}

public class Test {
  static void checkAge(int age) throws InvalidAgeException {
    if (age < 18)
      throw new InvalidAgeException("Age must be 18 or above");
  }
}
```


### Try-with-Resources
- It is used to automatically close resources (like files, streams, connections).
- Any resource that implements AutoCloseable (or Closeable) can be used inside it.

#### 🧱 Normal try–finally (old way)
```java

public class Test{
    public void test(){
      BufferedReader br = null;
      try {
        br = new BufferedReader(new FileReader("data.txt"));
        System.out.println(br.readLine());
      } catch (IOException e) {
        e.printStackTrace();
      } finally {
        try {
          if (br != null) br.close();  // must close manually
        } catch (IOException e) {
          e.printStackTrace();
        }
      }
    }
}

```
#### ⚙️ Try-with-Resources (new way)
- ✅ Once the try block finishes (normally or with exception),
- br.close() is called automatically.
- No need for a finally block.

```java

public class Test{
    public void test(){
      try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
        System.out.println(br.readLine());
      } catch (IOException e) {
        e.printStackTrace();
      }
      //Multiple Resources Example -> ✅ Both br and fw will be closed automatically.
      try (
              BufferedReader br = new BufferedReader(new FileReader("input.txt"));
              FileWriter fw = new FileWriter("output.txt")
      ) {
        fw.write(br.readLine());
      } catch (IOException e) {
        e.printStackTrace();
      }

    }
}

```
#### 🧠 Why Does It Work Automatically?
- Because classes like BufferedReader → extend Reader → which implements Closeable
- Closeable extends AutoCloseable
- AutoCloseable has one method: void close()












------------------
### 37. Collection
- `Collection Api` - talking about concept
- `Collection` - talking about an interface
- `Collections` - it's your class

- List (Arraylist)- 
- Set (HashSet) 
  - is collection of unique value
  -  will not give value whit sorted format
  - if need sorted can use TreeSet
- Map (HashMap)
  - is a collection of key and value pair
  - can not have keys repeated and if repeated this means set new value for this key






#### 38. Stream
- 🔹 What is a Stream?
  - Sequence of elements supporting functional operations(like filter, map, reduce, etc.).
  - It’s used to process collections (List, Set, etc.) efficiently and cleanly.
  - ⚠️ Important Rule : You can use a Stream only once. If you try to reuse it, you’ll get:
  `java.lang.IllegalStateException: stream has already been operated upon or closed`
#### 🧱 Main Stream Operations
- `filter()` : Filters elements based on a Predicate
- `map()` : Transforms each element (change value or type)
- `mapToInt()` : Converts elements to int values (for sum, avg, etc.)
- `forEach()` : Performs an action for each element
- `reduce()` : Combines elements to produce a single value
- `findFirst()` : Returns first element that matches condition
- `toList()` : Collects stream elements into a List


#### ⚙️ Parallel Stream (Uses multi-threading)
- Used to process data using multiple threads for speed (especially for large collections).
- 🧠 Note: order is not guaranteed with parallelStream().
```java
public class Test {
    public void test(){
      List<Integer> numbers = List.of(1,2,3,4,5,6,7,8,9,10);
      numbers.parallelStream()
              .forEach(System.out::println);

    }
}
```
#### ⚡ Method Reference
- Shortcut for lambda expressions.
- 1 - `Method Reference` : .map(String::toUpperCase) --> ClassName::methodName
- 2- `Constructor Reference` : .map(User::new) --> ClassName::new

#### ❓ What is a Predicate?
- A Predicate is a functional interface that takes one input and returns a boolean. 
- Used commonly in filter().

```java
public class Test {
    public void test() {
      List<Integer> nums = List.of(1, 2, 3, 4, 5);
      List<String> names = List.of("Ali", "Sara", "Mohammed", "Omar");
      Predicate<Integer> isEven = n -> n % 2 == 0;

      int result = nums.stream()
                .filter(isEven)
                .mapToInt(n -> n * 2)
                .reduce(0, Integer::sum);
      System.out.println(result); // 12
      
      names.stream()
              .filter(n -> n.startsWith("M"))       // filter
              .map(String::toUpperCase)             // convert to uppercase
              .forEach(System.out::println);        // print each
    }
    
}
```

------------













- Note: you can use stream only once or get run time error stream has already been operated upon or closed

- filter
- map : if you need change value
- forEach method
- reduce - return single value
- findFirst
- toList
- parallelStream - multiple tread

- what is Predicate ?
- mapToInt
- method reference:  mention where the method belongs to and (::) then method name eg: .map(String::toUpperCase)
- constructor reference:  mention where the class name  (::) then new  eg: .map(User::new)
### 39. Optional
- 