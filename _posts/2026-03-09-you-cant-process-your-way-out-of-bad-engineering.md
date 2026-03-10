---
layout: post
title: "You Can't Process Your Way Out of Bad Engineering"
---

Many engineering organizations have a very predictable response to failure.

A risky deployment causes an outage. A poorly designed feature requires a costly rewrite. A bug reaches production that clearly should have been caught earlier. Leadership looks for a systemic fix.

And the most obvious lever available is process.

So new process appears. Mandatory design documents. Formal behavior specifications. Architecture review boards. Planning artifacts that must exist before anyone writes a line of code.

The logic seems reasonable. If engineers are making mistakes, stricter process should prevent those mistakes.
Sometimes it succeeds in minimizing damage, but it rarely solves the real problem. And there is a hidden cost that most engineering leads don't consider.

---

## The lowest-common-denominator trap

When organizations introduce new process, it is almost always motivated by a specific failure, or pattern of failures.

Someone ships something poorly designed, so design documents become mandatory. 
A feature causes an incident, so architecture reviews become required. 
A team delivers something brittle, so leadership introduces more planning steps before implementation begins.

The intent is to prevent those mistakes from happening again, but once introduced, these rules apply to everyone.
Your strongest engineers now operate under constraints created to control your weakest ones. 
Engineers who were already careful and disciplined spend more time satisfying procedural requirements instead of solving problems. 
Meanwhile the engineers who struggled before are still struggling.

Process can limit certain mistakes. It cannot create judgment. Good engineers become slower. Bad engineers remain bad.
The organization feels safer, but the underlying quality problem is still there.

At this point it's worth clarifying something. I'm not here to prescribe why an organization might end up with weak engineering outcomes.

Maybe the hiring bar is too low.
Maybe there isn’t enough mentorship for engineers who show potential.
Maybe compensation makes it difficult to retain strong engineers.
Maybe the engineering culture doesn’t reward good technical judgment.

Maybe all of the above?

Whatever the root cause, the result is the same: process is introduced in an attempt to compensate for a problem it cannot actually solve.

---

## When planning becomes theater

This dynamic shows up most clearly in the process that happens *before* engineers even start writing code.

After a few painful incidents, leadership concludes that engineers need to “think things through more carefully.” 
The solution is to require more planning artifacts. 
Suddenly every change requires a design document. Every feature must be accompanied by a structured specification describing expected behavior.

In theory, these tools can be useful. Thoughtful design discussions are healthy. Clear problem definitions prevent wasted work. 
But the content, format, and extent should be up to the engineering team taking on the task.
They're the engineers who read and understand the requirements. They're the engineers who have to implement it. 
They should be trusted to handle managing the implementation in whatever way they see fruitful.

But unfortunately in many organizations, "design discussions" quietly turn into something else: documentation theater.

The goal shifts from **thinking clearly about a problem** to **producing an artifact that satisfies a process requirement**.
You start seeing design documents for trivial changes. Engineers spend hours filling out templates with sections like “Motivation,” “Alternatives Considered,” and “Risk Analysis” for work that could have been explained in a short conversation between two engineers.

The document exists because the process requires it, not because the work demands it.

---

## Two examples engineers will recognize

Consider a simple change: adding a new field to an existing API response.

The work itself is straightforward. Update the schema, add some backend code, and deploy. It might take a couple of hours.

But before implementation can begin, the engineer must produce a six-page design document explaining the motivation, evaluating alternatives, describing the rollout strategy, and documenting potential risks.

The document circulates for comments. A few engineers leave minor suggestions. A week later the change is approved.

The implementation takes two hours.

Nothing about that document improved the quality of the design. The artifact exists because the process says design documents must exist.

Another common example is mandatory behavior specifications.

In theory, behavior-driven development helps clarify expected system behavior before implementation begins. In practice, some organizations require engineers to produce extensive “Given / When / Then” scenarios for every feature regardless of complexity.

An engineer spends an afternoon writing structured specifications describing how a simple endpoint should behave. Everyone involved already understands the behavior. The code itself will be trivial.

The exercise produces a document.

It does not produce better engineering.

---

## Engineers design systems and processes for a living

There is also a deeper irony in organizations that rely heavily on top-down process to control engineering behavior.

Designing reliable systems under constraints is exactly what engineers are paid to do.

Engineers build distributed systems, data pipelines, deployment infrastructure, and operational tooling. They spend their careers designing processes that produce correct outcomes in complex environments. Figuring out efficient ways to solve technical problems is the job.

If a team of engineers cannot design a process that works for the systems they own, the absence of a centralized rulebook is unlikely to be the real issue.

More often, the problem is that the team itself lacks the engineering maturity required to solve it.

---

## Process is sometimes necessary

None of this means engineering organizations should operate without process.

Large organizations require coordination. Compliance frameworks such as SOC2 introduce legitimate requirements around change management and auditing. 
Companies operating in regulated environments need safeguards around how software is developed and deployed. Some structure is unavoidable as systems and teams grow.

The problem is not the existence of process. The problem is introducing process without acknowledging the tradeoffs.
Every new rule slows down engineers who were already operating responsibly. 
Every additional approval step adds friction to work that might not have required it. 
Process designed to guard against the lowest common denominator inevitably affects everyone.

Good engineering leaders recognize these tradeoffs and introduce process carefully rather than reflexively.

Before adding a new rule, it is worth asking a simple question:
Is this improving the organization as a whole, or is it just protecting us from our weakest engineers?

If the answer is the latter, the real problem likely lies somewhere else.

---

## The real responsibility of leadership

Strong engineering organizations are built on good engineers, clear ownership, and trust in technical judgment.

Teams should own the systems they build. Engineers closest to a system should be responsible for deciding how that system is designed, tested, and deployed.
Process should support that autonomy, not replace it.

Engineering leadership is responsible for building teams capable of delivering high-quality systems. That means hiring carefully, developing engineers who show potential, and holding teams accountable for outcomes.

It does not mean firing people when they make a mistake. We all make mistakes. I’ve pushed bugs to production. 
I’ve shipped features that, in hindsight, could have been implemented better.
Mistakes are part of the process. Every mistake is a learning opportunity. 
Behind the wisest, most experienced principal engineers are countless hours spent debugging production issues they caused and hundreds of “did I really write that?” moments.

However, it does mean teams should be held accountable for the long-term outcomes of their products, the bar for what is “good” should be high, and a pattern of repeating the same mistakes should be addressed.

Process can help coordinate work and satisfy compliance requirements.

It cannot replace engineering judgment.

And it certainly cannot replace good engineers.

You cannot process your way out of bad engineering.
