# 🧩 Applying SOLID Principles to an Order Printing System in JavaScript

In real-world applications, it's common to start with a simple class and enhance it over time. But as features like printing to PDF, paper, or even emailing and faxing get added, the class grows complex and hard to manage.

This is where the **SOLID principles** help. They offer five key design principles to make your code modular, maintainable, and extensible.

---

## 🛠️ The Scenario

We have an `Order` class that stores order details and prints them in different formats. Initially, it might look like this:

```js
class Order {
  constructor(orderDetails) {
    this.orderDetails = orderDetails;
  }

  print(format) {
    if (format === 'pdf') {
      console.log("Printing to PDF:", this.orderDetails);
    } else if (format === 'paper') {
      console.log("Printing to Paper:", this.orderDetails);
    } else {
      throw new Error("Unsupported format");
    }
  }
}
```

This works temporarily but violates several SOLID principles.

---

## ✅ 1. Single Responsibility Principle (SRP)

**A class should have only one reason to change.**

**Problem:**  
The `Order` class handles both order data and printing logic.

**Solution:**  
Split it into separate classes.

```js
class Order {
  constructor(orderDetails) {
    this.orderDetails = orderDetails;
  }
}

class OrderPrinter {
  printToPDF(order) {
    console.log("Printing to PDF:", order.orderDetails);
  }

  printToPaper(order) {
    console.log("Printing to Paper:", order.orderDetails);
  }
}
```

---

## ✅ 2. Open/Closed Principle (OCP)

**Software entities should be open for extension but closed for modification.**

**Problem:**  
Adding a new format like Excel requires modifying the `OrderPrinter`.

**Solution:**  
Use polymorphism and abstraction.

```js
class IPrinter {
  print(order) {
    throw new Error("Must override print()");
  }
}

class PDFPrinter extends IPrinter {
  print(order) {
    console.log("PDF generated:", order.orderDetails);
  }
}

class PaperPrinter extends IPrinter {
  print(order) {
    console.log("Printed on paper:", order.orderDetails);
  }
}
```

---

## ✅ 3. Liskov Substitution Principle (LSP)

**Subtypes must be substitutable for their base types.**

Each printer subclass follows the same interface, so we can substitute one for another:

```js
function processPrint(order, printer) {
  printer.print(order);
}

const order = new Order({ id: 1, item: "Mobile Phone" });
const printer = new PDFPrinter();

processPrint(order, printer); // PDF generated: { id: 1, item: 'Mobile Phone' }
```

---

## ✅ 4. Interface Segregation Principle (ISP)

**Clients should not be forced to depend on methods they do not use.**

**Problem:**  
Imagine a class like this:

```js
class MultiFunctionPrinter {
  printPDF() {}
  printPaper() {}
  fax() {}
  email() {}
}
```

Not all clients need all these methods.

**Solution:**  
Split into smaller interfaces.

```js
class IPrinter {
  print(order) {}
}

class IFax {
  fax(order) {}
}
```

Now clients only depend on what they need.

---

## ✅ 5. Dependency Inversion Principle (DIP)

**High-level modules should not depend on low-level modules. Both should depend on abstractions.**

**Problem:**  
OrderManager might directly depend on PDFPrinter.

**Solution:**  
Depend on abstraction:

```js
class OrderManager {
  constructor(order, printer) {
    this.order = order;
    this.printer = printer;
  }

  printReceipt() {
    this.printer.print(this.order);
  }
}

const order = new Order({ id: 123, item: "Book" });
const printer = new PaperPrinter();
const manager = new OrderManager(order, printer);

manager.printReceipt(); // Printed on paper: { id: 123, item: 'Book' }
```

---

## 🧾 Final Structure Summary

```plaintext
Order            => Stores order data (SRP)
IPrinter         => Abstraction for printing (OCP, DIP)
PDFPrinter       => Prints to PDF (LSP)
PaperPrinter     => Prints to paper (LSP)
OrderManager     => Uses IPrinter to perform business logic (DIP)
```

---

## ✅ Complete Working Example

```js
// Order class with only order-related data (SRP)
class Order {
  constructor(orderDetails) {
    this.orderDetails = orderDetails;
  }
}

// Abstract Printer Interface (OCP, DIP)
class IPrinter {
  print(order) {
    throw new Error("Must override print()");
  }
}

// Concrete printer implementations (LSP)
class PDFPrinter extends IPrinter {
  print(order) {
    console.log("PDF generated:", order.orderDetails);
  }
}

class PaperPrinter extends IPrinter {
  print(order) {
    console.log("Printed on paper:", order.orderDetails);
  }
}

// High-level class depends on abstraction (DIP)
class OrderManager {
  constructor(order, printer) {
    this.order = order;
    this.printer = printer;
  }

  printReceipt() {
    this.printer.print(this.order);
  }
}

// Example usage
const order = new Order({ id: 101, item: "Laptop" });
const printer = new PDFPrinter(); // or new PaperPrinter()

const manager = new OrderManager(order, printer);
manager.printReceipt(); // Output: PDF generated: { id: 101, item: 'Laptop' }
```

---

## 🧠 Conclusion

By applying the **SOLID principles**, we transformed a rigid and tightly-coupled system into a **modular**, **testable**, and **extensible** design. Although JavaScript doesn't enforce interfaces like statically-typed languages do, we can still use these principles to write clean and robust architecture.

> **Clean architecture begins with clean thinking. Keep your classes focused, abstract your dependencies, and let behaviors grow — not bugs.**
