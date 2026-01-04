---
layout: post
title:  "From Natural Language to Ubiquitous Language — Reducing Ambiguity in Specifications with LLMs"
date:   2026-01-04 10:00:00 +0200
categories: Software Engineering, AI, Spec-Driven Development
---
Yesterday, I read Marc Brooker's blog post on Natural Language Programming, which I highly recommend to anyone wanting to understand how programming and software development are currently evolving. One analogy Marc uses to describe the future of programming particularly stuck with me: With a spec-driven development approach, it becomes possible to actively include computers in the discussion about the design of a software system.

In this view, LLMs are no longer just code generators. They become participants in a discussion about how a system should behave. Spec-driven development is thus less of a new development methodology and more of a technological extension of existing human workflows.

Successful software systems are rarely created by someone immediately typing perfect code into an editor. They emerge from communication—through specifications, user stories, and shared discussions at the whiteboard. The formal implementation in code is often just the last step of a long, creative process.

Marc Brooker rightly emphasizes that success comes from iteration, context accumulation, and collaboration. What is new is that we can now use technology to let computers participate in the very same conversations humans already have when describing how a software system should behave. We position LLMs here as active conversation partners and critical reviewers, not as autonomous authors who blindly take work off our hands.

But as soon as we make natural language the primary tool for specification, we encounter a central problem: Conversations in natural language are inherently ambiguous.

The Emergence of a Ubiquitous Language

When humans discuss a problem together, ambiguous terms must be clarified to enable effective communication within the current domain. We all know this from meetings: Someone says "customer" but means "account." Another says "order" but refers only to the "shopping cart."

In my experience, clarification happens in an iterative process: If a term is used for the first time and interpreted differently by different conversation partners, a need arises to explicitly define its meaning for the ongoing discussion. Through this step-by-step definition of terms for a concrete problem area, a common language emerges that is used by all participants.

In Domain-Driven Design (DDD), this common language is called Ubiquitous Language. It applies within a delimited area, the so-called Bounded Context.

Handling the ambiguity of natural language was reserved exclusively for humans for a long time. However, with the advent of LLMs and spec-driven development, it becomes possible to include machines in this clarification process as well. LLMs can help development teams detect such ambiguities early on by making them explicit and clarifying them within the respective Bounded Context. They act like a new team member who unreservedly asks: "When you say 'user' here, do you mean the logged-in administrator or the end customer?"

Collaborative Clarification as Engineering Work

This leads to a new interaction loop in software development, which I view as genuine engineering work and not administrative overhead:

Starting Point: The human creates a specification or description in natural language.

Detection: The LLM scans the text and makes ambiguities visible. It identifies terms that allow for multiple plausible interpretations in the current context.

Clarification: The human clarifies the intention.

The LLM uses this now stable terminology as semantic anchors. Once a definition is set, any deviation or inconsistency in later documents becomes detectable. It is important to understand here: The language exists in the context of the work, not in a separate, dusty glossary document that no one reads. The definitions live where the work is being done.

Practical Application with a Custom Kiro Power

To translate this theoretical approach into practice and reduce ambiguity across all artifacts, I have developed my own Kiro Power.

This Power is specifically designed to work with Kiro in Spec-Mode. It takes effect where the actual work happens: in specification documents, design drafts, and backlog items.

The Kiro Power encapsulates three essential elements:

Steering Instructions with a clear focus on ambiguity detection (instead of pure text generation).

Context on previously clarified domain terms.

Activation Logic, so that it is deployed specifically only during spec-driven work.

In the active state, the Power supports Kiro in recognizing ambiguous or overloaded terms and uncovering inconsistent terminology across different documents. It proactively prompts the human to clarify the intention before the ambiguity propagates into the implementation.

In doing so, the Power does not define a rigid formal language or grammar that would hinder the creative flow. Instead, it helps maintain a consistent Ubiquitous Language across all spec-driven artifacts. The result is that humans and LLMs work together on clarification, while domain semantics remain stable over time.

From Ubiquitous Language to Implementation

Why is this effort before the first line of code so important? Because a defined meaning massively constrains implementation decisions—in a positive sense.

When it is clearly defined what a term means and what it does not, many wrong turns in architecture fall away. Precise language supports:

Refactoring: We know exactly which concepts belong together.

Platform-specific Specialization: The model can generate more precise code for specific frameworks because the functional requirement is unambiguous.

Long-term Maintainability: The code reflects the language of the domain.

Conclusion: Precision before Code

The greatest leverage in modern software development with AI lies not in accelerating typing, but in the phase before implementation. Reducing ambiguity is a design activity and the mandatory prerequisite for safe automation.

LLMs are amplifiers. If we feed in unspecific, ambiguous instructions, they amplify the chaos. However, if we use them to create clarity and precision, they amplify the quality of our software architecture. Spec-driven development ultimately means: We use the machine to force ourselves to think more precisely.