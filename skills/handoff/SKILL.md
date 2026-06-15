--

name: handoff
description: Create a project handoff document so another agent, LLM, engineer, or future session can continue the work without rereading the entire conversation.
argument-hint: "What will the next session focus on?"
-----------------------------------------------------

Create a project handoff document for a fresh agent, engineer, or future session.

The objective is not to summarize the conversation.

The objective is to transfer project state and preserve context.

The handoff should allow the next session to continue the work immediately without revisiting previously resolved discussions.

If the user provided arguments, treat them as the intended focus of the next session and tailor the handoff accordingly.

Do not duplicate content already captured in existing artifacts such as:

* PRDs
* Architecture documents
* ADRs
* Plans
* Specifications
* Issues
* Commits
* Diffs

Reference those artifacts by path, URL, or identifier instead.

Redact sensitive information including:

* API keys
* Passwords
* Secrets
* Personally identifiable information

Structure the handoff document as follows:

# Project Objective

Brief description of what is being built or achieved.

# Current State

Describe the current phase of the project.

Examples:

* Idea exploration
* Requirements definition
* Solution design
* Planning
* Implementation
* Testing
* Deployment

# Decisions Made

List decisions that have already been made and should not be revisited unless new information emerges.

# Key Requirements

List agreed requirements, constraints, assumptions, and success criteria.

# Open Questions

List unresolved questions, decisions, dependencies, and risks.

# Recommended Next Step

Describe the single most useful next action.

# Suggested Skills

Recommend which skills should be used next and why.

# Relevant Artifacts

Reference existing documents, repositories, plans, designs, issues, or URLs.

# Do Not Revisit

List topics, debates, decisions, and conclusions that have already been resolved.

The handoff should be concise, actionable, and optimized for project continuation rather than conversation summarization.

