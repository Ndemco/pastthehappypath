---
layout: post
title: "Test the Contract, Not the Code: The Case for Integration-First Testing"
---
A unit test has never caught a missing header.

That's not a knock on unit tests. They're fast, focused, and great at what they do. But "great at what they do" and "right for the job" aren't the same thing.

The testing pyramid tells us to write unit tests by the hundreds, and integration tests sparingly.
And like [the food pyramid](https://www.uabmedicine.org/news/myplate-healthy-eating-chart-replaced-the-food-pyramid) of 1992,
it became gospel — repeated in bootcamps, blog posts, and code reviews — until most of us stopped asking whether it actually fit our context.

For backend APIs, I don't think it does. Follow it religiously and you'll end up with a test suite that's fast, green, and completely blind to the bugs that actually take down production.

My argument is simple: integration tests should be your foundation, and unit tests should fill in the gaps. Let me show you why.

___

## The Cracks Are Always at the Seams

There's no clean study that puts an exact number on this (I looked) but every developer currently on an on-call rotation knows this to be true:
production incidents routinely start at a service boundary and cascade outward.
Think transactionality gaps, expired tokens, and breaking changes to your API. None of these live in your business logic. They live at the seams.

This is where the main issue with the testing pyramid is apparent. Units tests are really, **really** bad at testing your application boundaries.

A well-designed application boundary is thin and just performs the IO with minimal logic. Consider this simple repository class:
```java
@Repository
public class UserRepository {

    private final JdbcTemplate jdbcTemplate;

    public UserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public Optional<User> findById(long id) {
        return jdbcTemplate.query(
                "SELECT id, name, email FROM users WHERE id = ?",
                (rs, rowNum) -> new User(
                        rs.getLong("id"),
                        rs.getString("name"),
                        rs.getString("email")
                ),
                id
        ).stream().findFirst();
    }
}
```

And here's what the unit test for it inevitably looks like:
```java
@Test
void findById_returnsUser() {
    User expected = new User(1L, "John Doe", "john@example.com");

    when(jdbcTemplate.query(anyString(), any(RowMapper.class), eq(1L)))
        .thenReturn(List.of(expected));

    Optional<User> result = userRepository.findById(1L);

    assertThat(result).contains(expected);
}
```
This test is essentially asserting: "given that the database returns a user, does my method return that user?", which is a tautology.
You mocked the only interesting thing that could happen, then verified that your code returned the mock. Congratualations.
You have green lines, and you have learned nothing.

What you actually want to know is:

- Is the SQL correct?
- Does the RowMapper correctly map the result set columns to the object?
- Does it handle zero rows correctly?
- Does it handle multiple rows without exploding?

None of those questions are answered by a unit test. All of them are answered by an integration test against a real database.

If we can agree on these things:

- The primary goal of a testing suite is to avoid production issues
- Most production issues happen at your application boundaries
- Unit tests are bad at testing application boundaries

Then why would you make unit tests the foundation of your testing suite?

---

## The Whole Is Not the Sum of Its Parts

Unit tests are built on an assumption that if every individual piece works, the whole must work too.
But if you've spent any time debugging a production incident where every test was green, you already know that's not true.

When we write unit tests for a backend API, we're asking narrow questions. Does this service method return the right value?
Does this mapper transform this object correctly? Does this validator reject a null input?
These questions have their place. But they're not the ones that should be driving your API test strategy.

The question you should always come back to is: **does my API behave correctly?**

That's it. At the end of the day, your users don't care about the return value of `auditLogRepository.save(entry)`.
They send a request, and they expect a response that conforms to a contract. Everything else is an implementation detail.

This is where integration tests earn their keep. When you write a test that sends a real HTTP request and asserts on the response, you're testing behavior, and behavior is what you should be optimizing for.
You're not asking "does this function return X?" You're asking "does this API do what it promises?" Those are fundamentally different questions.

There's also a quiet bonus here: **by testing behavior, you're implicitly testing implementation.**
If your business logic is wrong, your behavior will be wrong, and your test will fail.
You haven't lost anything by moving up the stack. You've just stopped caring about *how* the right answer is produced, and started caring only that it *is* produced.
That's a more honest test.

---

## Integration Tests Don't Care How the Sausage Is Made

If the confidence argument hasn't sold you yet, here's a bonus that might.

We've already established that integration tests are implementation-agnostic: they test behavior, not code.
But there's a practical consequence of that which is worth spelling out: **your tests are completely decoupled from your codebase.**

And that decoupling has compounding benefits.

Change a method signature? Tests still pass. Restructure a class? Tests still pass. Swap out a library? Tests still pass. Rewrite it in Rust? Tests still pass.

That last one might raise eyebrows, but it's true.
You could rewrite your entire application in a different language, point the same integration test suite at it, and have reasonable confidence that you've got feature parity.
That's what it means to test behavior instead of implementation.

Unit tests can't make that claim. They're coupled to your implementation by design. Every refactor is a negotiation with your test suite.

Integration tests just get out of the way and let you build.

---

## But What About Speed?

This is the most common objection to integration-first testing, and the most valid, so let's address it head on.

Integration tests will always be slower than unit tests. But each one covers more ground, so you need fewer of them.
*Painfully* slow integration tests, though, are a design decision. 
The reason most integration test suites are slow is because they're stateful. They rely on a fixed set of database fixtures, 
they have to run sequentially to avoid stomping on each other's data, and they accumulate years of setup and teardown baggage that nobody wants to touch.
But it doesn't have to be that way.

A well-designed integration test suite is fixtureless. 
Each test creates the resources it needs via the API, programmatically extracts the IDs and state it needs from those responses, 
does its assertions, and leaves no trace. Every test is completely self-contained. And because no test depends on any other, you can run all of them in parallel.

That changes the math considerably. Thousands of integration tests, running in parallel against a real environment, can finish in minutes.

And while we're here: if your integration tests are slow because your endpoints are genuinely slow, that's not a testing problem.
That's a codebase problem that your integration tests just had the courtesy to surface.
A test suite that exposes real performance issues is doing its job.

---

## Unit Tests Still Have Their Place

None of this is to say that unit tests are useless.
There are places in a backend codebase where they shine: complex business logic, pure transformation functions, edge cases in validation rules.
If you have a method that takes inputs and produces outputs with no IO involved, a unit test is probably the right tool for that job.

The argument isn't *don't write unit tests.* The argument is *don't build your testing strategy around them.*

The testing pyramid tells you to make unit tests your foundation, to write them by the hundreds and treat integration tests as a luxury.
But for backend APIs (and maybe more), that priority is backwards. Your API's contract is what matters.
Your API's behavior at its boundaries is where things break.
Your API's ability to survive a refactor without dragging your test suite along for the ride is what makes a codebase sustainable.

Integration tests give you all of that. Unit tests fill in the gaps.

So by all means, write unit tests where they add value. But build your foundation on integration tests — on tests that send real requests,
assert on real responses, and tell you what your users actually care about.
