---
layout: post
title: "The Interface Ritual: Start Concrete, Stay Decoupled"
---

"Put your application boundaries behind interfaces" is one of the most commonly repeated pieces of advice in software engineering. It's also ritual masquerading as architecture.

In practice, most interfaces don't represent real abstractions — they're a mechanical application of "best practices" applied to a single implementation that was never designed to be abstract. 
Dropping that interface in doesn't make the system more flexible, and it doesn't meaningfully improve testability. 
It just adds indirection while giving the illusion of good design.

---

## Interfaces Don't Create Abstraction

This is the core misunderstanding: creating an interface does not create an abstraction. It creates an extra type.

Abstraction comes from how you design the boundary:

- Are you expressing behavior or mechanism?
- Are you leaking implementation details?
- Is your data model tied to a specific technology?

If your boundary is shaped around a SQL database, wrapping it in an interface doesn't make it storage-agnostic. It just hides the coupling behind another name.

---

## The Illusion of Flexibility

The most common justification is future flexibility:

> "We might swap Postgres for Redis later."

If your interface looks like this:
```kotlin
fun save(user: SqlBackedUserWithConstraints)
```

you don't have an abstraction. You have a SQL-shaped contract.

When you introduce Redis, you'll have to change the data model, the method signatures, and the interface itself. 
The interface didn't preserve flexibility—it delayed the realization that you never had any.

Interfaces don't give you flexibility. Good boundary design does.

If your boundary is well-designed—behavior-driven methods, clean technology-agnostic data, no leaked implementation details—then extracting an interface later is trivial. 
If it isn't, the interface won't save you.

---

## Design Is the Abstraction

Consider two repository implementations. The first is SQL-coupled:
```kotlin
// Bad: implementation details leak through the boundary
fun save(user: SqlBackedUserWithConstraints)
fun findBy(query: SqlQuery): List<Row>
```

The second is behavior-driven:
```kotlin
// Good: boundary expresses behavior, not mechanism
fun save(user: User)
fun findByEmail(email: Email): User?
```

Both could sit behind an interface. Only one is actually abstract.

The difference has nothing to do with the presence of an interface. 
It has everything to do with the design of the concrete implementation. 
You can take a poorly designed, SQL-coupled boundary and put it behind an interface—it's still SQL-coupled. 
You can take a well-designed, behavior-driven boundary with no interface at all—and it's already abstract.

This is the inversion most teams miss:

- A good design is flexible **before** the interface exists
- A bad design is rigid **even if** an interface exists

So if you want real flexibility, focus on boundary design:

- **Design around behavior, not mechanism** — `fun createUser(user: User)` not `fun insertRow(table: String, values: Map<String, Any>)`
- **Don't leak implementation details** — no SQL fragments, ORM types, or storage-specific constraints crossing the boundary
- **Keep your data model portable** — if your model only works in one system, no interface will make it portable

The boundary design is the important part. The interface just reflects it — or hides the lack of it.

---

## The Testing Argument (Doesn't Hold)

Another common claim:

> "We need interfaces so we can mock things."

Most modern frameworks like Mockito and MockK can mock concrete classes without interfaces. 
But the deeper problem is that unit tests are awful at testing application boundaries.
What does mocking your own boundary in a unit test actually validate?
```kotlin
val repo = mockk<UserRepo>()
every { repo.save(any()) } returns Unit

val service = UserService(repo)
service.createUser(...)
```

You verified that `save()` was called with certain arguments. 
You didn't validate whether data actually persists, whether your schema works, whether transactions behave correctly, or whether serialization is correct. 
You verified your expectations—not reality.

Unit tests have real value. 
They shine in the service layer: domain logic, input validation, pure functions with a clear input and expected output. 
But boundaries are where unit tests give you false confidence by definition — they remove the I/O and simulate it instead.

If you care about whether your system works at the boundary, test the whole thing running for real — your application and its dependencies 
(a Postgres container, a MockServer instance for external APIs) stood up locally, with a framework like Playwright hammering your API with real HTTP requests. 
I'd rather have 500 tests that exercise the real stack than 5,000 that only prove my mocks are wired correctly.

---

## The Feedback Loop

These ideas tend to reinforce each other:

- Interfaces are introduced "for flexibility"
- Interfaces enable mocking
- Mocking enables unit tests
- Unit tests justify the interfaces

You end up with layers of indirection, interfaces that forever have one implementation, and tests that validate assumptions instead of behavior.

It looks like architecture. It's not. It's ritual.

---

## When Interfaces Do Make Sense

Interfaces are useful when the abstraction is real:

- Multiple implementations already exist (e.g. `NotificationSender` backed by SendGrid in one deployment, SNS in another)
- You're building a plugin or extension system
- You're isolating a volatile external dependency you genuinely expect to change

In those cases, the abstraction comes first. The interface formalizes it.

---

## Final Thought

Interfaces are cheap. Abstraction is not.

You can add an interface in minutes when you actually need one. But no amount of interfaces will fix a boundary that was never designed to be abstract in the first place.

Start concrete. Design for abstraction. Let interfaces be the paperwork.
