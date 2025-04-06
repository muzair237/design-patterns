# Software Design Patterns

## What Are Software Design Patterns?

Design patterns are **proven, reusable solutions** to common software design problems. They provide a structured approach to solving problems in a **scalable, maintainable, and efficient way**.

Instead of focusing on "why" and "what" makes the design **good** or **bad**, design patterns focus on "how" something should be coded.

## Why Use Design Patterns?

- **Standardized Solutions** → Avoid reinventing the wheel.
- **Maintainability** → Code becomes easier to modify and extend.
- **Scalability** → Helps design systems that grow over time.
- **Readability & Collaboration:** → Common patterns make it easier for developers to understand each other's code.

---

## Gang of Four (GoF) Design Patterns

The **Gang of Four (GoF)** refers to four authors—**Erich Gamma, Richard Helm, Ralph Johnson, and John Vlissides**—who wrote the famous book ***"Design Patterns: Elements of Reusable Object-Oriented Software"*** in 1994.

They introduced **23 foundational design patterns**, categorized into three groups:

### 1️⃣ Creational Patterns (Object Creation & Initialization)

**Definition:**  
Creational patterns deal with **how objects are created** while hiding the complexities of instantiation. They ensure that the system remains **flexible and reusable** by providing controlled and optimized object creation mechanisms.

### 2️⃣ Structural Patterns (Class & Object Composition)

**Definition:**  
Structural patterns define **how objects and classes are composed** to form larger structures while ensuring flexibility and efficiency. They help create relationships between components that are **scalable and easy to maintain**.

### 3️⃣ Behavioral Patterns (Communication Between Objects)

**Definition:**  
Behavioral patterns focus on **how objects interact and communicate**, defining the responsibilities of different components. They help manage **complex workflows and business logic** efficiently.

---

#### Now, we begin our journey through the 23 Gang of Four (GoF) Design Patterns, starting with Creational Patterns.

### 1. Singleton

**Definition:**  
The Singleton pattern ensures that a class **has only one instance** and provides a **global access point** to that instance.

**Intent:** 
- Restrict the instantiation of a class to **one** object.
- Provide a **global access point** to that single instance.
- Ensure **controlled access** to shared resources (e.g., configuration settings, logging, caching).

**Structure:** 
- **Private Constructor** → Prevents direct instantiation using ```new```.
- **Static Instance Property** → Stores the single instance.
- **Public Static Method** → Provides global access to the instance.

**Example:** 
In real-world applications, configurations (like API keys, database connections, environment variables) should be loaded once and shared across the entire app.

```ts
class ConfigManager {
  private static instance: ConfigManager;
  private config: Record<string, any>;

  // Private constructor to prevent external instantiation
  private constructor() {
    this.config = {
      apiUrl: "https://api.example.com",
      maxConnections: 10,
      timeout: 5000,
    };
  }

  // Public method to get the single instance
  public static getInstance(): ConfigManager {
    if (!ConfigManager.instance) {
      ConfigManager.instance = new ConfigManager();
    }
    return ConfigManager.instance;
  }

  // Method to get a config value
  public get(key: string): any {
    return this.config[key];
  }

  // Method to set/update a config value
  public set(key: string, value: any): void {
    this.config[key] = value;
  }
}

// Usage:
const config1 = ConfigManager.getInstance();
console.log(config1.get("apiUrl")); // Output: https://api.example.com

const config2 = ConfigManager.getInstance();
config2.set("apiUrl", "https://new-api.example.com");

console.log(config1.get("apiUrl")); // Output: https://new-api.example.com (same instance)
console.log(config1 === config2); // Output: true (same instance)
```

**Explanation:** 
- The **constructor is private**, so no new instances can be created from outside.
- The **static** ```getInstance()``` **method** ensures only one instance is created.
- The **same instance is used** across the application, maintaining **consistent configurations.**
- When one part of the system updates the configuration, all other parts see the same update.

**When to Use:** 
- When a **single instance** should control a **shared resource** (e.g., logging, configuration, database connection).
- When you **need global access** to a component but must enforce a single entry point.
- When **state consistency** is crucial (e.g., managing user sessions, caching).

---

### 2. Factory Method

**Definition:**  
The Factory Method Pattern defines an interface for **creating objects**, but lets subclasses or implementing classes **decide which class to instantiate**. It allows the instantiation logic to be **delegated and centralized**, promoting flexibility and loose coupling.



**Intent:** 
- Separate the **object creation process** from the business logic.
- Allow a class to delegate the instantiation of objects to its subclasses or other methods.
- Make your code **open for extension but closed for modification** (Open/Closed Principle).

**Structure:** 
- A **common interface or abstract class** defines a method for creating objects.
- **Concrete classes** implement this method and **return instances** of different subclasses.
- Client code depends on the interface and lets the factory decide which concrete class to instantiate, promoting loose coupling.

**Example:** 
In real-world applications, different channels (Email, SMS, Push) have different implementations, but the app only wants to deal with a generic Notification interface.

```ts
// Common interface
interface Notification {
  send(message: string): void;
}

// Concrete implementations
class EmailNotification implements Notification {
  send(message: string): void {
    console.log(`Sending EMAIL: ${message}`);
  }
}

class SMSNotification implements Notification {
  send(message: string): void {
    console.log(`Sending SMS: ${message}`);
  }
}

class PushNotification implements Notification {
  send(message: string): void {
    console.log(`Sending PUSH Notification: ${message}`);
  }
}

// Factory class
class NotificationFactory {
  static createNotification(channel: 'email' | 'sms' | 'push'): Notification {
    switch (channel) {
      case 'email':
        return new EmailNotification();
      case 'sms':
        return new SMSNotification();
      case 'push':
        return new PushNotification();
      default:
        throw new Error("Unsupported notification channel");
    }
  }
}

// Usage
const notification1 = NotificationFactory.createNotification('email');
notification1.send("Welcome to our platform!");  // Output: Sending EMAIL: Welcome to our platform!

const notification2 = NotificationFactory.createNotification('sms');
notification2.send("Your order has been shipped."); // Output: Sending SMS: Your order has been shipped.
```

**Explanation:** 
- The client doesn't know or care which type of notification is created.
- It only interacts with the ```Notification``` interface.
- The **Factory handles the decision-making logic** about which concrete class to instantiate.
- You can easily add new types (like WhatsApp or Telegram) without modifying the client logic — only the factory.

**When to Use:** 
- When the exact type of object **isn’t known until runtime**.
- When you want to **delegate creation logic** to a central factory.
- When your system needs to **support future extensions** with minimal changes.
- When you want to **simplify client code** and decouple it from concrete implementations.

---

### 3. Abstract Fatory

**Definition:**  
The Abstract Factory Pattern provides an interface for creating **families of related or dependent objects** without specifying their concrete classes.



**Intent:** 
- Group multiple **related object factories** under one abstraction.
- Ensure that related components are **compatible with each other**.
- Make it easy to **switch entire sets of products** without altering client code.

**Structure:** 
- **Abstract Factory Interface** declares methods for creating each kind of object.
- **Concrete Factories** implement the interface and produce families of related objects.
- **Client** uses only the abstract interfaces, unaware of the concrete classes.

**Example:** 
In real-world applications, a web app renders UI elements like buttons and modals differently depending on the selected theme.

```ts
// Abstract product interfaces
interface Button {
  render(): void;
}

interface Modal {
  open(): void;
}

// Concrete products for Light Theme
class LightButton implements Button {
  render() {
    console.log("Rendering light-themed button");
  }
}

class LightModal implements Modal {
  open() {
    console.log("Opening light-themed modal");
  }
}

// Concrete products for Dark Theme
class DarkButton implements Button {
  render() {
    console.log("Rendering dark-themed button");
  }
}

class DarkModal implements Modal {
  open() {
    console.log("Opening dark-themed modal");
  }
}

// Abstract factory
interface ThemeFactory {
  createButton(): Button;
  createModal(): Modal;
}

// Concrete factories
class LightThemeFactory implements ThemeFactory {
  createButton(): Button {
    return new LightButton();
  }
  createModal(): Modal {
    return new LightModal();
  }
}

class DarkThemeFactory implements ThemeFactory {
  createButton(): Button {
    return new DarkButton();
  }
  createModal(): Modal {
    return new DarkModal();
  }
}

// Usage
function renderUI(factory: ThemeFactory) {
  const button = factory.createButton();
  const modal = factory.createModal();
  button.render();
  modal.open();
}

const theme = 'dark';
const factory = theme === 'dark' ? new DarkThemeFactory() : new LightThemeFactory();
renderUI(factory);

// Output:
// Rendering dark-themed button
// Opening dark-themed modal
```

**Explanation:** 
- The client uses only the ```ThemeFactory``` interface.
- Each theme factory creates ```consistent and matching UI components```.
- Adding a new theme (e.g., ```HighContrastThemeFactory```) is easy and doesn’t break existing code.

**When to Use:** 
- When you need to create **multiple related objects** consistently.
- When product variations (like themes, platforms, configs) are **mutually exclusive**.
- When adding a new family of products should be easy and not require modifying existing client logic.

---

