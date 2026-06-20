# 🏗️ OOP — Object-Oriented Programming

> OOP is the foundation of most modern software. Master the 4 pillars, SOLID principles, and design patterns.

---

## 📖 The 4 Pillars of OOP

### 1. Encapsulation
**Definition:** Bundling data (attributes) and methods (behavior) together, and restricting direct access to internal state.

**Real-world analogy:** A TV remote. You press buttons without knowing the internal circuit.

```python
class BankAccount:
    def __init__(self, balance):
        self.__balance = balance  # Private attribute (name mangling)
    
    def deposit(self, amount):
        if amount > 0:
            self.__balance += amount
    
    def get_balance(self):
        return self.__balance  # Controlled access via getter

account = BankAccount(1000)
account.deposit(500)
print(account.get_balance())  # 1500
# print(account.__balance)   # AttributeError — encapsulated!
```

### 2. Inheritance
**Definition:** A class (child) inherits properties and behavior from another class (parent).

**Real-world analogy:** A Dog IS an Animal. It inherits eat(), sleep() but also has bark().

```python
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        raise NotImplementedError("Subclasses must implement speak()")
    
    def eat(self):
        return f"{self.name} is eating"

class Dog(Animal):
    def speak(self):
        return f"{self.name} says Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says Meow!"

dog = Dog("Buddy")
print(dog.speak())  # Buddy says Woof!
print(dog.eat())    # Buddy is eating (inherited)
```

**Types of Inheritance:**
- **Single**: Dog → Animal
- **Multi-level**: Puppy → Dog → Animal
- **Multiple**: (Python supports, Java doesn't — uses interfaces instead)
- **Hierarchical**: Dog, Cat both → Animal

### 3. Polymorphism
**Definition:** Same interface, different behavior. One method name works differently for different types.

```python
# Runtime polymorphism (Method Overriding)
def make_sound(animal):
    print(animal.speak())  # Same call, different behavior

make_sound(Dog("Rex"))    # Rex says Woof!
make_sound(Cat("Whiskers")) # Whiskers says Meow!

# Compile-time polymorphism (Method Overloading — Python uses *args)
class Calculator:
    def add(self, *args):
        return sum(args)

calc = Calculator()
print(calc.add(2, 3))     # 5
print(calc.add(1, 2, 3))  # 6
```

### 4. Abstraction
**Definition:** Hiding implementation details, exposing only what's necessary.

```python
from abc import ABC, abstractmethod

class Shape(ABC):
    @abstractmethod
    def area(self):
        pass
    
    @abstractmethod
    def perimeter(self):
        pass
    
    def describe(self):  # Concrete method in abstract class
        return f"Area: {self.area()}, Perimeter: {self.perimeter()}"

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14 * self.radius ** 2
    
    def perimeter(self):
        return 2 * 3.14 * self.radius
```

---

## 🔑 SOLID Principles

| Principle | One-line Definition | Key Benefit |
|-----------|--------------------|-----------  |
| **S** — Single Responsibility | A class should have only one reason to change | Easier to maintain |
| **O** — Open/Closed | Open for extension, closed for modification | Safer to add features |
| **L** — Liskov Substitution | Child classes should be substitutable for parent | Reliable inheritance |
| **I** — Interface Segregation | Don't force classes to implement unused interfaces | Lean interfaces |
| **D** — Dependency Inversion | Depend on abstractions, not concrete classes | Flexible coupling |

### SOLID Example — Single Responsibility
```python
# ❌ Bad: UserManager does too much
class UserManager:
    def create_user(self, user): ...
    def send_email(self, user): ...   # Different responsibility
    def generate_report(self): ...    # Different responsibility

# ✅ Good: Separate classes for each responsibility
class UserManager:
    def create_user(self, user): ...

class EmailService:
    def send_email(self, user): ...

class ReportGenerator:
    def generate_report(self): ...
```

---

## 🔑 Key OOP Concepts

### Constructor and Destructor
```python
class Resource:
    def __init__(self):
        print("Resource acquired")
    
    def __del__(self):
        print("Resource released")
```

### Method Types
```python
class MyClass:
    class_var = "shared"
    
    def instance_method(self):     # Access instance (self) and class
        return self.class_var
    
    @classmethod
    def class_method(cls):          # Access class (cls), not instance
        return cls.class_var
    
    @staticmethod
    def static_method():            # No access to self or cls
        return "Utility function"
```

### `__str__` vs `__repr__`
```python
class Product:
    def __init__(self, name, price):
        self.name = name
        self.price = price
    
    def __str__(self):
        return f"{self.name}: ₹{self.price}"   # Human-readable
    
    def __repr__(self):
        return f"Product('{self.name}', {self.price})"  # Debug-friendly
```

### Duck Typing (Python)
> "If it walks like a duck and quacks like a duck, it IS a duck."
> Python checks behavior, not type.

```python
class Duck:
    def quack(self): return "Quack!"

class Person:
    def quack(self): return "I'm quacking like a duck!"

def make_it_quack(duck):
    print(duck.quack())  # Works for both Duck and Person!
```

---

## 🎨 Design Patterns (Most Asked)

### Singleton Pattern — Only one instance
```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

db1 = Singleton()
db2 = Singleton()
print(db1 is db2)  # True — same instance
```

### Factory Pattern — Create objects without specifying exact class
```python
class Animal:
    def speak(self): pass

class Dog(Animal):
    def speak(self): return "Woof!"

class Cat(Animal):
    def speak(self): return "Meow!"

def animal_factory(animal_type):
    if animal_type == "dog":
        return Dog()
    elif animal_type == "cat":
        return Cat()

pet = animal_factory("dog")
print(pet.speak())  # Woof!
```

### Observer Pattern — Subscribe and notify
```python
class EventSystem:
    def __init__(self):
        self._listeners = []
    
    def subscribe(self, listener):
        self._listeners.append(listener)
    
    def notify(self, event):
        for listener in self._listeners:
            listener.update(event)
```

---

## ❓ Frequently Asked Interview Questions

**Q1: Difference between abstraction and encapsulation?**
> **Encapsulation** is about hiding *data* (using private attributes). **Abstraction** is about hiding *complexity* (using abstract classes/interfaces). Encapsulation is the mechanism; abstraction is the design principle.

**Q2: What is method overloading vs overriding?**
> **Overloading**: Same method name, different parameters (compile-time polymorphism). **Overriding**: Child class redefines parent's method (runtime polymorphism).

**Q3: Can we override static methods?**
> No. Static methods belong to the class, not instances. You can *hide* them by defining a same-named static method in a subclass, but it's not true overriding.

**Q4: What is the difference between Interface and Abstract Class?**

| Feature | Interface | Abstract Class |
|---------|-----------|---------------|
| Methods | All abstract (default in Java 8+) | Can have both |
| Variables | `public static final` only | Any |
| Constructor | No | Yes |
| Inheritance | Multiple (implements) | Single |
| Use case | Contract, capability | Partial implementation |

**Q5: What is composition vs inheritance?**
> **Inheritance**: IS-A relationship (Dog IS-A Animal). **Composition**: HAS-A relationship (Car HAS-A Engine). Prefer composition over inheritance for flexibility ("favor composition over inheritance" — GoF).

---

## ✅ Revision Checklist

- [ ] Can I explain all 4 pillars with code examples?
- [ ] Can I explain SOLID principles with real examples?
- [ ] Can I implement Singleton, Factory, Observer patterns?
- [ ] Do I understand the difference between abstract class and interface?
- [ ] Can I explain method overloading vs overriding?
- [ ] Do I understand duck typing in Python?
- [ ] Can I explain composition vs inheritance with when to use each?
