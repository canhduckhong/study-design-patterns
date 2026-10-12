# Day 2 Quiz — Factory Method & Abstract Factory

*10 questions. Answer all before scrolling to the key. Target: 8/10.*

**1.** Which statement best captures the intent of **Factory Method**?
- A) Guarantee that only one instance of a class exists
- B) Define a creation method in a base class and let subclasses decide which concrete class to instantiate
- C) Build a complex object step by step
- D) Create families of related objects that must be used together

**2.** Which statement best captures the intent of **Abstract Factory**?
- A) A factory class declared with the `abstract` keyword
- B) A static method that picks a class based on a string parameter
- C) An interface for creating families of related or dependent objects without specifying their concrete classes
- D) A base class whose subclasses override a single creation hook

**3.** Identify the pattern:

```ts
abstract class Dialog {
  abstract createButton(): Button
  render() {
    const ok = this.createButton()
    ok.onClick(() => this.close())
    return `<div class="dialog">${ok.render()}</div>`
  }
  close() { /* ... */ }
}
class WindowsDialog extends Dialog { createButton() { return new WindowsButton() } }
class WebDialog extends Dialog { createButton() { return new HtmlButton() } }
```
- A) Abstract Factory
- B) Factory Method
- C) Simple Factory
- D) Builder

**4.** Identify the pattern:

```ts
function createLogger(kind: "console" | "file"): Logger {
  switch (kind) {
    case "console": return new ConsoleLogger()
    case "file": return new FileLogger("/var/log/app.log")
  }
}
```
- A) Factory Method — it's a method that creates objects
- B) Abstract Factory — it hides concrete classes
- C) Simple Factory — centralizes creation, but is not one of the GoF patterns
- D) Prototype

**5.** Your app supports **MySQL** and **PostgreSQL**. For each database you need a `Connection`, a `QueryBuilder`, and a `MigrationRunner`, and a Postgres `QueryBuilder` produces SQL a MySQL `Connection` can't execute. Which pattern fits best?
- A) Factory Method on the `Connection` class
- B) Abstract Factory — one `DatabaseFactory` per vendor that creates all three
- C) Singleton per vendor
- D) A static `Connection.fromUrl()` method

**6.** You have `ThemeFactory` with `createButton()` and `createDialog()`, implemented by `LightThemeFactory` and `DarkThemeFactory`. Product asks for (i) a new **HighContrast** theme and (ii) a new **Tooltip** widget. Which statement is accurate?
- A) Both are equally easy — add one class each
- B) (i) is easy: add one factory and its products. (ii) is costly: change the `ThemeFactory` interface and every existing factory
- C) (i) is costly, (ii) is easy
- D) Both require rewriting the client

**7.** What's the main **mechanical** difference between Factory Method and Abstract Factory, as originally described?
- A) Factory Method relies on inheritance (a subclass overrides the creation method); Abstract Factory relies on composition (the client is given a factory object)
- B) Factory Method is for UI, Abstract Factory is for databases
- C) Abstract Factory must be a Singleton; Factory Method must not
- D) There is no difference; they are two names for the same pattern

**8.** A teammate writes a `UserFactory` class whose only method is `create() { return new User() }`. There is one `User` class and no plans for more. Your best review comment:
- A) Great, factories always improve testability
- B) Convert it to an Abstract Factory to be safe
- C) It adds indirection with no benefit: one concrete class, no variants, no shared creation logic. Use `new User()` and introduce a factory when a second implementation appears
- D) Make it a Singleton too

**9.** Which of these is a **Factory Method** in the GoF sense?
- A) `Array.from(iterable)`
- B) `Promise.resolve(value)`
- C) Each collection class implementing `[Symbol.iterator]()` to return its own iterator type, which `for...of` uses without knowing the concrete iterator
- D) `JSON.parse(text)`

**10.** In Factory Method, what's usually the Creator's **primary** responsibility?
- A) Creating products. That's all it should do
- B) Some business logic that uses the product. The factory method is a hook that lets subclasses swap which product that logic works with
- C) Caching every product it has created
- D) Validating the constructor arguments of every product

---
---

## Answer key (spoilers)

**1 — B.** "Defer instantiation to subclasses." A is Singleton, C is Builder (Day 3), D is Abstract Factory.

**2 — C.** The word that matters is *families*. A is a trap: being abstract has nothing to do with the name. B is Simple Factory, D is Factory Method.

**3 — B.** `Dialog` has real business logic (`render`, wiring the click to `close`) and one abstract creation hook that subclasses override. It's one product, chosen by inheritance, so it's Factory Method. It would become Abstract Factory if a separate object created several matching widgets and was handed to the dialog.

**4 — C.** A free function with a switch. It's useful and often the right first step, but it's not a GoF pattern, and adding a logger kind means editing the switch.

**5 — B.** Three products that must come from the **same** vendor is the definition of a family. Abstract Factory makes mixing a Postgres `QueryBuilder` with a MySQL `Connection` impossible by construction.

**6 — B.** This is the Abstract Factory asymmetry. New families are cheap and follow Open/Closed. New product kinds ripple through the interface and every concrete factory. Use the pattern when product kinds are stable and families grow.

**7 — A.** Factory Method: subclass overrides one method (inheritance). Abstract Factory: client holds a factory object and delegates (composition). Abstract Factory's individual `createX()` methods are often implemented as factory methods, which is why the two get confused.

**8 — C.** Knowing when **not** to use a pattern is the senior signal. A factory earns its keep when there are real variants or non-trivial creation logic. Here it's pure indirection. (Day 11's "pattern-itis" in miniature.)

**9 — C.** Each collection subclass decides which concrete iterator to create, and client code (`for...of`) works against the iterator interface. A, B, D are **static factory methods** (named constructors), which share the word "factory" but aren't the GoF pattern.

**10 — B.** Its main job is the logic that *uses* the product. If a "creator" only returns `new X()`, it's a simple factory with extra inheritance. That's why Factory Method is often described as Template Method applied to object creation.

---

**Score yourself.** Missed questions → re-read that section in `theory.md`, then tick off Day 2 in `../study-plan.md`.
