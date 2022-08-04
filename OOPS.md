# <center>OOPS</center>
Dart is an object-oriented programming language.The main goal of oops is to reduce programming complexity and do several tasks simultaneously.

> #### The four pillars of oops:
> 1. Abstraction
> 2. Encapsulation
> 3. Inheritance
> 4. Polymorphism

### <u>Abstraction</u>
- Abstract class in Dart is defined as those classes which contain one or more than one abstract method.
- Its main goal is to handle complexity by hiding unnecessary  details from the user.
- In Dart, you can use the ***abstract*** keyword only on classes because abstract methods simply don’t have a body

```dart
abstract class Example {
  /// This is an abstract method because it has no body.
  void methodOne();

  /// This is NOT an abstract method because it has a body.
  void methodTwo() {}
}
class AnotherExample extends Example {}
class AnotherExample extends Example {}
```

**Features of Abstract Class:**  

1. A class can be declared abstract by using **abstract** keyword only.
2. A class declared as abstract **can’t be initialized**. You can access it by **Inheriting** or **Interface** with another class.
3. If you uses **Inheriting** by keyword ***extends*** , You can override one or more properties or methods.
4. If you uses **Interface** by keyword ***implements*** , You must all the properties and methods.

**Syntax:**

>abstract class class_name { <br>
>  // Body of the abstract class <br>
>}

**Note:**

>It is not mandatory to **override** the method when there is only one class extending the abstract class, 
>because **override** is used to change the **pre-defined code** and as in the above case, nothing is defined inside the method so the above code will 
>work just fine without override.

<hr>

### <u>Encapsulation</u>
Encapsulation is the mechanism that confirms critical and sensitive data which is hidden from users. 

#### Private Fields
In dart, no public or private keywords are used. So, to make the variable private, underscore ( _ ) is used as a prefix to that field name.

```dart
class Employee {
  var _name;
}

void main() {
  var employee = Employee();
  employee._name = "Jack";
  print(employee._name);
}
```

#### Read-only Fields:
You can do that by adding the final keyword before the field declaration. Hence, you can only access its value but no new value can be set.

```dart
class Employee {
  final _name="Jack";
}

void main() {
  var employee = Employee();
  print(employee._name);
}
```

<hr>

### <u>Polymorphism</u>
When we inheriting from the paren class we can customize the parent class methods.
It reduces the time and effort to write repetitive code and provides you with the flexibility to override.

#### Polymorphism By Method Overriding

```dart
class ParentClass{
void functionName(){
  }
}
class ChildClass extends ParentClass{
@override 
void functionName(){
  }
}
```

>Note: You use **@override** annotation to note/comment while overriding the method.

<hr>

### <u>Interfaces</u>
If a class inherits another it should redefine each function present inside an interfaced class in its way. 
we have to make use of **implements** keyword to do so.

```dart
class Geek { 
   void printdata() { 
      print("Hello Geek !!"); 
   } 
}  
  
class Gfg implements Geek { 
   void printdata() {  
      print("Welcome to GeeksForGeeks"); 
   } 
}
```
#### Multiple Inheritance in Dart
Although practically dart doesn’t support multiple inheritances, it supports multiple interfaces.

>class class_name implements interface_class1, interface_class2, ...., interface_classN { <br>
>}



<hr>

### <u>MIXINS</u>

Mixins are useful when you need code sharing without using inheritance.
A mixin is a sort of class that can be “associated” to another class in order to reuse pieces of code without using inheritance. 
It requires the **with** keyword.

```dart
mixin Breathing {
  void swim() => print("Breathing");
}

mixin Walking {
  void walk() => print("Walking");
}

mixin Coding {
  void code() => print("print('Hello world!')");
}

/// This class now has the `walk()` method
class Human with Walking {}

/// This class now has the `walk()` and `code()` methods
class Developer with Walking, Coding {}
```

### <u>Metadata Annotation</u>
A way of adding extra information to any component in our code, such as class or a method.

>we use **@required** to specify that a named parameter is not optional, so our code won’t compile if the field annotated is not present.

>we use **@override** to identify those APIs given by a parent class that are implemented in a child class.

***Any class can be transformed into an annotation, as long as they provide a const constructor:***

<hr>

### <u>Callable Classes</u>

To allow an instance of your Dart class to be called like a function, implement the call() method. 

>class class_name {  
>  return_type call ( parameters ) {
>  } 
>}

```dart
class GeeksForGeeks {
	
String call(String a, String b, String c) => 'Welcome to $a$b$c!';
}

void main() {
var geek_input = GeeksForGeeks();
var geek_output = geek_input('Geeks', 'For', 'Geeks');
print(geek_output);
}

```

#### Note:

> It must be noted that Dart doesn’t support multiple callable methods inside a class.


# <center> Dart Utilities: </center>

### <u>Date and Time</u>

### <u>ASYNCHRONOUS PROGRAMMING</u>

Asynchronous programming says  ***"Don't wait for us, We will call you back."***.

#### Synchronous Programming:

In Synchronous programming, the program is executed line by line, one at a time. 

```dart
main() {
print("First Operation"); 
print("Second Big Operation"); 
print("Third Operation"); 
print("Last Operation"); 
}
```
O/p:
First Operation.
Third Operation.
Last Operation.
Second Big Operation.

**Disadvantage:**
Let’s suppose Second Big Operation takes 3 seconds to load then Third Operation and Last Operation need to wait for 3 seconds.
To solve this issue asynchronous programming is here.

### <u>Asynchronous Programming</u>

In Asynchronous programming, program execution continues to the next line without waiting to complete other work.
It simply means ***"Don't wait"***.

```dart
main() {
print("First Operation");   
Future.delayed(Duration(seconds:3),()=>print('Second Big Operation'));
print("Third Operation"); 
print("Last Operation"); 
}
```
O/p:
First Operation.
Second Big Operation.
Third Operation.
Last Operation.

### <u>Future In Dart</u>
In dart, the Future represents a value that is not yet available, that will be available at some time in the future. 

***State Of Future***
1. Uncompleted
2. Completed

**Uncompleted** :

When you call an asynchronous function, it returns to an uncompleted future. 
It means the future is waiting for the function asynchronous operation to finishor to throw an error.

**Completed** :

It can be completed with value or completed with error. 
Future<int> produces an int value, and Future<String> produces a String value. If the future doesn’t produce any value, then the type of future is Future<void>.
 


### <u>Async And Await In Dart</u>
Both async and await keywords provide a declarative way to define an asynchronous function and use their results.

**Important Concept**
- To define an Asynchronous function, add async before the function body.
- The await keyword work only in the async function.

 ```dart
 main() {
print("Start");
getData();
print("End");
}

void getData() async{
  String data = await middleFunction();
  print(data);
}

Future<String> middleFunction(){
  return Future.delayed(Duration(seconds:5), ()=> "Hello");
}
 ```
O/p:
Start
End
Hello



# <center>Exception Handling</center>

**Exception In Dart**:
An exception is an error that occurs at runtime during program execution. 

```dart
try {
.....
}
on Exception1 {
....
}
catch Exception2 {
....
}
finally {
// code that should always execute whether an exception or not.
}
```

**Example**:

```dart
void main() {
  int a = 12;
  int b = 0;
  int res;
  try {
    res = a ~/ b;
  } on UnsupportedError {
    print('Cannot divide by zero');
  } catch (ex) {
    print(ex);
  } finally {
    print('Finally block always executed');
  }
}
```
