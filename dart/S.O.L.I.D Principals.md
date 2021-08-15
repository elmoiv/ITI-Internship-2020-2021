# S.O.L.I.D Coding in Dart

The principle of SOLID is made by Robert C. Martain; An American Software Engineer generally known as "Uncle Bob".

By Applying these principles, you can enhance the quality of your code and make it more logically consistent.

## SOLID Principles

There are 5 SOLID principles:
  - [The Single-Responsibility Principle](#1-the-single-responsibility-principle-srp)
  - [The Open-Closed Principle](#2-the-openclosed-principle-ocp)
  - [The Liskov Substitution Principle](#)
  - [The Interface Segregation Principle](#)
  - [The Dependency inversion Principle](#)

Just like **DRY**: *Don't Repeat yourself* and **KISS**: *Keep It Small and Simple*, These principles descirbe a decades of development best practices which were gathered and organized into a set of 5 acronyms for the sake of simplicity.

Generally, the SOLID principles are basic learning steps for every code developer to maintain highest quality for his code. You can call them a **Programming Blueprints**.

Let's explain these principle in some detail:

## 1) The Single-Responsibility Principle (SRP)

> ### “A class should have one, and only one, reason to change”

Every class (can also be a function) should have one and only one responsibility. Thus, there will be only a reason to change it.

### **Bad Practice**:
A function that takes care of the entire process all at once:

```dart
void mathOps(var numbers) {
    print('the max is ${max(numbers)}');
    print('the max is ${min(numbers)}');
    print('the mean is ${mean(numbers)}');
}

void main() {
    mathOps([12, 5, -3, 18]);
}
```

### **Good Practice**:
Applying SRP to the previous code we get:

```dart
void getMax(var numbers) {
    print('the max is ${max(numbers)}');
}

void getMin(var numbers) {
    print('the min is ${min(numbers)}');
}

void getMean(var numbers) {
    print('the mean is ${mean(numbers)}');
}

void getResult(var numbers) {
    getMax(numbers);
    getMin(numbers);
    getMean(numbers);
}

void main() {
    getResults([12, 5, -3, 18]);
}
```

This guaranties localizing errors and making your code reusable.

## 2) The Open–closed principle (OCP)

> ### “Software entities … should be open for extension but closed for modification”

Simply: You shouldn't need to modify the code you've already written to accommodate new functionality, but rather add what you need.

We can clarify this with the following bad practice:

### **Bad Practice**:

```dart
class VehicleCalculations {
    double calculateValue(Vehicle v) {
        if (v.runtimeType == Car) {
            return v.getValue() * 0.8;
        if (v.runtimeType == Bike) {
            return v.getValue() * 0.5;
    }
}
```

### **Good Practice**:
Applying SRP to the previous code we get:

```dart
abstract class Vehicle {
    double calculateValue();
}

class Car extends Vehicle {
    @override
    double calculateValue() {
        return this.getValue() * 0.8;
}

class Truck extends Vehicle{
    @override
    double calculateValue() {
        return this.getValue() * 0.9;
}
```

Now if we need to add a class called Bus we will only create a new class with no need to add a new `if-else` clause.


## 3) The Liskov substitution principle (LSP)

> ### “Functions that use pointers or references to base classes must be able to use objects of derived classes without knowing it”

We can simplify this concept as the following:
if in a *subclass* you redefine a function that is also present in the *parent class*, the two functions should have the same expected behaviour although they may have different outputs

We can understand it more with the following bad practice:

### **Bad Practice**:

```dart
class Car {
    String type;
    var properties;
}

void printCarsColors(List<Car> cars) {
    for (Car car in cars) {
        print(car.properties['color']);
    }
}

void main() {
    Car ferrari = Car();
    ferrari.properties = {'color': 'red', 'power': 250};

    Car lambo = Car();
    lambo.properties = ['green', 300];

    printCarsColors([ferrari, lambo]);
}
```

This will throw an error because `lambo` has it's properties set as a `List` however `printCarsColors` only accepts type of `Map`.

### **Good Practice**:
We can solve this problem through getter and setter:

```dart
class Car {
    String type;
    var _properties;

    Map get carProperties => this._properties;

    set carProperties(Map prop) => this._properties = prop; 
}

void printCarsColors(List<Car> cars) {
    for (Car car in cars) {
        print(car.carProperties['color']);
    }
}

void main() {
    Car ferrari = Car();
    ferrari.carProperties = {'color': 'red', 'power': 250};

    Car lambo = Car();
    lambo.carProperties = {'color': 'green', 'power': 300};

    printCarsColors([ferrari, lambo]);
}
```

This will prevent the user from adding different types in the Compile time and will guarantee the same behaviour all over objects fullfilling the LSP principle.


## 4) The Interface Segregation Principle (ISP)

> ### “Many client-specific interfaces are better than one general-purpose interface”

To avoid *subclasses* inheriting methods from *parent classes* that they don't need

### **Bad Practice**:

```dart
abstract class Mammals {
    void swim() {
        print('Can Swim');
    }

    void walk() {
        print('Can Walk');
    };
}

class Human implements Mammals {
    @override
    void walk() {
        print('Human Can Walk');
    }

    @override
    void swim() {
        print('Human Can Swim');
    }
}

class Whale implements Mammals {
    @override
    void swim() {
        print('Whale Can Swim');
    }
}
```

It appears as if this was a good practice but when we try to call `walk` method from `Whale` class like this:
```dart
void main() {
    Whale whale = Whale();
    whale.walk();
}
```

This will actually print: "Can Walk" however this is logically incorrect.

### **Good Practice**:
To solve this issue we craft a separate class for each action

```dart
abstract class Walker {
    void walk() {
        print('Can Walk');
    };
}

abstract class Swimmer {
    void swim() {
        print('Can Swim');
    };
}

class Human with Walker, Swimmer{
    @override
    void walk() {
        print('Human Can Walk');
    }

    @override
    void swim() {
        print('Human Can Swim');
    }
}

class Whale implements Swimmer {
    @override
    void swim() {
        print('Whale Can Swim');
    }
}
```

Here Whale dose not have the un-needed `walk` action method.


## 5) The Dependency Inversion Principle (DIP)

> ### “Abstractions should not depend on details. Details should depend on abstraction. High-level modules should not depend on low-level modules. Both should depend on abstractions”

Going through this concept, we should depend on a dynamically changing interface or a class rather than static or concrete implemented ones.
Also details should always depend on abstraction and not the opposite.

### **Bad Practice**:

Consider a `Car` class that depends on the *concrete* `Engine` class; therefore, it is not obeying DIP.

```dart
class Car {
    Engine engine;
    
    void start() {
        engine.start();
    }
}

class Engine {
    start() {
        // Some code
    }
}
```
The code will work, for now, but what if we wanted to add another engine type, let’s say a diesel engine? This will require refactoring the `Car` class.

### **Good Practice**:

Using an Engine Interface, we can adjust the Car to be more dynamic and DIP like. 

```dart
class Car {
    EngineInterface engine;

    void start() {
        engine.start();
    }
}

abstract class EngineInterface {
    void start();
}

class PetrolEngine implements EngineInterface {
    @override
    void start() {
        // Some Code
    }
}

class DieselEngine implements EngineInterface {
    @override
    void start() {
        // Some Code
    }
}
```

Now we can connect any type of Engine that implements the `Engine` interface to the `Car` class

## References

- https://www.digitalocean.com/community/conceptual_articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design
- https://www.educative.io/edpresso/what-are-the-solid-principles-in-java
- https://towardsdatascience.com/solid-coding-in-python-1281392a6a94
- https://medium.com/flutterdevs/s-o-l-i-d-principles-in-dart-e6c0c8d1f8f1
- https://www.linkedin.com/pulse/solid-design-principles-python-examples-hiral-amodia/
