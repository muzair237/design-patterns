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

The **Gang of Four (GoF)** refers to four authors—**Erich Gamma, Richard Helm, Ralph Johnson, and John Vlissides**—who wrote the famous book **_"Design Patterns: Elements of Reusable Object-Oriented Software"_** in 1994.

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

### 1. Singleton Pattern

**Definition:**  
The Singleton pattern ensures that a class **has only one instance** and provides a **global access point** to that instance.

**Intent:**

- Restrict the instantiation of a class to **one** object.
- Provide a **global access point** to that single instance.
- Ensure **controlled access** to shared resources (e.g., configuration settings, logging, caching).

**Structure:**

- **Private Constructor** → Prevents direct instantiation using `new`.
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
- The **static** `getInstance()` **method** ensures only one instance is created.
- The **same instance is used** across the application, maintaining **consistent configurations.**
- When one part of the system updates the configuration, all other parts see the same update.

**When to Use:**

- When a **single instance** should control a **shared resource** (e.g., logging, configuration, database connection).
- When you **need global access** to a component but must enforce a single entry point.
- When **state consistency** is crucial (e.g., managing user sessions, caching).

---

### 2. Factory Method Pattern

**Definition:**  
The Factory Method Pattern defines an **interface** for creating an object, but lets subclasses alter the type of objects that will be created. It allows a class to delegate the responsibility of instantiating its objects to subclasses, promoting **loose coupling** and enabling the addition of new types of objects **without modifying existing code**.

## **Intent:**

- Separate the **object creation process** from the business logic.
- Allow a class to delegate the instantiation of objects to its subclasses or other methods.
- Make your code **open for extension but closed for modification** (Open/Closed Principle).

## **Structure:**

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

// Creator (abstract)
abstract class NotificationFactory {
  abstract createNotification(): Notification;

  notify(message: string): void {
    const notification = this.createNotification();
    notification.send(message);
  }
}

// Concrete factories
class EmailNotificationFactory extends NotificationFactory {
  createNotification(): Notification {
    return new EmailNotification();
  }
}

class SMSNotificationFactory extends NotificationFactory {
  createNotification(): Notification {
    return new SMSNotification();
  }
}

// Client usage
const factory: NotificationFactory = new EmailNotificationFactory();
factory.notify("Welcome!"); // Output: Sending EMAIL: Welcome!
```

**Explanation:**

- **The client doesn’t directly instantiate concrete classes** like `EmailNotification` or `SMSNotification`.
- It only interacts with the **abstract factory** (`NotificationFactory`) and the **interface** (`Notification`).
- The **subclasses override the factory method** (createNotification) to decide which concrete object to return.
- This approach **promotes open/closed principle** – you can add new notification types (e.g., WhatsApp, Telegram) by creating new factory subclasses **without touching existing client or factory logic**.

**When to Use:**

- When the exact type of object **isn’t known until runtime**.
- When you want to **delegate object creation to subclasses** rather than having one central factory with conditional logic.
- When your system needs to **support future extensions** without modifying existing client code.
- When you want to **simplify client code** and decouple it from concrete implementations.

---

### 3. Abstract Fatory Pattern

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

const theme = "dark";
const factory =
  theme === "dark" ? new DarkThemeFactory() : new LightThemeFactory();
renderUI(factory);

// Output:
// Rendering dark-themed button
// Opening dark-themed modal
```

**Explanation:**

- The client uses only the `ThemeFactory` interface.
- Each theme factory creates `consistent and matching UI components`.
- Adding a new theme (e.g., `HighContrastThemeFactory`) is easy and doesn’t break existing code.

**When to Use:**

- When you need to create **multiple related objects** consistently.
- When product variations (like themes, platforms, configs) are **mutually exclusive**.
- When adding a new family of products should be easy and not require modifying existing client logic.

---

### 4. Prototype Pattern

**Definition:**  
The Prototype Pattern lets you create new objects by **cloning existing objects**, known as prototypes, **instead of creating them from scratch**. This is useful when object creation is **expensive or complex**.

**Intent:**

- Avoid the overhead of creating objects from scratch by **cloning pre-configured prototypes**.
- Enable new object creation at runtime without depending on their concrete classes.
- Make it easy to add and remove prototypes dynamically.

**Structure:**

- A Prototype interface declares a `clone()` method.
- **Concrete classes** implement the `clone()` method to return a copy of themselves.
- A **Client** can clone objects without knowing their concrete class.

**Example:**
In a document editor, users can choose a pre-defined template. When selected, the system clones the template and allows the user to modify their own version.

```ts
// Prototype interface
interface DocumentPrototype {
  clone(): DocumentPrototype;
  getInfo(): void;
}

// Concrete Prototype - Report Template
class ReportDocument implements DocumentPrototype {
  constructor(
    private title: string,
    private content: string,
    private author: string
  ) {}

  clone(): DocumentPrototype {
    return new ReportDocument(this.title, this.content, this.author);
  }

  getInfo(): void {
    console.log(`Report: "${this.title}" by ${this.author}`);
  }
}

// Concrete Prototype - Resume Template
class ResumeDocument implements DocumentPrototype {
  constructor(private name: string, private skills: string[]) {}

  clone(): DocumentPrototype {
    return new ResumeDocument(this.name, [...this.skills]);
  }

  getInfo(): void {
    console.log(`Resume: ${this.name} with skills ${this.skills.join(", ")}`);
  }
}

// Prototype Registry
class DocumentRegistry {
  private prototypes: Map<string, DocumentPrototype> = new Map();

  register(type: string, prototype: DocumentPrototype): void {
    this.prototypes.set(type, prototype);
  }

  create(type: string): DocumentPrototype {
    const prototype = this.prototypes.get(type);
    if (!prototype) throw new Error("No such document type registered");
    return prototype.clone();
  }
}

// Usage
const registry = new DocumentRegistry();
registry.register(
  "report",
  new ReportDocument("Monthly Report", "Q1 Data", "Finance Team")
);
registry.register(
  "resume",
  new ResumeDocument("John Doe", ["JavaScript", "TypeScript", "Node.js"])
);

const user1Doc = registry.create("report");
const user2Doc = registry.create("resume");

user1Doc.getInfo(); // Report: "Monthly Report" by Finance Team
user2Doc.getInfo(); // Resume: John Doe with skills JavaScript, TypeScript, Node.js
```

**Explanation:**

- The `clone()` method ensures each copy is independent and deeply replicated.
- The **Prototype Registry** acts like a storage of prototypes to clone from.
- The **client doesn't know** which concrete class is used — only that it implements the `DocumentPrototype`.

**When to Use:**

- When object creation is **costly or time-consuming**.
- When the system needs to create objects **at runtime** from a pool of prototypes.
- When you need to **avoid subclassing factories** for each variation.
- When you want to **decouple clients** from object instantiation..

---

### 5. Builder Pattern

**Definition:**  
The Builder Pattern separates the **construction of a complex object** from its representation so that the same construction process can create **different representations**.

**Intent:**

- Build a complex object **step-by-step**.
- Allow creation of different representations using the **same building process**.
- Encapsulate the construction logic in a **dedicated builder class**.

**Structure:**

- **Builder Interface** declares all possible building steps.
- **Concrete Builders** implement these steps to construct and assemble parts.
- **Director** defines the order in which to call steps.
- **Product** is the complex object being built.

**Example:**
For instance, we want to build an HTTP request step-by-step (method, headers, body, etc.) using a fluent and flexible API.

```ts
// Product
class HttpRequest {
  method?: string;
  url?: string;
  headers: Record<string, string> = {};
  body?: string;

  send(): void {
    console.log(`Sending ${this.method} request to ${this.url}`);
    console.log("Headers:", this.headers);
    if (this.body) console.log("Body:", this.body);
  }
}

// Builder Interface
interface HttpRequestBuilder {
  setMethod(method: string): this;
  setURL(url: string): this;
  setHeader(key: string, value: string): this;
  setBody(body: string): this;
  build(): HttpRequest;
}

// Concrete Builder
class ConcreteHttpRequestBuilder implements HttpRequestBuilder {
  private request: HttpRequest;

  constructor() {
    this.request = new HttpRequest();
  }

  setMethod(method: string): this {
    this.request.method = method;
    return this;
  }

  setURL(url: string): this {
    this.request.url = url;
    return this;
  }

  setHeader(key: string, value: string): this {
    this.request.headers[key] = value;
    return this;
  }

  setBody(body: string): this {
    this.request.body = body;
    return this;
  }

  build(): HttpRequest {
    return this.request;
  }
}

// Director (Optional)
class HttpDirector {
  constructor(private builder: HttpRequestBuilder) {}

  createPostRequest(url: string, body: string): HttpRequest {
    return this.builder
      .setMethod("POST")
      .setURL(url)
      .setHeader("Content-Type", "application/json")
      .setBody(body)
      .build();
  }
}

// Usage
const builder = new ConcreteHttpRequestBuilder();
const director = new HttpDirector(builder);

const request = director.createPostRequest(
  "https://api.example.com/data",
  JSON.stringify({ name: "Uzair", role: "Developer" })
);

request.send();

// Output:
// Sending POST request to https://api.example.com/data
// Headers: { 'Content-Type': 'application/json' }
// Body: {"name":"Uzair","role":"Developer"}
```

**Explanation:**

- We’re constructing an `HttpRequest` object **step-by-step**, keeping the logic out of the `HttpRequest` class.
- The **Builder** allows fluid construction, the **Director** encapsulates reusable construction logic.
- You can easily add different builders for other protocols (e.g., GraphQLRequestBuilder).

**When to Use:**

- When you need to build complex objects with many optional or required fields.
- When the object construction process must be **independent of the parts that make up the object**.
- When you want to reuse the same **construction process for different representations**.

---
