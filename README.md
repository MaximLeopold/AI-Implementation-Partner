# AI Implementation Partner

A skill-based custom agent framework for moving AI ideas from early use-case exploration to implementation planning, incremental building, and project handoff.

The framework is designed for applied AI implementation in business, strategy, finance, M&A, due diligence, Virtual Data Room (VDR), market intelligence, productivity, and workflow automation contexts.

# Attribution

Parts of this project are adapted from Matt Pocock’s mattpocock/skills and Addy Osmani's AddyOsmani/agent-skills public repositories which are licensed under the MIT License. In particular, this project builds on the skill-based workflow concept and adapts selected skills such as grill-me for AI implementation workflows.

This repository extends those ideas into an AI implementation partner framework focused on business use-case exploration, MVP planning, virtual data-room assistance, market intelligence, custom agent workflows, and project handoff.

Copyright (c) 2026 Matt Pocock

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions: 
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

Copyright (c) 2025 Addy Osmani

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

## Overview

AI Implementation Partner is not a single chatbot prompt. It is a structured agent operating model supported by reusable skills, workflow documentation, and example implementation artifacts.

The framework helps users avoid jumping too quickly from an idea into technology choices. Instead, it guides work through a staged process:

```text
New Idea
   ↓
Grill Me
   ↓
Shared Understanding
   ↓
Planning
   ↓
Build
   ↓
Handoff
```

The goal is to create a disciplined path from business problem to AI-enabled MVP.

## What This Repository Contains

```text
AI-Implementation-Partner/
├── README.md
├── agent/
│   └── AGENT_INSTRUCTIONS.md
├── docs/
│   ├── design-principles.md
│   └── workflow.md
├── examples/
│   ├── VDR-assistant-handoff
│   └── vdr_mvp_implementation_plan.md
└── skills/
    ├── grill-me/
    │   └── SKILL.md
    ├── handoff/
    │   └── SKILL.md
    ├── planning-and-task-breakdown/
    │   └── SKILL.md
    └── tdd/
        └── SKILL.md
```

## Core Concept

The framework is built around a simple principle:

> Shared understanding before planning. Planning before building. Handoff before context loss.

The agent is instructed to act as an AI ideation and implementation advisor combining elements of a management consultant, M&A advisor, AI strategist, product manager, data scientist, software engineer, and solution architect.

It is especially focused on practical enterprise AI implementation, where business value, governance, simplicity, maintainability, and user adoption matter more than technical novelty.

## Workflow

### 1. New Idea

A user introduces an AI use case, workflow improvement, product concept, or implementation idea.

The agent does not immediately produce an implementation plan. Instead, it begins by clarifying the idea.

### 2. Grill Me

The first active skill is `grill-me`.

This skill interviews the user one question at a time to clarify the problem, target user, workflow, business value, constraints, and success criteria.

The purpose is to prevent premature architecture decisions and ensure the project is grounded in a real user need.

### 3. Shared Understanding

Once the important design decisions have been explored, the agent produces a concise recap covering:

- Project objective
- Target users
- Business value
- Key requirements
- Design decisions made
- Open assumptions
- Constraints
- Risks
- Success criteria

Only after this does the agent ask whether the user wants to move into planning mode.

### 4. Planning

The `planning-and-task-breakdown` skill turns a clear specification into ordered implementation tasks.

It focuses on:

- Dependency mapping
- Vertical task slicing
- Acceptance criteria
- Verification steps
- Scope estimation
- Risk identification
- Checkpoints between phases

The planning skill explicitly discourages building before the implementation sequence is understood.

### 5. Build

The `tdd` skill supports incremental implementation using a red-green-refactor loop.

It emphasizes:

- One behavior at a time
- Tests through public interfaces
- Minimal implementation to pass each test
- Refactoring only after tests are green
- Avoiding implementation-coupled tests

### 6. Handoff

The `handoff` skill creates a project continuation document so another agent, LLM session, engineer, or future user can continue the work without rereading the full conversation.

A handoff includes:

- Project objective
- Current state
- Decisions made
- Key requirements
- Open questions
- Recommended next step
- Suggested skills
- Relevant artifacts
- Topics not to revisit

## Skills

### `grill-me`

A structured questioning skill for refining new ideas before implementation.

Use it when a user wants to explore an AI use case, product concept, workflow improvement, architecture idea, or implementation plan.

### `planning-and-task-breakdown`

A planning skill for converting clear requirements into implementable tasks.

It is designed to make work executable by humans, AI agents, or future sessions while avoiding oversized, ambiguous tasks.

### `tdd`

A test-driven development skill for building incrementally.

It uses the red-green-refactor loop and focuses on behavior-driven tests through public interfaces rather than implementation details.

### `handoff`

A project-transfer skill for preserving context across sessions, agents, or collaborators.

It is designed to prevent context loss and make project continuation easier.

## Example: Virtual Data Room Assistant MVP

The repository includes an example VDR Assistant MVP, focused on retrieval-first due diligence workflows.

The example defines an assistant that allows users to ask questions against uploaded VDR documents and receive grounded answers with supporting excerpts and source references.

The VDR assistant is intentionally designed not to act as an autonomous analyst or speculative deal advisor. Its purpose is evidence retrieval, document discovery, and traceable Q&A.

The example implementation plan includes:

- OpenAI-only retrieval stack
- OpenAI Vector Stores
- Streamlit frontend
- ZIP-based VDR uploads
- Single active VDR replacement model
- Metadata-preserving ingestion
- Grounded Q&A
- Structured answers with citations and excerpts

## Example AI Implementation Areas

This framework can be adapted to support MVPs and AI implementations such as:

- Virtual Data Room assistants
- Market intelligence systems
- Investment research workflows
- M&A due diligence tools
- Knowledge management agents
- Custom research agents
- Workflow automation assistants
- Internal productivity tools
- Retrieval-augmented generation prototypes
- Agentic business process support systems

## Design Principles

The repository follows four practical design principles:

1. Ask before advising  
2. Shared understanding before planning  
3. Planning before building  
4. Handoff before context loss  

These principles are intended to make AI implementation more deliberate, user-centered, and executable.

## Current Status

This is an early-stage framework repository. It currently contains:

- Agent instructions
- Workflow documentation
- Four reusable skills
- A VDR assistant handoff example
- A VDR MVP implementation plan

It does not yet contain a fully packaged application or production-ready software system.

## Potential Next Steps

Possible extensions include:

- Add example conversations for each workflow stage
- Add a `/templates` folder for use-case briefs, planning documents, and handoff documents
- Add a simple Streamlit demo for the VDR assistant workflow
- Add sample prompts for market intelligence and custom agent workflows
- Add implementation examples for OpenAI API, vector stores, and retrieval workflows
- Add a visual architecture diagram for the VDR MVP
- Add setup instructions once executable code is included

## Disclaimer

This project is for educational, experimental, and prototyping purposes. It does not provide legal, tax, regulatory, investment, or financial advice. Outputs from any AI implementation should be reviewed by qualified professionals before being used in real business decisions.
