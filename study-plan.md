# Design Patterns & Anti-Patterns — 2-Week Mastery Plan

**Goal:** Recognize classic patterns and anti-patterns on sight, know when to use what (and when not to), articulate trade-offs clearly — interview-ready.
**Time budget:** 1 hour/day × 14 days.
**Daily quiz:** at the end of each day, ask Claude: *"give me the Day X quiz from my study plan"* — generated fresh so you never see answers early. Day 14 is the big final (~40 questions).

## Daily rhythm (60 min)

| Time | Activity |
|------|----------|
| 0–20 min | **Learn** — read the pattern(s): intent, structure, participants |
| 20–40 min | **Apply** — implement a small example OR refactor bad code into the pattern |
| 40–50 min | **Consolidate** — write 2–3 flashcards per pattern: *intent in one sentence*, *when to use*, *trade-off/cost* |
| 50–60 min | **Daily quiz** — ask Claude for today's quiz, review anything you missed |

**Primary resource:** [Refactoring Guru](https://refactoring.guru/design-patterns) (best diagrams + pseudocode). Backup: [Sourcemaking](https://sourcemaking.com/design_patterns) (also covers anti-patterns).

---

## Week 1 — Creational & Structural patterns

### Day 1 — Foundations
- [ ] Read: why patterns exist — shared vocabulary, proven solutions, communication in interviews
- [ ] Read: the three GoF categories — **Creational** (how objects are made), **Structural** (how objects compose), **Behavioral** (how objects communicate)
- [ ] Review SOLID — patterns are mostly SOLID applied (especially Open/Closed and Dependency Inversion)
- [ ] Exercise: for 3 codebases/frameworks you know, name one pattern you suspect they use (verify on Day 12)
- [ ] Flashcards written
- [ ] Day 1 quiz taken

### Day 2 — Creational I: Factory Method, Abstract Factory
- [ ] Read: Factory Method (defer instantiation to subclasses) and Abstract Factory (families of related objects)
- [ ] Internalize the distinction: Factory Method = one product; Abstract Factory = a *family* of products that must stay consistent
- [ ] Exercise: build a notification sender (email/SMS/push) with a factory
- [ ] Exercise: extend to themed UI widgets (light/dark button + dialog) with an abstract factory
- [ ] Note the trade-off: flexibility vs. indirection — more classes, harder to trace
- [ ] Flashcards written
- [ ] Day 2 quiz taken

### Day 3 — Creational II: Builder, Singleton, Prototype
- [ ] Read: Builder — step-by-step construction, solves the telescoping-constructor problem
- [ ] Read: Singleton — and why it's widely considered a semi-anti-pattern (hidden dependencies, hard to test, global state)
- [ ] Read: Prototype — clone instead of construct (lighter coverage)
- [ ] Exercise: refactor a constructor with 7 parameters into a builder
- [ ] Write down 3 reasons Singleton hurts testability and 2 legitimate uses (loggers, config caches)
- [ ] Flashcards written
- [ ] Day 3 quiz taken

### Day 4 — Structural I: Adapter, Facade, Decorator
- [ ] Read: Adapter (make incompatible interfaces work), Facade (simple front over complex subsystem), Decorator (add behavior by wrapping)
- [ ] Internalize the distinction: Adapter *changes* an interface; Facade *simplifies* one; Decorator *keeps* it but adds behavior
- [ ] Exercise: wrap a third-party API behind an adapter
- [ ] Exercise: stack two decorators (logging + caching around a data fetcher — this is middleware!)
- [ ] Flashcards written
- [ ] Day 4 quiz taken

### Day 5 — Structural II: Composite, Proxy, Bridge, Flyweight
- [ ] Read: Composite — trees where leaves and containers share an interface (file systems, UI trees)
- [ ] Read: Proxy — a stand-in that controls access (lazy loading, access control, remote objects)
- [ ] Read: Bridge & Flyweight — intent and one example each (lighter coverage)
- [ ] Internalize the distinction: Decorator vs. Proxy — same shape, different *intent* (add behavior vs. control access)
- [ ] Exercise: model a folder/file tree with Composite; sketch a caching proxy
- [ ] Flashcards written
- [ ] Day 5 quiz taken

### Day 6 — Behavioral I: Strategy, Observer, Command
- [ ] Read: Strategy — swap algorithms at runtime (the most-used pattern in practice: comparators, pricing rules, DI)
- [ ] Read: Observer — publish/subscribe, one-to-many notification (event systems, React state, RxJS)
- [ ] Read: Command — encapsulate a request as an object (undo/redo, task queues)
- [ ] Exercise: implement a checkout with swappable discount strategies; add an observer that logs price changes
- [ ] Flashcards written
- [ ] Day 6 quiz taken

### Day 7 — Week 1 review & recognition drills
- [ ] Re-read all flashcards
- [ ] Recognition drill: read example code on Refactoring Guru *without the pattern name visible* and identify it
- [ ] Write one sentence on each confusable pair: Factory Method vs. Abstract Factory vs. Builder
- [ ] Write one sentence on each: Adapter vs. Facade vs. Decorator vs. Proxy
- [ ] Write one sentence on: Strategy vs. Command
- [ ] Self-check: name a real-world framework example for every pattern so far (interviews love this)
- [ ] Day 7 quiz taken (cumulative, Week 1)

---

## Week 2 — Behavioral patterns, anti-patterns, synthesis

### Day 8 — Behavioral II: State, Template Method, Iterator
- [ ] Read: State — behavior changes with internal state, object "changes class" (order lifecycle, TCP connection)
- [ ] Read: Template Method — skeleton algorithm in base class, steps overridden (test frameworks, hooks)
- [ ] Read: Iterator — sequential access without exposing internals (know why it's built into most languages)
- [ ] Internalize the distinctions: Strategy vs. State (client chooses vs. transitions itself); Template Method vs. Strategy (inheritance vs. composition)
- [ ] Exercise: model an order (pending → paid → shipped → delivered) with State
- [ ] Flashcards written
- [ ] Day 8 quiz taken

### Day 9 — Behavioral III: Chain of Responsibility, Mediator, Memento, Visitor
- [ ] Read: Chain of Responsibility — pass a request along handlers until one handles it (middleware, event bubbling)
- [ ] Read: Mediator — centralize communication between components (chat rooms, UI dialogs)
- [ ] Read: Memento & Visitor — intent + one example each (undo snapshots; operations over ASTs)
- [ ] Exercise: build a 3-handler validation chain (auth → rate limit → validation)
- [ ] Flashcards written
- [ ] Day 9 quiz taken

### Day 10 — Anti-patterns I: code-level
- [ ] Read: God Object / Blob — one class does everything
- [ ] Read: Spaghetti Code — no structure, tangled control flow
- [ ] Read: Copy-Paste Programming — duplication instead of abstraction
- [ ] Read: Magic Numbers/Strings — unexplained literals
- [ ] Read: Golden Hammer — one familiar tool for every problem
- [ ] Read: Premature Optimization — complexity before evidence
- [ ] Exercise: for each, write the *smell* (how you spot it), the *cost*, and the *fix* (e.g., God Object → extract classes, Strategy, Facade)
- [ ] Day 10 quiz taken

### Day 11 — Anti-patterns II: design & architecture level
- [ ] Read: Big Ball of Mud — architecture without architecture
- [ ] Read: Lava Flow — dead code nobody dares remove
- [ ] Read: Anemic Domain Model — data classes with no behavior, logic leaks everywhere
- [ ] Read: Poltergeist — short-lived classes that only pass calls along
- [ ] Read: Singleton abuse — global state disguised as a pattern
- [ ] Read: Pattern-itis / over-engineering — *the anti-pattern interviewers probe hardest: knowing when NOT to use a pattern*
- [ ] Exercise: review code you've written or a repo you know; find one anti-pattern and name the fix
- [ ] Day 11 quiz taken

### Day 12 — When to use what: the decision framework
- [ ] Build a one-page decision cheat sheet organized by *problem*, not by pattern:
  - "Object creation is complex/conditional" → Factory / Builder
  - "Vary behavior at runtime" → Strategy / State
  - "React to changes" → Observer
  - "Add behavior without modifying" → Decorator
  - "Interfaces don't match" → Adapter
  - "Subsystem too complex for callers" → Facade
  - "Requests need queuing/undo" → Command
  - "One of several handlers may handle it" → Chain of Responsibility
- [ ] Verify your Day 1 framework guesses (middleware = Chain of Responsibility + Decorator; event emitters = Observer; DI containers = Factory + singleton-scoped instances)
- [ ] Internalize the rule of thumb: reach for a pattern when you feel the *pain it solves* — never preemptively
- [ ] Day 12 quiz taken

### Day 13 — Interview drills
- [ ] Scenario → pattern, out loud, timed (2 min each):
  - [ ] Payment system supporting new providers without touching checkout code
  - [ ] Undo/redo in an editor
  - [ ] Notify many UI widgets when data changes
  - [ ] Legacy API with a wrong-shaped interface
  - [ ] Report generator with fixed steps but varying formats
  - [ ] Request passes through auth, logging, and validation layers
- [ ] For each: name the pattern, sketch the structure, **state one trade-off**, **name one rejected alternative and why** — that four-part structure is what distinguishes strong interview answers
- [ ] Re-drill your weakest confusable pairs from Day 7
- [ ] Day 13 quiz taken

### Day 14 — Final quiz + review
- [ ] Take the final ~40-question quiz (ask Claude: *"give me the Day 14 final design patterns quiz from my study plan"*) — covers pattern identification from code, scenario → best pattern, trade-offs, confusable pairs, anti-pattern recognition
- [ ] Review every miss: re-read that pattern's page and update its flashcard
- [ ] Keep the Day 12 cheat sheet and flashcards for pre-interview review

---

## Pattern coverage checklist

**Creational**
- [ ] Factory Method
- [ ] Abstract Factory
- [ ] Builder
- [ ] Singleton
- [ ] Prototype

**Structural**
- [ ] Adapter
- [ ] Facade
- [ ] Decorator
- [ ] Composite
- [ ] Proxy
- [ ] Bridge
- [ ] Flyweight

**Behavioral**
- [ ] Strategy
- [ ] Observer
- [ ] Command
- [ ] State
- [ ] Template Method
- [ ] Iterator
- [ ] Chain of Responsibility
- [ ] Mediator
- [ ] Memento
- [ ] Visitor

**Anti-patterns**
- [ ] God Object
- [ ] Spaghetti Code
- [ ] Copy-Paste Programming
- [ ] Magic Numbers
- [ ] Golden Hammer
- [ ] Premature Optimization
- [ ] Big Ball of Mud
- [ ] Lava Flow
- [ ] Anemic Domain Model
- [ ] Poltergeist
- [ ] Singleton abuse
- [ ] Over-engineering
