---
layout: post
title:  "How Creating a Ubiquitous Language Ensures AI Builds What You Actually Want"
date:   2026-01-04 10:00:00 +0200
categories: Software Engineering, AI, Spec-Driven Development
---
Recently, I read [Marc Brooker's blog post on Natural Language Programming](https://brooker.co.za/blog/2025/12/16/natural-language.html), which I highly recommend to anyone wanting to understand how programming and software development are currently evolving. One analogy Marc uses to describe the future of programming particularly stuck with me: With a spec-driven development approach, it becomes possible to actively include computers in the discussion about the design of a software system.

In this view, LLMs are no longer just code generators. They become participants in a discussion about how a system should behave. Spec-driven development is thus less of a new development methodology and more of a technological extension of existing human workflows.

Successful software systems are rarely created by someone immediately typing perfect code into an editor. They emerge from communication—through specifications, user stories, and shared discussions at the whiteboard. The formal implementation in code is often just the last step of a long, creative process.

Marc Brooker rightly emphasizes that success comes from iteration, context accumulation, and collaboration. What is new is that we can now use technology to let computers participate in the very same conversations humans already have when describing how a software system should behave. We position LLMs here as active conversation partners and critical reviewers, not as autonomous authors who blindly take work off our hands.

But as soon as we make natural language the primary tool for specification, we encounter a central problem: Conversations in natural language are inherently ambiguous.

## The Emergence of a Ubiquitous Language

When humans discuss a problem together, ambiguous terms must be clarified to enable effective communication within the current domain. We all know this from meetings: Someone says "customer" but means "account." Another says "order" but refers only to the "shopping cart."

In my experience, clarification happens in an iterative process: If a term is used for the first time and interpreted differently by different conversation partners, a need arises to explicitly define its meaning for the ongoing discussion. Through this step-by-step definition of terms for a concrete problem area, a common language emerges that is used by all participants.

In Domain-Driven Design (DDD), this common language is called Ubiquitous Language. It applies within a delimited area, the so-called Bounded Context. A bounded context in DDD is a boundary within which a specific domain model and ubiquitous language apply consistently. For example, in an e-commerce system, an Order Management bounded context defines what an Order means and enforces its lifecycle rules, while Payment and Shipping use different models and terminology for the same real-world concept. 

Handling the ambiguity of natural language was long reserved exclusively for humans. With the advent of LLMs and spec-driven development, machines can now participate in this clarification process by making ambiguities explicit within a given Bounded Context, asking in the Order Management context whether the term “order” refers to a customer commitment at checkout or to a payment or shipping artifact used in downstream contexts.

## Collaborative Clarification as Engineering Work

This leads to a new interaction loop in software development that I consider genuine engineering work rather than administrative overhead. With tools like Kiro, work on a new feature often starts with a simple prompt that captures the initial idea and intent in natural language, which Kiro then elaborates into a specification.

From there, ambiguities surface quickly. The LLM supports the team by highlighting terms that can be interpreted in more than one way in the current bounded context, such as whether an “order” refers to a customer commitment in Order Management or to a payment or shipping concept elsewhere. The human clarifies the intent, and once that clarification is made, the terminology becomes stable. These agreed-upon terms act as anchors in all subsequent artifacts, making inconsistencies visible as soon as they appear.

## Practical Application with a Custom Kiro Power

To translate this theoretical approach into practice, I developed the [**Spec Ambiguity Resolver**](https://github.com/schleidl/spec-ambiguity-resolver-kiro-power) - a Kiro Power that helps establish and maintain a ubiquitous language throughout the entire development lifecycle.

The power is active during all phases of spec-driven development: specification writing, design document creation, task breakdown, and implementation. It works by maintaining a living glossary file (`domain-terms.md`) that serves as the single source of truth for the project's ubiquitous language.

Let's return to our Order Management example to see how this works in practice. When the team starts writing their specification and uses the term "order," the power immediately flags this as potentially ambiguous:

> "I noticed the term 'order' appears in the specification. To establish our ubiquitous language, we should define what 'order' means in this project context so all participants - including me as the coding AI - share the same understanding.
>
> Based on the context, I propose this definition:
>
> **Order**: A customer commitment created at checkout in the Order Management bounded context, including items, quantities, pricing, and delivery address. This represents the customer's intent to purchase.
>
> Does this definition accurately capture what 'order' means in your project? Would you like to adjust it before I add it to our ubiquitous language?"

This interaction is crucial: the power never autonomously creates definitions. It proposes them based on context and waits for explicit human approval. This ensures the ubiquitous language represents genuine semantic agreement, not just AI-generated documentation.

Once approved, the definition is added to `domain-terms.md`. From that point forward, the power ensures consistency: if someone later uses "order" to mean something different (like a payment record or shipping manifest), it flags the inconsistency and prompts clarification. Should the team discover they need separate concepts, they can establish distinct terms like "Customer Order" and "Fulfillment Order," each with its own precise definition within its bounded context.

The power doesn't impose a rigid formal language. Instead, it helps the team build their own shared vocabulary organically as they work, making ambiguities visible before they propagate into code. The result is that humans and AI collaborate on clarification, while domain semantics remain stable and traceable from requirements through implementation.

## From Ubiquitous Language to Implementation

Why invest this effort in establishing a ubiquitous language before writing the first line of code? Because precise semantic agreement fundamentally constrains implementation decisions - in a positive sense.

When the entire team - including AI coding agents - shares an explicit understanding of what "order" means in the Order Management context, entire classes of architectural mistakes become impossible. The AI agent implementing the checkout flow knows exactly what data structure to create, what lifecycle states are valid, and which operations belong to this concept versus others like payment processing or shipping.

This semantic precision enables several critical capabilities:

**Refactoring with Confidence**: When "order" has a precise definition in `domain-terms.md`, we know exactly which code belongs together. Refactoring becomes safer because the boundaries between concepts are explicit. We can confidently move order validation logic into the Order class, knowing it won't accidentally include payment processing concerns.

**AI-Generated Code Quality**: When an AI coding agent reads the ubiquitous language before implementation, it generates code that accurately reflects the domain model. Instead of generic class names like `DataProcessor` or `RequestHandler`, it creates `CustomerOrder` with methods like `confirmPurchaseIntent()` that mirror the team's shared vocabulary.

**Long-term Maintainability**: Code that speaks the domain language remains comprehensible as the team evolves. A developer joining the project six months later can read `domain-terms.md`, understand the ubiquitous language, and immediately recognize those same terms throughout the codebase. The semantic agreement established during specification persists into the implementation.

## Conclusion: Precision Before Code

The greatest leverage in modern software development with AI lies not in accelerating typing, but in establishing semantic agreement before implementation begins. Kiro has already taken a giant leap in this direction with its spec-driven development workflow, which structures the development process around creating clear specifications, design documents, and task lists before any code is written. This blog post explored how we can extend this foundation by establishing a ubiquitous language - a shared vocabulary that ensures all project participants, including AI coding agents, share the same understanding of domain terms throughout the entire development lifecycle. Reducing ambiguity through a ubiquitous language is genuine design work - the mandatory prerequisite for safe automation.

LLMs are amplifiers. When we give an AI agent ambiguous instructions where "order" could mean a dozen different things, it amplifies the chaos by generating code that reflects our confusion. But when we first establish that "order" means precisely "a customer commitment in the Order Management context," the AI amplifies our clarity by generating code that accurately implements this shared understanding.

Spec-driven development with tools like the Spec Ambiguity Resolver ultimately means using the machine as a partner in thinking more precisely. The AI doesn't just execute our specifications - it participates in refining them by making ambiguities visible, proposing definitions for our review, and maintaining consistency across the entire project lifecycle.

The result is software where the code speaks the same language as the specifications, where AI agents and human developers share the same semantic foundation, and where the ubiquitous language established in early conversations persists all the way through to implementation. This is how we move from natural language programming to truly collaborative software development.