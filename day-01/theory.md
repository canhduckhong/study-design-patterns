# Day 1 — Foundations: Why Patterns, the Three Categories, SOLID

> 📖 **Interactive version:** https://claude.ai/artifact/Wo59jSkukMwztSQHow1gUg — same content with section progress checkmarks, tap-to-reveal flashcards, and the pattern map color-coded by category.

*Read time: ~20 min. Then do the exercise (~20 min), write flashcards (~10 min), take the quiz (~10 min).*

---

## 1. Why design patterns exist

A design pattern is a **named, reusable solution to a recurring design problem**. The 23 "classic" ones come from the 1994 *Design Patterns* book by the "Gang of Four" (GoF) — Gamma, Helm, Johnson, Vlissides. They didn't invent the solutions; they catalogued what experienced developers were already doing and **gave each solution a name**.

The name is the point. Patterns give you three things:

1. **Shared vocabulary.** "Let's put a Strategy here" transmits an entire design — an interface, interchangeable implementations, runtime selection — in three words. In an interview, using the right name precisely signals experience faster than anything else.
2. **Proven trade-offs.** Each pattern comes with known costs and benefits, already debugged by thousands of teams. You're not guessing.
3. **A design checklist.** When you feel a specific pain (rigidity, duplication, a class that can't be extended without editing it), patterns are the catalog of known cures.

**What patterns are NOT:**
- Not algorithms (an algorithm solves a computation; a pattern shapes a structure).
- Not code you copy-paste — they're *templates* you adapt to your language and situation.
- Not goals. **Code doesn't get better by containing more patterns.** Using a pattern where a plain function suffices is itself an anti-pattern (you'll meet it on Day 11 as "pattern-itis").

> **Interview framing to memorize:** *"Patterns are a vocabulary for discussing design trade-offs. I reach for one when I feel the pain it solves, not preemptively."*

---

## 2. The three GoF categories

Every classic pattern answers one of three questions:

### Creational — *how are objects made?*
Decouple code from the concrete classes it instantiates. The `new` keyword hard-codes a class; creational patterns make "which class, configured how" a flexible decision.

| Pattern | One-liner |
|---|---|
| Factory Method | Subclass decides which concrete product to create |
| Abstract Factory | Create whole *families* of related products consistently |
| Builder | Assemble a complex object step by step |
| Singleton | Exactly one instance, globally accessible |
| Prototype | Create by cloning an existing instance |

### Structural — *how do objects compose into larger structures?*
Combine objects and classes into bigger units while keeping them flexible.

| Pattern | One-liner |
|---|---|
| Adapter | Convert one interface into the one the client expects |
| Facade | One simple entry point over a complex subsystem |
| Decorator | Wrap an object to add behavior, same interface |
| Composite | Trees where one item and a group share an interface |
| Proxy | A stand-in that controls access to the real object |
| Bridge | Split two independent dimensions of variation |
| Flyweight | Share state across many objects to save memory |

### Behavioral — *how do objects communicate and share responsibility?*
Assign responsibilities and define how objects talk to each other.

| Pattern | One-liner |
|---|---|
| Strategy | Swap an algorithm at runtime |
| Observer | Notify many subscribers when something changes |
| Command | Turn a request into an object (queue it, undo it) |
| State | Behavior changes as internal state changes |
| Template Method | Fixed algorithm skeleton, overridable steps |
| Iterator | Walk a collection without exposing its internals |
| Chain of Responsibility | Pass a request down a line of handlers |
| Mediator | Centralize communication between components |
| Memento | Snapshot state for later restore |
| Visitor | Add operations to an object structure from outside |

**Quick self-test trick:** given a pattern, ask *"is the problem about making objects, arranging objects, or objects talking?"* That classifies almost everything.

---

## 3. SOLID refresher — patterns are SOLID applied

Most patterns are mechanical ways to achieve one or two SOLID principles:

- **S — Single Responsibility:** a class should have one reason to change.
  → *Facade* (pull coordination out), *Decorator* (each wrapper = one concern).
- **O — Open/Closed:** open for extension, closed for modification. **The engine behind most patterns.**
  → *Strategy* (add an algorithm without editing the context), *Decorator* (add behavior without editing the class), *Factory Method* (add a product without editing the creator), *Observer* (add a subscriber without editing the publisher).
- **L — Liskov Substitution:** a subtype must be usable anywhere its base type is.
  → This is what makes wrapping patterns (*Decorator*, *Proxy*, *Composite*) work at all — the wrapper must be a drop-in substitute.
- **I — Interface Segregation:** many small interfaces beat one fat one.
  → Keeps *Strategy*/*Command* interfaces tiny (often one method).
- **D — Dependency Inversion:** depend on abstractions, not concretions.
  → The reason *factories* exist: the client depends on `Notifier`, the factory picks `EmailNotifier`.

The umbrella slogan uniting all of it: **"program to an interface, not an implementation"** — callers depend on a contract, so implementations can vary freely.

A 10-line taste (TypeScript) of Open/Closed via Strategy — you'll build this properly on Day 6:

```ts
interface DiscountStrategy { apply(total: number): number }

class NoDiscount implements DiscountStrategy { apply(t: number) { return t } }
class BlackFriday implements DiscountStrategy { apply(t: number) { return t * 0.7 } }

class Checkout {
  constructor(private discount: DiscountStrategy) {}   // depends on abstraction (D)
  total(subtotal: number) { return this.discount.apply(subtotal) }  // extensible without edits (O)
}
```

Adding a `StudentDiscount` tomorrow touches **zero** existing lines. That feeling — *extend without modify* — is what half the catalog exists to create.

---

## 4. Exercise (~20 min)

For **3 frameworks or codebases you know**, write down one pattern you *suspect* they use and why. Don't look anything up — these are hypotheses you'll verify on Day 12. Prompts if you're stuck:

- What does Express/Koa middleware remind you of?
- What is an event emitter / `addEventListener` doing?
- How does your ORM hand you objects without you calling `new`?
- What's a React higher-order component or a Python decorator structurally doing?

Write them in a file `day-01/hypotheses.md` — three lines, each: *framework → suspected pattern → why*.

## 5. Flashcards (~10 min)

Make these five (question on front, answer on back):

1. What are the three GoF categories, and what question does each answer?
2. What's the primary value of design patterns on a team?
3. State the Open/Closed principle in one sentence, and name two patterns that embody it.
4. What does "program to an interface, not an implementation" buy you?
5. When is the right moment to introduce a pattern? (Answer: when you feel the pain it solves — never preemptively.)

## 6. Quiz

Take `quiz.md` in this folder. Don't scroll past the spoiler line until you've answered all questions.
