# SOLID Principles — Practical Guide

> A practical guide to understanding, applying, and revising the **SOLID Principles** using Python, real-world examples, before/after designs, runnable code, and outputs.

---

## 📌 What is SOLID?

**SOLID** is a set of five object-oriented design principles that help us write software that is:

* Easy to understand
* Easy to maintain
* Easy to extend
* Less tightly coupled
* Easier to test
* Easier to modify without breaking existing code

### The 5 SOLID Principles

| Letter | Principle                       | Core Idea                                    |
| ------ | ------------------------------- | -------------------------------------------- |
| **S**  | Single Responsibility Principle | One class → one responsibility               |
| **O**  | Open/Closed Principle           | Open for extension, closed for modification  |
| **L**  | Liskov Substitution Principle   | Child should safely replace parent           |
| **I**  | Interface Segregation Principle | Don't force unused methods                   |
| **D**  | Dependency Inversion Principle  | Depend on abstractions, not concrete classes |

---

# 🟢 S — Single Responsibility Principle (SRP)

## Definition

> **A class should have one responsibility and one reason to change.**

The important phrase is:

### "One reason to change"

SRP does **not** mean:

> A class can have only one method.

A class can have multiple methods as long as they belong to the **same responsibility**.

---

## 🔗 Related Concepts

* Responsibility
* Reason to change
* Cohesion
* Separation of concerns
* Maintainability
* Modularity

### Cohesion

**High cohesion:**

Methods inside a class are closely related.

**Low cohesion:**

Methods inside a class perform unrelated tasks.

---

## 🌍 Real-World Example

Think about an **Employee**.

An employee's salary calculation, database storage, report generation, and email sending are different responsibilities.

### ❌ Bad Design

```text
Employee
├── Calculate salary
├── Calculate tax
├── Save to database
├── Generate report
└── Send email
```

The class has multiple reasons to change.

---

## ❌ Before SRP

```python
class Employee:

    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def calculate_salary(self):
        return self.salary

    def calculate_tax(self):
        return self.salary * 0.10

    def save_to_database(self):
        print("Saving employee to database")

    def generate_report(self):
        print("Generating employee report")

    def send_email(self):
        print("Sending employee email")


employee = Employee("Kalyan", 50000)

print("Salary:", employee.calculate_salary())
print("Tax:", employee.calculate_tax())

employee.save_to_database()
employee.generate_report()
employee.send_email()
```

### Output

```text
Salary: 50000
Tax: 5000.0
Saving employee to database
Generating employee report
Sending employee email
```

### Problem

The code works, but `Employee` has several unrelated responsibilities.

If:

* Tax rules change → `Employee` changes
* Database changes → `Employee` changes
* Report format changes → `Employee` changes
* Email system changes → `Employee` changes

---

## ✅ After SRP

Separate the responsibilities.

```python
class Employee:

    def __init__(self, name, salary):
        self.name = name
        self.salary = salary

    def calculate_salary(self):
        return self.salary

    def calculate_tax(self):
        return self.salary * 0.10


class EmployeeRepository:

    def save(self, employee):
        print(f"Saving {employee.name} to database")


class EmployeeReport:

    def generate(self, employee):
        print(f"Generating report for {employee.name}")


class EmailService:

    def send(self, employee):
        print(f"Sending email to {employee.name}")


employee = Employee("Kalyan", 50000)

print("Salary:", employee.calculate_salary())
print("Tax:", employee.calculate_tax())

repository = EmployeeRepository()
repository.save(employee)

report = EmployeeReport()
report.generate(employee)

email = EmailService()
email.send(employee)
```

### Output

```text
Salary: 50000
Tax: 5000.0
Saving Kalyan to database
Generating report for Kalyan
Sending email to Kalyan
```

### Design

```text
Employee
    ↓
Employee/pay logic

EmployeeRepository
    ↓
Database logic

EmployeeReport
    ↓
Report logic

EmailService
    ↓
Email logic
```

Each class has a focused responsibility.

---

## 🧠 SRP Quick Recap

```text
SRP
 ↓
One responsibility
 ↓
One reason to change
 ↓
High cohesion
 ↓
Separate unrelated responsibilities
```

### Ask yourself:

> "If two completely different things change, do I need to modify the same class?"

If yes → **check for SRP violation.**

---

# 🟡 O — Open/Closed Principle (OCP)

## Definition

> **Software entities should be open for extension but closed for modification.**

Simple meaning:

> **We should be able to add new behavior without repeatedly modifying stable existing code.**

---

## 🔗 Related Concepts

* Extension
* Abstraction
* Polymorphism
* Inheritance
* Composition
* Strategy Pattern
* Interfaces
* Dependency Injection

---

## 🌍 Real-World Example

Imagine an e-commerce website with different discounts:

```text
Regular → 5%
Premium → 10%
VIP → 20%
Student → 15%
Senior Citizen → 12%
```

A bad design keeps adding more conditions.

---

## ❌ Before OCP

```python
class DiscountCalculator:

    def calculate_discount(self, customer_type, amount):

        if customer_type == "regular":
            return amount * 0.05

        elif customer_type == "premium":
            return amount * 0.10

        elif customer_type == "vip":
            return amount * 0.20

        elif customer_type == "student":
            return amount * 0.15

        elif customer_type == "senior":
            return amount * 0.12


calculator = DiscountCalculator()

print("Regular:", calculator.calculate_discount("regular", 1000))
print("Premium:", calculator.calculate_discount("premium", 1000))
print("VIP:", calculator.calculate_discount("vip", 1000))
print("Student:", calculator.calculate_discount("student", 1000))
```

### Output

```text
Regular: 50.0
Premium: 100.0
VIP: 200.0
Student: 150.0
```

Now suppose we add:

```text
SeniorCitizen
```

We must modify the existing class:

```python
elif customer_type == "senior":
    return amount * 0.12
```

Then another type:

```text
Employee
```

We modify it again.

### Problem

```text
New behavior
     ↓
Modify existing class
     ↓
More if/elif
     ↓
Class grows
     ↓
More chances of bugs
```

---

## ✅ After OCP

Create an abstraction:

```python
from abc import ABC, abstractmethod


class Discount(ABC):

    @abstractmethod
    def calculate(self, amount):
        pass
```

Create individual implementations:

```python
class RegularDiscount(Discount):

    def calculate(self, amount):
        return amount * 0.05


class PremiumDiscount(Discount):

    def calculate(self, amount):
        return amount * 0.10


class VIPDiscount(Discount):

    def calculate(self, amount):
        return amount * 0.20


class StudentDiscount(Discount):

    def calculate(self, amount):
        return amount * 0.15


class SeniorCitizenDiscount(Discount):

    def calculate(self, amount):
        return amount * 0.12
```

Use them:

```python
discounts = [
    RegularDiscount(),
    PremiumDiscount(),
    VIPDiscount(),
    StudentDiscount(),
    SeniorCitizenDiscount()
]

for discount in discounts:
    print(discount.calculate(1000))
```

### Output

```text
50.0
100.0
200.0
150.0
120.0
```

Now if we want to add an employee discount:

```python
class EmployeeDiscount(Discount):

    def calculate(self, amount):
        return amount * 0.18
```

We **add a new class**.

We don't modify:

```text
RegularDiscount
PremiumDiscount
VIPDiscount
StudentDiscount
SeniorCitizenDiscount
```

### Design

```text
             Discount
                ↑
       ┌────────┼────────┐
       ↓        ↓        ↓
   Regular   Premium     VIP
       ↓        ↓        ↓
   Student   Senior    Employee
```

---

## 🧠 OCP Quick Recap

```text
OCP
 ↓
Open for extension
 ↓
Closed for modification
 ↓
New behavior → add new code
 ↓
Avoid repeatedly modifying stable code
```

### Ask yourself:

> "Whenever I add a new type, am I repeatedly modifying the same class?"

If yes → **check for OCP violation.**

---

# 🔵 L — Liskov Substitution Principle (LSP)

## Definition

> **Objects of a child class should be usable wherever objects of the parent class are expected without breaking the program's correctness.**

Simple meaning:

> **A child should safely substitute its parent.**

---

## 🔗 Related Concepts

* Inheritance
* Subtyping
* Polymorphism
* Behavioral contract
* Abstraction
* Substitutability

### Important

LSP is about **behavior**, not just inheritance.

A class can technically inherit from another class but still violate LSP.

---

## 🌍 Real-World Example

Consider:

```text
Bird
├── Sparrow
└── Penguin
```

A common mistake is:

```text
Bird
└── fly()
```

But Penguin cannot fly.

---

## ❌ Before LSP

```python
class Bird:

    def fly(self):
        print("Bird is flying")


class Sparrow(Bird):

    def fly(self):
        print("Sparrow is flying")


class Penguin(Bird):

    def fly(self):
        raise Exception("Penguin cannot fly")


def make_bird_fly(bird):
    bird.fly()


make_bird_fly(Sparrow())

try:
    make_bird_fly(Penguin())
except Exception as e:
    print("Error:", e)
```

### Output

```text
Sparrow is flying
Error: Penguin cannot fly
```

### Why is this an LSP violation?

The parent abstraction promises:

```text
Bird
 ↓
fly()
```

But the child cannot fulfill that behavior.

Therefore:

> Penguin cannot safely substitute for Bird under this abstraction.

---

## ✅ After LSP

Separate common behavior from flying behavior.

```python
class Bird:

    def eat(self):
        print("Bird is eating")


class FlyingBird(Bird):

    def fly(self):
        print("Bird is flying")


class Sparrow(FlyingBird):

    def fly(self):
        print("Sparrow is flying")


class Penguin(Bird):

    def swim(self):
        print("Penguin is swimming")


def make_bird_fly(bird):
    bird.fly()


sparrow = Sparrow()
penguin = Penguin()

make_bird_fly(sparrow)

penguin.swim()
```

### Output

```text
Sparrow is flying
Penguin is swimming
```

Now `Penguin` is not expected to provide `fly()`.

### Design

```text
             Bird
            /    \
           /      \
      Sparrow    Penguin
         ↓          ↓
       fly()      swim()
```

---

## 🧠 LSP Quick Recap

```text
LSP
 ↓
Parent defines behavioral contract
 ↓
Child must honor that contract
 ↓
Child should safely replace parent
 ↓
No unexpected behavior
```

### Ask yourself:

> "If I replace the parent object with this child object, will the program still behave correctly?"

If no → **check for LSP violation.**

---

# 🟣 I — Interface Segregation Principle (ISP)

## Definition

> **Clients should not be forced to depend on methods they do not use.**

Simple meaning:

> **Don't force a class to implement unnecessary methods.**

---

## 🔗 Related Concepts

* Interfaces
* Abstraction
* Separation of concerns
* Small interfaces
* Cohesion
* Dependency
* Composition

---

## 🌍 Real-World Example

Imagine an office machine:

```text
Machine
├── Print
├── Scan
└── Fax
```

A multifunction printer can do all three.

But a simple printer may only print.

---

## ❌ Before ISP

```python
class Machine:

    def print_document(self):
        print("Printing")

    def scan_document(self):
        print("Scanning")

    def fax_document(self):
        print("Faxing")


class SimplePrinter(Machine):

    def print_document(self):
        print("Printing")

    def scan_document(self):
        raise NotImplementedError("Simple printer cannot scan")

    def fax_document(self):
        raise NotImplementedError("Simple printer cannot fax")


printer = SimplePrinter()

printer.print_document()

try:
    printer.scan_document()
except NotImplementedError as e:
    print("Error:", e)
```

### Output

```text
Printing
Error: Simple printer cannot scan
```

### Problem

`SimplePrinter` is forced to implement:

```text
scan_document()
fax_document()
```

even though it doesn't support them.

---

## ✅ After ISP

Split the large interface into smaller interfaces.

```python
from abc import ABC, abstractmethod


class Printer(ABC):

    @abstractmethod
    def print_document(self):
        pass


class Scanner(ABC):

    @abstractmethod
    def scan_document(self):
        pass


class Fax(ABC):

    @abstractmethod
    def fax_document(self):
        pass
```

### Simple Printer

```python
class SimplePrinter(Printer):

    def print_document(self):
        print("Printing")
```

### Multi-Function Printer

```python
class MultiFunctionPrinter(Printer, Scanner, Fax):

    def print_document(self):
        print("Printing")

    def scan_document(self):
        print("Scanning")

    def fax_document(self):
        print("Faxing")
```

Run:

```python
simple_printer = SimplePrinter()
simple_printer.print_document()

multi_printer = MultiFunctionPrinter()

multi_printer.print_document()
multi_printer.scan_document()
multi_printer.fax_document()
```

### Output

```text
Printing
Printing
Scanning
Faxing
```

### Design

```text
Printer ───────→ print()

Scanner ───────→ scan()

Fax ───────────→ fax()

SimplePrinter
      ↓
   Printer

MultiFunctionPrinter
      ↓
Printer + Scanner + Fax
```

---

## 🧠 ISP Quick Recap

```text
ISP
 ↓
Avoid large interfaces
 ↓
Split into smaller interfaces
 ↓
Classes implement only what they need
```

### Ask yourself:

> "Is this class being forced to implement methods that it doesn't need?"

If yes → **check for ISP violation.**

---

# 🔴 D — Dependency Inversion Principle (DIP)

## Definition

> **High-level modules should not depend on low-level modules. Both should depend on abstractions.**

And:

> **Abstractions should not depend on details. Details should depend on abstractions.**

Simple meaning:

> **Depend on abstractions, not concrete implementations.**

---

## 🔗 Related Concepts

* Abstraction
* Coupling
* Loose coupling
* Dependency Injection
* Constructor Injection
* Interfaces
* High-level modules
* Low-level modules
* Inversion of Control

---

## 🌍 Real-World Example

Imagine an application that stores users.

Initially:

```text
UserService
    ↓
MySQLDatabase
```

Later, the company wants MongoDB.

If `UserService` directly creates MySQL, we must modify `UserService`.

---

## ❌ Before DIP

```python
class MySQLDatabase:

    def save(self, data):
        print(f"Saving {data} to MySQL")


class UserService:

    def __init__(self):
        self.database = MySQLDatabase()

    def register_user(self, user):
        self.database.save(user)


service = UserService()

service.register_user("Kalyan")
```

### Output

```text
Saving Kalyan to MySQL
```

The problem is:

```python
self.database = MySQLDatabase()
```

`UserService` is directly coupled to a concrete implementation.

---

## Problem

```text
UserService
     ↓
MySQLDatabase
```

If we change:

```text
MySQL → MongoDB
```

we have to modify `UserService`.

That's tight coupling.

---

## ✅ After DIP

Create an abstraction:

```python
from abc import ABC, abstractmethod


class Database(ABC):

    @abstractmethod
    def save(self, data):
        pass
```

### MySQL

```python
class MySQLDatabase(Database):

    def save(self, data):
        print(f"Saving {data} to MySQL")
```

### MongoDB

```python
class MongoDBDatabase(Database):

    def save(self, data):
        print(f"Saving {data} to MongoDB")
```

### UserService

```python
class UserService:

    def __init__(self, database):
        self.database = database

    def register_user(self, user):
        self.database.save(user)
```

### Inject MySQL

```python
mysql = MySQLDatabase()

user_service = UserService(mysql)

user_service.register_user("Kalyan")
```

### Output

```text
Saving Kalyan to MySQL
```

### Switch to MongoDB

```python
mongodb = MongoDBDatabase()

user_service = UserService(mongodb)

user_service.register_user("Kalyan")
```

### Output

```text
Saving Kalyan to MongoDB
```

Notice:

> `UserService` itself did not change.

---

## Dependency Injection

The dependency is created outside:

```python
mysql = MySQLDatabase()

user_service = UserService(mysql)
```

The dependency is injected here:

```python
UserService(mysql)
```

And received here:

```python
def __init__(self, database):
```

This is called:

### Constructor Injection

---

## Dependency Injection vs DIP

These are related but not identical.

### DIP

A **design principle**:

```text
High-level code
      ↓
Abstraction
      ↑
Low-level implementation
```

### Dependency Injection

A **technique** for providing dependencies from outside:

```python
UserService(database)
```

Therefore:

```text
DIP = Principle
DI  = Technique
```

---

## 🧠 DIP Quick Recap

```text
DIP
 ↓
Avoid high-level → concrete dependency
 ↓
Create abstraction
 ↓
Concrete classes implement abstraction
 ↓
Inject dependency from outside
 ↓
Loose coupling
```

### Ask yourself:

> "Does my high-level class directly create/use a specific low-level implementation?"

If yes → **check for DIP violation.**

---

# 🏆 SOLID — One-Page Revision

## S — Single Responsibility

> **One class → one responsibility → one reason to change.**

```text
❌ Employee
├── Salary
├── Database
├── Report
└── Email

✅ Separate responsibilities
```

---

## O — Open/Closed

> **Open for extension, closed for modification.**

```text
❌ New type → modify if/elif

✅ New type → add new implementation
```

---

## L — Liskov Substitution

> **Child should safely replace parent.**

```text
Parent
  ↑
Child

If replacing Parent with Child
breaks expected behavior → LSP violation
```

---

## I — Interface Segregation

> **Don't force classes to depend on methods they don't use.**

```text
❌ Large interface

Machine
├── print
├── scan
└── fax

SimplePrinter
├── print ✅
├── scan  ❌
└── fax   ❌

✅ Small interfaces

Printer
Scanner
Fax
```

---

## D — Dependency Inversion

> **Depend on abstractions, not concrete implementations.**

```text
❌
UserService → MySQL

✅
UserService → Database ← MySQL
                         ← MongoDB
```

---

# 🧠 How to Identify SOLID Violations

When you see code, ask these five questions:

```text
1️⃣ SRP
Does this class have multiple unrelated responsibilities?

2️⃣ OCP
Do I have to modify existing code every time I add a new variation?

3️⃣ LSP
Can this child safely replace its parent?

4️⃣ ISP
Is this class forced to implement methods it doesn't need?

5️⃣ DIP
Does high-level code directly depend on concrete low-level implementations?
```

---

# 🔥 SOLID in One Real Application

Imagine an e-commerce application.

## ❌ Without SOLID

```text
                    E-Commerce App
                          │
             ┌────────────┼────────────┐
             ↓            ↓            ↓
          Payment      Database       Email
             │            │            │
           Stripe        MySQL        SMTP
```

Everything becomes tightly connected.

---

## ✅ With SOLID

```text
                    E-Commerce App
                          │
       ┌──────────────────┼──────────────────┐
       ↓                  ↓                  ↓
   Payment             Database          Notification
   Interface           Interface           Interface
       ↑                  ↑                  ↑
   ┌───┴───┐          ┌───┴───┐         ┌──┴───┐
   ↓       ↓          ↓       ↓         ↓      ↓
 Card     UPI        MySQL   Mongo      Email   SMS
```

Responsibilities are separated, abstractions are used, and new implementations can be added more safely.

---

# 🎯 SOLID Decision Guide

When you see a problem, think:

```text
                  CODE PROBLEM
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
   Too many         Adding new      Child breaks
   responsibilities  types          parent behavior
        │              │              │
        ↓              ↓              ↓
       SRP            OCP            LSP


        ┌──────────────┴──────────────┐
        ↓                             ↓
   Unused methods              Tight coupling
        │                             │
        ↓                             ↓
       ISP                           DIP
```

---

# 🚀 Final SOLID Mental Model

Remember this:

```text
S → Separate responsibilities
O → Extend without unnecessary modification
L → Substitute safely
I → Keep interfaces small
D → Depend on abstractions
```

Or simply:

> **S — Separate**
> **O — Extend**
> **L — Substitute**
> **I — Segregate**
> **D — Abstract**

---

# 📚 Learning Order

For mastering SOLID, follow this order:

```text
1. OOP Fundamentals
       ↓
2. Classes & Objects
       ↓
3. Encapsulation
       ↓
4. Inheritance
       ↓
5. Polymorphism
       ↓
6. Abstraction
       ↓
7. SOLID
       ↓
8. Design Patterns
       ↓
9. Clean Architecture
       ↓
10. Real-world System Design
```

SOLID is not about memorizing five definitions.

The real goal is:

> **Look at a piece of code → recognize the design problem → identify the SOLID principle → redesign the code.**

---

# ⭐ Quick Revision Card

```text
┌─────────────────────────────────────────────┐
│                   SOLID                     │
├─────────────────────────────────────────────┤
│ S → One responsibility / reason to change   │
│                                             │
│ O → Extend, don't repeatedly modify        │
│                                             │
│ L → Child safely replaces parent            │
│                                             │
│ I → Don't force unused methods              │
│                                             │
│ D → Depend on abstractions                  │
└─────────────────────────────────────────────┘
```

## The 5 Questions to Remember

```text
S → "Does this class do too much?"

O → "Do I modify existing code for every new type?"

L → "Can this child safely replace its parent?"

I → "Am I forcing unused methods?"

D → "Am I depending directly on a concrete implementation?"
```

---

# 🏁 Final Takeaway

SOLID is not about memorizing definitions.

The real skill is:

```text
Read Code
   ↓
Find the Design Problem
   ↓
Identify the SOLID Principle
   ↓
Understand Why
   ↓
Redesign
   ↓
Test the New Design
```

> **If you can look at unfamiliar code and identify which design principle is being violated and how to redesign it, you are applying SOLID rather than simply memorizing it.**
