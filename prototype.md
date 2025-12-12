# A.U.R.A. — Core Prototype Specification
**Artificial Ubiquitous Reality Analysis**  
**Prototype v0.1 — End-to-End MVP**

---

## 1. Purpose of This Document

This document defines the **scope, goals, and functional requirements** of the first working prototype of **A.U.R.A.** (Artificial Ubiquitous Reality Analysis).

The prototype is intended to:
- validate the core methodological assumptions of A.U.R.A.;
- demonstrate an end-to-end execution flow;
- serve as a foundation for future iterations, not as a final architecture.

This document is intentionally concise and implementation-oriented.

---

## 2. Context and Motivation

A.U.R.A. investigates how **Large Language Models (LLMs)** interpret, infer, and construct “reality” from **non-linear, fragmented, or opaque texts**, with a particular focus on **Stream of Consciousness (SoC)** narratives.

The prototype acts as a **controlled laboratory** where:
- narrative input is treated as experimental data;
- LLMs are treated as cognitive inference engines;
- outputs are captured as structured, reproducible artifacts.

---

## 3. Prototype Goals (What This Version Must Do)

The prototype MUST:

1. Accept a narrative text as input.
2. Accept an experiment definition via configuration (YAML).
3. Execute a minimal analytical pipeline.
4. Call an LLM (or a mock substitute).
5. Produce structured, inspectable outputs.
6. Be runnable locally via CLI.
7. Be reproducible and deterministic where possible.

The prototype does NOT aim to:
- provide a UI;
- optimize performance;
- finalize scientific metrics;
- lock architectural decisions prematurely.

---

## 4. Conceptual Model

### 4.1 Narrative Modes

The prototype supports two conceptual modes:

- **Structured Stream of Consciousness**
  - An underlying narrative may exist but is hidden.
  - The system attempts to reconstruct latent structure.

- **Unstructured Stream of Consciousness**
  - No underlying narrative is assumed.
  - The system observes whether and how structure is imposed.

These modes influence prompts and interpretation, not pipeline mechanics.

---

## 5. High-Level Architecture

The prototype consists of:

- A Python CLI (`aura`)
- A configuration-driven execution engine
- A pluggable LLM abstraction layer
- A minimal analytical pipeline
- A structured output system

All execution is **user-controlled**:
- model choice
- token limits
- cost constraints
- analysis depth

---

## 6. Epics and User Stories

### EPIC 1 — Project Scaffolding & Execution

**Goal:** Provide a runnable, installable core engine.

- **US1.1**: CLI entrypoint (`aura run <config.yaml>`)
- **US1.2**: Standard folder and module structure
- **US1.3**: Installable Python package (editable mode)

---

### EPIC 2 — Experiment Configuration

**Goal:** Enable declarative experiment definition.

- **US2.1**: YAML-based experiment manifest
- **US2.2**: Load narrative text from file
- **US2.3**: Support structured vs unstructured SoC mode flag

---

### EPIC 3 — LLM Orchestration

**Goal:** Abstract LLM access and control costs.

- **US3.1**: Provider-agnostic LLM interface
- **US3.2**: API keys supplied via environment variables
- **US3.3**: Token budget tracking and enforcement
- **US3.4**: Mock provider for zero-cost development

---

### EPIC 4 — Analytical Pipeline

**Goal:** Produce layered analytical output.

- **US4.1**: Surface analysis layer
  - counts, fragmentation heuristics, basic metrics
- **US4.2**: Semantic analysis layer
  - themes, clusters, motifs (LLM-driven)
- **US4.3**: Inference layer
  - entities, implied events, causal assumptions

Each layer produces **structured JSON output**.

---

### EPIC 5 — Experiment Orchestration & Output

**Goal:** Ensure reproducibility and traceability.

- **US5.1**: Execute selected layers sequentially
- **US5.2**: Persist outputs in a structured directory
- **US5.3**: Generate experiment metadata
- **US5.4**: Generate a human-readable experiment summary

---

### EPIC 6 — Automation (Deferred)

**Goal:** Enable CI-based execution.

- **US6.1**: GitHub Action to run experiments
- **US6.2**: Secure handling of secrets in CI

(This epic is intentionally deferred until the prototype stabilizes.)

---

## 7. Definition of “Prototype Complete”

The prototype is considered complete when:

- A user can run:
  ```bash
  aura run experiment.yaml
  ````
* A narrative is loaded and analyzed.
* At least one analytical layer runs end-to-end.
* Outputs are written to disk.
* No API calls are required if mock mode is enabled.
* The execution is documented and reproducible.

---

## 8. Expected Outcomes

By completing this prototype, we expect to:

* Validate the feasibility of A.U.R.A.’s methodology.
* Observe initial LLM behavior under SoC constraints.
* Identify architectural pressure points early.
* Establish a concrete base for scientific discussion.

---

## 9. Next Steps After Prototype

After the prototype:

* refine prompts and layers;
* formalize metrics;
* expand multi-model comparison;
* stabilize `aura-core`;
* introduce `aura-pipeline`.

---

**Status:**
This document reflects the agreed-upon scope and direction as of Prototype v0.1.
