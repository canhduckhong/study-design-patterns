# Day 2 — Creational I: Factory Method, Abstract Factory

> 📖 **Interactive version:** https://claude.ai/artifact/FW8NXeychjVDQxrEjH7G6D — same content with section progress checkmarks, class diagrams, a live "mix the families" demo, and tap-to-reveal flashcards.

*Read time: ~20 min. Then do the two exercises (~20 min), write flashcards (~10 min), take the quiz (~10 min).*

---

## 1. The problem both patterns solve

Every `new ConcreteClass()` is a **hard-coded decision**. One is harmless. The pain starts when the same decision is scattered across the codebase, or when it depends on runtime input:

```ts
// checkout.ts
const exporter = format === "pdf" ? new PdfFormatter() : new CsvFormatter()
// admin.ts
const exporter = format === "pdf" ? new PdfFormatter() : format === "csv" ? new CsvFormatter() : new XlsxFormatter()
// cron.ts — someone forgot Xlsx here
```

Adding a format means hunting down every branch. The caller *only wants something that formats a report*; it shouldn't have to know which classes exist. That's a **Dependency Inversion** violation (Day 1): high-level code depends on concretions.

Creational patterns move the "which class?" decision to **one place** and let callers depend on an interface.

### Baseline: the Simple Factory (not a GoF pattern)

The first fix most people write — and often the right one:

```ts
function createFormatter(format: "pdf" | "csv"): ReportFormatter {
  switch (format) {
    case "pdf": return new PdfFormatter()
    case "csv": return new CsvFormatter()
  }
}
```

It centralizes the decision, but it's **not** one of the 23 GoF patterns. It's a static/free function with a switch, so adding a type still means editing it (not fully Open/Closed). Knowing that "Simple Factory ≠ Factory Method" is a classic interview check.

> **Also not Factory Method:** *static factory methods* like `Array.from()`, `Promise.resolve()`, `Date.UTC()` or `User.fromJSON()`. Those are named constructors — useful, but a different idea that happens to share the word "factory".

---

## 2. Factory Method

**Intent:** *Define an interface for creating an object, but let subclasses decide which class to instantiate.* Factory Method defers instantiation to subclasses.

### Participants

| Role | What it is | In the example |
|---|---|---|
| **Product** | Interface the created objects share | `ReportFormatter` |
| **Concrete Product** | Actual implementations | `PdfFormatter`, `CsvFormatter` |
| **Creator** | Class with business logic that *uses* a product, plus an abstract factory method | `ReportExporter` |
| **Concrete Creator** | Subclass that overrides the factory method | `PdfExporter`, `CsvExporter` |

```
       Creator                         Product
 ┌─────────────────────┐        ┌───────────────────┐
 │ export(data)        │──uses─▶│ «interface»       │
 │ #createFormatter()* │        │ format(rows)      │
 └─────────▲───────────┘        └────────▲──────────┘
           │ extends                     │ implements
 ┌─────────┴───────────┐        ┌────────┴──────────┐
 │ PdfExporter         │─new───▶│ PdfFormatter      │
 │ createFormatter()   │        └───────────────────┘
 └─────────────────────┘
```

### Example (TypeScript)

```ts
interface ReportFormatter {
  readonly extension: string
  format(rows: Record<string, unknown>[]): string
}

class CsvFormatter implements ReportFormatter {
  readonly extension = "csv"
  format(rows: Record<string, unknown>[]) {
    const header = Object.keys(rows[0] ?? {}).join(",")
    return [header, ...rows.map(r => Object.values(r).join(","))].join("\n")
  }
}

class JsonFormatter implements ReportFormatter {
  readonly extension = "json"
  format(rows: Record<string, unknown>[]) { return JSON.stringify(rows, null, 2) }
}

abstract class ReportExporter {
  // ↓ the factory method — the ONLY thing subclasses must supply
  protected abstract createFormatter(): ReportFormatter

  // ↓ the real job of the Creator: business logic that works with ANY formatter
  export(name: string, rows: Record<string, unknown>[]) {
    const formatter = this.createFormatter()
    const body = formatter.format(rows)
    return { filename: `${name}-${new Date().toISOString().slice(0, 10)}.${formatter.extension}`, body }
  }
}

class CsvExporter extends ReportExporter {
  protected createFormatter() { return new CsvFormatter() }
}
class JsonExporter extends ReportExporter {
  protected createFormatter() { return new JsonFormatter() }
}

// Client depends only on the abstract Creator
function runNightlyExport(exporter: ReportExporter) {
  return exporter.export("sales", [{ sku: "A1", qty: 3 }])
}
runNightlyExport(new CsvExporter())
```

Adding XLSX tomorrow = one new `XlsxFormatter` + one new `XlsxExporter`. **Zero edits** to `ReportExporter` or `runNightlyExport` → Open/Closed.

### The point people miss

The Creator is **not** a factory class whose job is creating things. Its main responsibility is some **business logic** (`export`) that needs a product; the factory method is a *hook* that lets subclasses plug in the product. If your "creator" does nothing but return `new X()`, you've written a simple factory with extra inheritance.

It's often called "Template Method applied to creation" — the base class owns the algorithm, a subclass fills in one step (you'll meet Template Method on Day 8).

### Variants you'll see in the wild

- **Default implementation:** the Creator's factory method isn't abstract; it returns a sensible default, and subclasses override only when needed.
- **Parameterized factory method:** `createFormatter(kind)` with a switch inside — blurs into Simple Factory.
- **Composition instead of inheritance (idiomatic TS/JS):** pass the factory in as a function:
  ```ts
  class ReportExporter {
    constructor(private createFormatter: () => ReportFormatter) {}
  }
  new ReportExporter(() => new CsvFormatter())
  ```
  Same intent, no subclass explosion. In interviews, mention you know the GoF form *and* this lighter one.

### Real-world sightings

- `[Symbol.iterator]()` in JS / `iterator()` in Java collections — each collection subclass returns its own iterator type.
- Framework hooks such as Django's `get_form_class()` / `get_queryset()`, or `createElement`-style hooks in UI frameworks you override to swap the produced class.
- Test doubles: subclass the creator in a test and override the factory method to return a fake.

### Trade-offs

| ✅ Gains | ❌ Costs |
|---|---|
| Client decoupled from concrete products (DIP) | One new subclass per product → **parallel class hierarchies** |
| Add products without editing the creator (OCP) | Indirection: "which formatter actually runs?" needs a jump through the hierarchy |
| Creation code in one place (SRP) | Inheritance-based: tied to subclassing, which TS/JS often avoids |

---

## 3. Abstract Factory

**Intent:** *Provide an interface for creating **families of related or dependent objects** without specifying their concrete classes.*

The keyword is **family**. You don't need one product; you need several that **must match each other**. Mixing them is a bug.

### Participants

| Role | What it is | In the example |
|---|---|---|
| **Abstract Products** | One interface per product kind | `BlobStore`, `JobQueue` |
| **Concrete Products** | One implementation per family per kind | `S3BlobStore`, `SqsJobQueue`, `MemoryBlobStore`, `MemoryJobQueue` |
| **Abstract Factory** | One creation method per product kind | `InfraFactory` |
| **Concrete Factory** | One per family | `AwsInfraFactory`, `InMemoryInfraFactory` |
| **Client** | Receives a factory, never sees concrete classes | `UploadService` |

```
                  «interface» InfraFactory
                  createBlobStore(): BlobStore
                  createQueue():     JobQueue
                   ▲                        ▲
     AwsInfraFactory                 InMemoryInfraFactory
      ├─ S3BlobStore                   ├─ MemoryBlobStore
      └─ SqsJobQueue                   └─ MemoryJobQueue
           (family: AWS)                    (family: tests / local dev)
```

### Example (TypeScript)

```ts
interface BlobStore { put(key: string, data: string): Promise<string> }  // returns a URL
interface JobQueue  { enqueue(job: { type: string; payload: unknown }): Promise<void> }

// --- AWS family ---
class S3BlobStore implements BlobStore {
  async put(key: string, _data: string) { return `s3://uploads/${key}` }
}
class SqsJobQueue implements JobQueue {
  async enqueue(job: { type: string }) { console.log("SQS ←", job.type) }
}

// --- In-memory family (tests, local dev) ---
class MemoryBlobStore implements BlobStore {
  files = new Map<string, string>()
  async put(key: string, data: string) { this.files.set(key, data); return `mem://${key}` }
}
class MemoryJobQueue implements JobQueue {
  jobs: unknown[] = []
  async enqueue(job: unknown) { this.jobs.push(job) }
}

// --- The abstract factory ---
interface InfraFactory {
  createBlobStore(): BlobStore
  createQueue(): JobQueue
}
class AwsInfraFactory implements InfraFactory {
  createBlobStore() { return new S3BlobStore() }
  createQueue()     { return new SqsJobQueue() }
}
class InMemoryInfraFactory implements InfraFactory {
  createBlobStore() { return new MemoryBlobStore() }
  createQueue()     { return new MemoryJobQueue() }
}

// --- Client: never names a concrete class ---
class UploadService {
  private store: BlobStore
  private queue: JobQueue
  constructor(infra: InfraFactory) {
    this.store = infra.createBlobStore()
    this.queue = infra.createQueue()
  }
  async upload(name: string, data: string) {
    const url = await this.store.put(name, data)
    await this.queue.enqueue({ type: "thumbnail", payload: { url } })
    return url
  }
}

// Composition root — the ONE place the family is chosen
const infra = process.env.NODE_ENV === "test" ? new InMemoryInfraFactory() : new AwsInfraFactory()
new UploadService(infra)
```

The bug this prevents: writing to an in-memory store and then enqueueing a job on real SQS whose worker tries to read `mem://...` from S3. **One factory = one family = guaranteed consistency.**

### The asymmetry (interviewers love this)

| Change | Cost |
|---|---|
| Add a new **family** (e.g. `GcpInfraFactory`) | Easy — one new factory + its products. No existing code changes. ✅ OCP |
| Add a new **product kind** (e.g. `createCache()`) | Hard — change the `InfraFactory` interface **and every concrete factory**. ❌ |

So Abstract Factory fits when the set of product *kinds* is stable and the set of *families* grows.

### Relationship to Factory Method

- Each `createX()` method on a concrete factory **is** effectively a factory method — Abstract Factory is frequently implemented with factory methods.
- **Factory Method uses inheritance:** a subclass overrides one method to pick a product.
- **Abstract Factory uses composition:** the client is *given* a factory object and delegates to it.
- Concrete factories are usually stateless, so you typically need only one instance per family (often registered once in a DI container).

### Real-world sightings

- Cross-platform UI toolkits: Java AWT `Toolkit`, Swing look-and-feel — a family of native widgets per OS.
- `DbProviderFactory` in .NET: creates `Connection`, `Command`, `Parameter` that all belong to the same database driver.
- Theme systems: a theme object that hands out matching button, input, and dialog styles.
- Test vs prod infrastructure wiring, exactly like the example above.

### Trade-offs

| ✅ Gains | ❌ Costs |
|---|---|
| **Consistency guarantee** — products from one family always match | Many interfaces + classes: (kinds × families) products + factories |
| Swap a whole family in one line (environment, theme, vendor) | Adding a new product kind touches every factory |
| Client fully decoupled from concrete classes | Hard to trace: "which `JobQueue` am I talking to?" |

---

## 4. Side by side

| | Simple Factory | Factory Method | Abstract Factory |
|---|---|---|---|
| GoF pattern? | No | Yes | Yes |
| Creates | One product, chosen by a parameter | **One** product | A **family** of related products |
| Mechanism | Function/static method with a switch | **Inheritance** — subclass overrides a method | **Composition** — client receives a factory object |
| Add a new variant | Edit the switch | Add a subclass | Add a factory (new family) |
| Typical smell it cures | `new` + `if` duplicated everywhere | Base class logic that needs "some kind of" product | Objects that must be used together get mixed up |
| One-line memory hook | "a switch in one place" | "subclass picks the product" | "factory of a matching set" |

**Day 3 preview:** Builder is the third creational sibling — it's about constructing **one complex object step by step**, not about choosing *which* class.

---

## 5. When NOT to use them

The plan's trade-off note: **flexibility vs. indirection — more classes, harder to trace.**

- Only one implementation exists and no second is foreseeable → just call `new`. You can introduce a factory the day the second one shows up.
- Variants differ only by data, not behavior → pass config, don't create a class per variant.
- In TS/JS a plain function or a `Record<Kind, () => Product>` map often gives you 90% of the benefit with 10% of the ceremony.
- If you can't explain *which family must stay consistent*, you don't need Abstract Factory.

> **Interview framing to memorize:** *"Factory Method lets a subclass decide which single product to create; Abstract Factory hands the client one object that produces a whole matching family. I'd start with a plain function and promote it to one of these when I have multiple real implementations — or families that must not be mixed."*

---

## 6. Exercises (~20 min)

Write your code in `day-02/exercises/`. No solutions are given here; check yourself against the acceptance list.

### Exercise A — Notification sender (Factory Method), ~10 min

Build `notification.ts`:
- `interface Notifier { send(to: string, message: string): void }` with `EmailNotifier`, `SmsNotifier`, `PushNotifier` (just `console.log` with a prefix).
- An abstract creator `NotificationService` with business logic `notify(user, message)` that: trims the message, truncates it to the channel's limit (SMS = 160 chars), and calls `send`. It obtains the notifier through an abstract `createNotifier()`.
- Concrete creators for each channel.

**Acceptance:** `notify()` contains no `if`/`switch` on channel type · adding `SlackNotifier` needs zero edits to existing classes · bonus: rewrite with a `() => Notifier` constructor parameter instead of subclasses and note which you prefer.

> Hint: where does the 160-char limit live? On the product (`notifier.maxLength`) is cleaner than on the creator.

### Exercise B — Themed UI widgets (Abstract Factory), ~10 min

Build `widgets.ts`:
- Products: `Button` with `render(): string` and `Dialog` with `render(title: string): string`.
- Families: `LightButton`/`LightDialog` and `DarkButton`/`DarkDialog` (return strings like `[light button]`).
- `interface ThemeFactory { createButton(): Button; createDialog(): Dialog }` with `LightThemeFactory`, `DarkThemeFactory`.
- A client `SettingsScreen` that takes a `ThemeFactory` and renders one dialog containing one button.

**Acceptance:** `SettingsScreen` never mentions `Light*` or `Dark*` · switching theme is a one-line change · it's impossible for `SettingsScreen` to render a light button inside a dark dialog.

**Then answer in a comment:** what changes if you add a `HighContrastThemeFactory`? What changes if you add a `Tooltip` product? (This is the asymmetry from §3.)

---

## 7. Flashcards (~10 min)

1. **Factory Method — intent in one sentence?** → Let subclasses decide which concrete product a creator instantiates, via an overridable creation method.
2. **Abstract Factory — intent in one sentence?** → One factory object creates a whole family of related products, guaranteeing they're compatible.
3. **Factory Method vs. Abstract Factory — the core distinction?** → One product via inheritance vs. a consistent family via composition.
4. **Is "Simple Factory" a GoF pattern? Is `Array.from()` a Factory Method?** → No and no — a switch-function and a static named constructor, respectively.
5. **Abstract Factory: what's easy to add and what's hard?** → New family = easy (new factory). New product kind = hard (edit interface + every factory).
6. **Main cost of both patterns?** → Indirection and class count — more types, harder to trace which concrete class runs. Don't introduce until there are real variants.

## 8. Quiz

Take `quiz.md` in this folder. Don't scroll past the spoiler line until you've answered all questions.
