# Implementation Plan: VDR Assistant MVP

## Overview

Build a retrieval-first Virtual Data Room (VDR) assistant using:
- OpenAI Developer Platform
- OpenAI Vector Stores
- Streamlit UI
- ZIP-based VDR uploads

The system will:
- ingest ZIP-based VDR snapshots
- preserve folder hierarchy metadata
- upload supported files into OpenAI-managed storage
- index documents using OpenAI Vector Stores
- support grounded Q&A and document discovery
- return evidence-backed answers with citations and excerpts

The MVP is optimized for:
- trustworthiness
- evidence traceability
- retrieval precision
- operational simplicity

The system is intentionally NOT:
- a generalized chatbot
- an autonomous analyst
- a synthesis-heavy AI workflow

---

# Architecture

## High-Level Architecture

```text
[Streamlit UI]
    |
    | Upload ZIP / Ask Questions
    v
[Application Backend Layer]
    |
    |-- ZIP Extraction
    |-- File Validation
    |-- Metadata Extraction
    |-- OpenAI File Uploads
    |-- Vector Store Management
    |-- Retrieval Orchestration
    |-- Response Formatting
    |
    v
[OpenAI Developer Platform]
    |
    |-- Files API
    |-- Vector Stores
    |-- Responses API
```

---

# Core Architectural Decisions

## Decision: OpenAI-Only Retrieval Stack

Rationale:
- enterprise requirement
- no external infrastructure allowed
- simplifies compliance and security review

Implication:
- all retrieval logic built around OpenAI Vector Stores
- no Pinecone/Weaviate/Elastic/etc.

---

## Decision: Single Active VDR

Rationale:
- minimizes operational complexity
- avoids workspace isolation requirements
- reduces indexing and retrieval ambiguity

Implication:
- every new ZIP upload replaces previous VDR

---

## Decision: Retrieval-First UX

Rationale:
- aligns with diligence workflows
- minimizes hallucination risk
- improves trustworthiness

Implication:
- all responses require evidence grounding
- unsupported answers are refused

---

## Decision: Structured Responses

Rationale:
- improves reviewability
- improves analyst trust
- supports diligence workflows

Required structure:
- Answer
- Sources
- Excerpts
- Conflicting Information

---

## Decision: Metadata-Centric Retrieval

Metadata preserved for every file/chunk:
- folder path
- filename
- file type
- page/slide/sheet metadata
- upload timestamp
- document identifier

Purpose:
- filtering
- traceability
- better retrieval precision
- citation rendering

---

# Proposed System Components

## 1. Streamlit Frontend

Responsibilities:
- ZIP upload UI
- ingestion status display
- ingestion report display
- chat interface
- response rendering
- source link rendering
- optional folder filters

Key views:
- Upload page
- Processing status
- Chat interface
- Retrieval/source panel

---

## 2. Ingestion Service

Responsibilities:
- unpack ZIP
- recursively scan folders
- detect supported file types
- skip unsupported files
- preserve folder hierarchy metadata
- upload files to OpenAI Files API
- attach files to Vector Store
- generate ingestion report

Supported file types:
- PDF
- DOCX
- PPTX
- XLSX

Graceful degradation:
- continue ingestion after failures
- collect skipped-file metadata

---

## 3. Metadata Registry

Purpose:
Maintain lightweight local metadata mapping.

Example metadata:
- original file path
- OpenAI file ID
- filename
- file extension
- folder hierarchy
- upload status
- parse status

Potential storage:
- SQLite
- local JSON registry

Note:
This is NOT a retrieval database.
It is operational metadata only.

---

## 4. Retrieval Layer

Responsibilities:
- submit user queries
- retrieve grounded evidence from Vector Store
- enforce retrieval-only behavior
- gather citations/snippets
- detect conflicting evidence
- support folder-scoped filtering

Behavior requirements:
- no unsupported inference
- no external knowledge
- transparent evidence display

---

## 5. Response Formatting Layer

Responsibilities:
- normalize answer structure
- render citations consistently
- attach excerpts
- include metadata
- include conflicting information section

Required output:

```text
Answer

Sources

Excerpt

Conflicting Information
```

---

# Dependency Graph

```text
Environment Setup
    |
    +--> OpenAI Integration
    |       |
    |       +--> Vector Store Management
    |       |
    |       +--> Retrieval Layer
    |
    +--> ZIP Ingestion
            |
            +--> Metadata Registry
            |
            +--> File Upload Pipeline
                    |
                    +--> Streamlit Upload UI
                            |
                            +--> Chat Experience
                                    |
                                    +--> Structured Response Rendering
```

---

# Build Sequence

## Phase 1 — Foundation

Goal:
Establish environment, OpenAI connectivity, and ingestion skeleton.

---

## Task 1: Repository and Environment Setup

**Description:**
Create the base Python project structure, dependency management, environment configuration, and Streamlit application shell.

**Acceptance criteria:**
- [ ] Streamlit app launches locally
- [ ] Environment variables load correctly
- [ ] OpenAI API connectivity verified
- [ ] Basic project structure created

**Verification:**
- [ ] Run `streamlit run app.py`
- [ ] Verify OpenAI authentication succeeds

**Dependencies:** None

**Files likely touched:**
- `requirements.txt`
- `.env`
- `app.py`
- `src/config.py`

**Estimated scope:** Small

---

## Task 2: OpenAI Vector Store Integration

**Description:**
Implement OpenAI Vector Store creation, retrieval, deletion, and replacement lifecycle management.

**Acceptance criteria:**
- [ ] New vector store can be created
- [ ] Existing vector store can be replaced
- [ ] Vector store IDs persist locally
- [ ] Cleanup logic works

**Verification:**
- [ ] Create test vector store
- [ ] Replace vector store successfully

**Dependencies:** Task 1

**Files likely touched:**
- `src/openai/vector_store.py`
- `src/storage/registry.py`

**Estimated scope:** Medium

---

## Task 3: ZIP Upload and Extraction Pipeline

**Description:**
Implement ZIP upload handling, extraction, recursive file discovery, and supported file filtering.

**Acceptance criteria:**
- [ ] ZIP uploads accepted
- [ ] Nested folders extracted correctly
- [ ] Unsupported files skipped safely
- [ ] Folder hierarchy preserved

**Verification:**
- [ ] Upload representative VDR ZIP
- [ ] Validate extracted file tree

**Dependencies:** Task 1

**Files likely touched:**
- `src/ingestion/zip_handler.py`
- `src/ingestion/file_scanner.py`
- `app.py`

**Estimated scope:** Medium

---

## Checkpoint: Foundation

- [ ] Streamlit app operational
- [ ] OpenAI connectivity works
- [ ] ZIP extraction works
- [ ] Vector Store lifecycle operational

---

# Phase 2 — Ingestion and Indexing

Goal:
Enable complete VDR ingestion and searchable indexing.

---

## Task 4: Metadata Extraction and Registry

**Description:**
Implement metadata tracking for folder paths, filenames, file types, and ingestion state.

**Acceptance criteria:**
- [ ] Folder paths preserved
- [ ] Metadata stored locally
- [ ] File status tracking implemented
- [ ] Metadata retrievable for citations

**Verification:**
- [ ] Inspect metadata registry
- [ ] Validate paths and identifiers

**Dependencies:** Tasks 2-3

**Files likely touched:**
- `src/storage/registry.py`
- `src/models/metadata.py`

**Estimated scope:** Small

---

## Task 5: OpenAI File Upload Pipeline

**Description:**
Upload supported files to OpenAI Files API and attach them to the active Vector Store.

**Acceptance criteria:**
- [ ] Supported files upload successfully
- [ ] Files attach to vector store
- [ ] Failed uploads handled gracefully
- [ ] Upload progress visible

**Verification:**
- [ ] Validate uploaded files inside OpenAI platform
- [ ] Confirm retrieval-ready indexing

**Dependencies:** Tasks 2-4

**Files likely touched:**
- `src/openai/file_upload.py`
- `src/ingestion/pipeline.py`

**Estimated scope:** Medium

---

## Task 6: Ingestion Reporting

**Description:**
Generate transparent ingestion reports showing indexed, skipped, and failed files.

**Acceptance criteria:**
- [ ] Indexed file counts displayed
- [ ] Unsupported files listed
- [ ] Failed files listed
- [ ] Report visible in UI

**Verification:**
- [ ] Run ingestion against mixed ZIP
- [ ] Validate report accuracy

**Dependencies:** Tasks 3-5

**Files likely touched:**
- `src/reporting/ingestion_report.py`
- `app.py`

**Estimated scope:** Small

---

## Checkpoint: Ingestion Complete

- [ ] Full VDR ingestion works
- [ ] Files searchable in Vector Store
- [ ] Metadata preserved
- [ ] Ingestion reporting operational

---

# Phase 3 — Retrieval and Q&A

Goal:
Implement grounded retrieval workflows and evidence-backed responses.

---

## Task 7: Retrieval Query Layer

**Description:**
Implement query submission and evidence retrieval from OpenAI Vector Stores.

**Acceptance criteria:**
- [ ] User questions retrieve relevant evidence
- [ ] Retrieval supports document discovery
- [ ] Folder filters supported
- [ ] Unsupported-answer behavior implemented

**Verification:**
- [ ] Run representative diligence questions
- [ ] Validate evidence grounding

**Dependencies:** Tasks 2-5

**Files likely touched:**
- `src/retrieval/query_engine.py`
- `src/openai/responses.py`

**Estimated scope:** Medium

---

## Task 8: Citation and Excerpt Extraction

**Description:**
Implement rendering of page/slide/sheet metadata and supporting excerpts.

**Acceptance criteria:**
- [ ] PDF citations show pages
- [ ] PPTX citations show slide references
- [ ] XLSX citations show sheet names
- [ ] Excerpts visible in answers

**Verification:**
- [ ] Validate citations against source files
- [ ] Confirm excerpts are grounded

**Dependencies:** Task 7

**Files likely touched:**
- `src/retrieval/citations.py`
- `src/retrieval/excerpts.py`

**Estimated scope:** Medium

---

## Task 9: Conflict Presentation Logic

**Description:**
Implement handling and display of conflicting retrieved evidence.

**Acceptance criteria:**
- [ ] Multiple conflicting values displayed
- [ ] Sources attached to each value
- [ ] No automatic conflict resolution occurs

**Verification:**
- [ ] Create controlled conflicting test case
- [ ] Validate output formatting

**Dependencies:** Tasks 7-8

**Files likely touched:**
- `src/retrieval/conflicts.py`
- `src/ui/rendering.py`

**Estimated scope:** Small

---

## Checkpoint: Retrieval Functional

- [ ] Grounded Q&A operational
- [ ] Citations accurate
- [ ] Unsupported answers rejected
- [ ] Conflicts displayed transparently

---

# Phase 4 — UI and User Experience

Goal:
Deliver a usable diligence-oriented interface.

---

## Task 10: Streamlit Chat Experience

**Description:**
Implement conversational Q&A interface with lightweight session memory.

**Acceptance criteria:**
- [ ] Chat history visible
- [ ] Follow-up references supported
- [ ] Session context maintained
- [ ] Queries remain grounded

**Verification:**
- [ ] Run multi-step query workflow
- [ ] Validate retrieval grounding persists

**Dependencies:** Tasks 7-9

**Files likely touched:**
- `app.py`
- `src/ui/chat.py`

**Estimated scope:** Medium

---

## Task 11: Structured Response Rendering

**Description:**
Render all responses in rigid diligence-style structure.

**Acceptance criteria:**
- [ ] Answer section rendered
- [ ] Sources section rendered
- [ ] Excerpts section rendered
- [ ] Conflicting Information section rendered

**Verification:**
- [ ] Validate formatting consistency
- [ ] Test across multiple query types

**Dependencies:** Tasks 8-10

**Files likely touched:**
- `src/ui/rendering.py`
- `app.py`

**Estimated scope:** Small

---

## Task 12: Folder Filter UX

**Description:**
Implement optional retrieval scoping/filter controls.

**Acceptance criteria:**
- [ ] Users can select workstream filters
- [ ] Filters affect retrieval scope
- [ ] Default remains full-VDR search

**Verification:**
- [ ] Validate filtered retrieval behavior

**Dependencies:** Tasks 7-11

**Files likely touched:**
- `src/ui/filters.py`
- `src/retrieval/query_engine.py`

**Estimated scope:** Small

---

## Checkpoint: MVP Functional

- [ ] End-to-end workflow operational
- [ ] Upload → index → query flow works
- [ ] Structured responses consistent
- [ ] Retrieval quality acceptable

---

# Phase 5 — Hardening and Validation

Goal:
Improve reliability, trustworthiness, and operational robustness.

---

## Task 13: Error Handling and Graceful Degradation

**Description:**
Ensure ingestion and retrieval failures never break the application.

**Acceptance criteria:**
- [ ] Corrupted files skipped safely
- [ ] API failures handled gracefully
- [ ] Partial ingestion remains operational
- [ ] User-facing errors understandable

**Verification:**
- [ ] Test intentionally broken files
- [ ] Simulate API failures

**Dependencies:** Tasks 1-12

**Files likely touched:**
- multiple ingestion/retrieval modules

**Estimated scope:** Medium

---

## Task 14: Retrieval Quality Validation Suite

**Description:**
Create representative diligence test questions and validate retrieval correctness.

**Acceptance criteria:**
- [ ] Test dataset established
- [ ] Ground-truth questions documented
- [ ] Citation correctness validated
- [ ] Unsupported-answer behavior validated

**Verification:**
- [ ] Run evaluation workflow manually
- [ ] Validate precision and traceability

**Dependencies:** Tasks 7-13

**Files likely touched:**
- `tests/retrieval_tests.py`
- `tests/test_data/`

**Estimated scope:** Medium

---

## Final Checkpoint: MVP Release Candidate

- [ ] Full ingestion pipeline operational
- [ ] OpenAI Vector Store retrieval operational
- [ ] Grounded answers enforced
- [ ] Citations accurate
- [ ] Folder hierarchy preserved
- [ ] Unsupported files handled safely
- [ ] Structured diligence responses operational
- [ ] Representative diligence workflows validated

---

# Risks and Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| OpenAI retrieval citation limitations | High | Add local metadata mapping and response post-processing |
| Large ZIP ingestion latency | Medium | Background ingestion status UI and progress reporting |
| XLSX extraction quality limitations | High | Preserve contextual snippets and avoid spreadsheet reasoning |
| Unsupported file types in VDR | Medium | Graceful degradation and transparent reporting |
| Weak retrieval precision | High | Improve metadata preservation and prompt constraints |
| Hallucinated answers | High | Strict grounding prompts and unsupported-answer enforcement |
| Inconsistent citations | High | Normalize response formatting layer |

---

# Open Questions

- Which OpenAI model should be primary for retrieval orchestration?
- Should ingestion be synchronous or background-threaded in Streamlit?
- What local metadata persistence format is preferred?
- What direct-linking mechanism will exist for local source files?
- Should original extracted VDR files remain locally cached after upload?

---

# Recommended Initial Tech Stack

## Python
- Python 3.11+

## Frontend
- Streamlit

## OpenAI SDK
- Official OpenAI Python SDK

## Storage
- Local filesystem
- SQLite or lightweight JSON metadata registry

## Parsing Libraries
Potential libraries:
- pypdf
- python-docx
- python-pptx
- openpyxl
- pandas

---

# Recommended MVP Success Criteria

The MVP is successful if users can:

1. Upload a ZIP-based VDR snapshot
2. Ingest documents without pipeline failure
3. Ask grounded diligence questions
4. Receive trustworthy evidence-backed answers
5. Trace answers back to exact source locations
6. Discover relevant documents quickly
7. Identify conflicting evidence transparently
8. Trust that unsupported answers are refused
