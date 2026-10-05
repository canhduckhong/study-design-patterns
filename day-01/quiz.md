# Day 1 Quiz — Foundations

*8 questions. Answer all before scrolling to the key. Target: 7/8.*

**1.** What is the primary value of design patterns on a team?
- A) They make code run faster
- B) They provide a shared vocabulary for proven solutions to recurring design problems x
- C) They guarantee bug-free code
- D) More patterns = better design

**2.** Which question do **Creational** patterns answer?
- A) How do objects communicate?
- B) How are objects composed into larger structures?
- C) How are objects made, and how do we decouple code from concrete classes? x
- D) How is data persisted?

**3.** Observer, Strategy, and Command all belong to which category?
- A) Creational
- B) Structural
- C) Behavioral x
- D) They're in different categories

**4.** Which list contains **only** structural patterns?
- A) Adapter, Facade, Decorator, Composite x
- B) Strategy, Adapter, Builder, Proxy
- C) Singleton, Bridge, Observer, Flyweight
- D) Factory Method, Facade, State, Iterator

**5.** Strategy, Decorator, and Factory Method are all primarily mechanisms for achieving which SOLID principle?
- A) Single Responsibility
- B) Open/Closed — extend behavior without modifying existing code x
- C) Interface Segregation
- D) Liskov Substitution

**6.** Which SOLID principle makes wrapping patterns (Decorator, Proxy) possible at all, because the wrapper must be a drop-in substitute for the wrapped object?
- A) Open/Closed
- B) Dependency Inversion
- C) Liskov Substitution x
- D) Single Responsibility

**7.** "Program to an interface, not an implementation" primarily means:
- A) Every class must have a matching interface file
- B) Callers depend on a contract, so concrete implementations can be swapped without changing the caller x
- C) Design the API before the database
- D) Avoid inheritance entirely

**8.** A teammate proposes adding Abstract Factory, Strategy, and Observer to a 200-line script "so it follows best practices." The best response reflects:
- A) Agree — patterns are always an improvement
- B) Patterns should be introduced when a concrete pain (duplication, rigidity, untestability) appears, not preemptively; premature patterns add indirection with no benefit x
- C) Only Singleton should be added preemptively
- D) Patterns only belong in compiled languages

---
---

## Answer key (spoilers)

**1 — B.** Patterns are first a *communication* tool; they say nothing about performance or correctness, and more patterns is often worse (see Q8).

**2 — C.** Creational = how objects are made. `new` hard-codes a class; creational patterns make that choice flexible.

**3 — C.** All three are about objects communicating / dividing responsibility → Behavioral.

**4 — A.** B has Strategy (behavioral) + Builder (creational); C has Singleton (creational) + Observer (behavioral); D has Factory Method (creational) + State and Iterator (behavioral).

**5 — B.** All three let you add a new algorithm/wrapper/product without editing existing code — Open/Closed in three different mechanical forms.

**6 — C.** If `LoggingFetcher` weren't substitutable wherever `Fetcher` is expected, wrapping would break clients. Liskov is the load-bearing wall under every same-interface wrapper.

**7 — B.** It's about substitutability through contracts — the foundation of Dependency Inversion and of nearly every pattern you'll study.

**8 — B.** Knowing when **not** to use patterns is the strongest signal of maturity, and interviewers probe it deliberately. The pain comes first; the pattern is the cure, not a vitamin.

---

**Score yourself.** Missed questions → re-read that section in `theory.md`, then tick off Day 1 in `../study-plan.md`.
