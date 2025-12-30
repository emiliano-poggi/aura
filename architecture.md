# AURA – Architecture v1.0.0

This document defines the **architecture** of the **Artificial Ubiquitous Reality Analysis (AURA)** project.
It translates the [AURA vision v1.*](vision.md) into a coherent set of structural decisions, execution boundaries, and conceptual contracts that guide implementation while deliberately avoiding premature formalization.

The architecture is **procedural rather than agentic**, **explicit rather than implicit**, and **epistemically conservative by design**.

---

## 1. Ecosystem (Repository Topology)

AURA is structured as a **multi-repository ecosystem**.
Each repository embodies a distinct epistemic and operational role. This separation is a **core architectural principle**, intended to preserve conceptual clarity, responsibility boundaries, and licensing intent.

---

### `aura` — Research Hub (Conceptual Authority)

This repository serves as the **conceptual anchor** of the project.

* Contains the vision document, conceptual framework, methodological principles, and research notes that define the scope and intent of AURA.
* Contains **no executable code**, runtime dependencies, or operational logic.
* Establishes the vocabulary, assumptions, and epistemic constraints that inform all other components.
* Licensed under **CC BY-NC-SA 4.0** to promote academic sharing while preventing unrestricted commercial reuse.

This repository is **authoritative but inert**: it constrains interpretation without performing it.

---

### `aura-core` — Execution Engine (Interpretive Instrument)

This repository implements the **operational heart** of AURA.

* Provides the only authorized mechanism for performing interpretation.
* Exposes a CLI and/or library interface through which experiments are executed.
* Implements cognitive engine adapters, including cloud-based, local, and mock engines.
* Hosts analysis units, interpretive protocols, execution tracing, and cost enforcement.
* Supports simulation and mock execution for development and pedagogical use.

All **cost-incurring, irreversible interpretive acts** occur exclusively within `aura-core`.

---

### `aura-experiments` — Experimental Datasets (Narrative Substrate)

This repository contains the **materials of inquiry**, not the act of inquiry.

* Stores narrative texts and experiment configurations.
* Is treated strictly as a dataset repository.
* Performs no execution and incurs no cost on its own.
* Becomes meaningful only when consumed by `aura-core`.

It represents the **substrate of interpretation**, not interpretation itself.

---

### `aura-docker` — Encapsulated Runtime Environment

This repository standardizes **execution environments**.

* Provides container images bundling `aura-core` with baseline configuration.
* May include offline or local LLM runtimes (e.g. Ollama) for demonstration or constrained execution.
* Supports onboarding, workshops, and reproducible experimentation.

It defines **environmental assumptions**, not analytical semantics.

---

### `aura-pipeline` — Automation and Operational Pedagogy

This repository provides **reference automation**, not hidden logic.

* Contains reusable CI workflows and templates.
* Demonstrates how to execute AURA responsibly in automated contexts.
* Forces explicit declaration of constraints, engines, and outputs.

Pipelines are treated as **executable documentation** and serve an **operational pedagogical role**:
they teach correct usage by constraining what execution allows, not by narrative explanation.

---

### Architectural Invariants

The following constraints apply across the ecosystem:

* Dependencies flow strictly downward: `aura → aura-core → aura-pipeline`.
* No interpretation or model invocation occurs outside `aura-core`.
* No cost-incurring execution is hidden or implicit.
* Pipelines and datasets never redefine execution semantics.

---

## 2. Execution Model and Control Boundaries

Execution in AURA is treated as a **deliberate, parameterized interpretive act**.
Nothing executes implicitly, automatically, or without explicit declaration.

---

### 2.1 The Experiment as the Atomic Unit of Execution

All execution in AURA occurs within the scope of an **Experiment**.

An experiment is the smallest complete unit of execution and consists of:

1. **Narrative Input**
   A single narrative text that constitutes the object of interpretation.
   It may be sourced from a local filesystem, the `aura-experiments` repository, or external sources via explicit adapters.

2. **Execution Configuration**
   A declarative specification of how the narrative is to be interpreted, including the selected cognitive engine, analysis definition, constraints, and output policy.

3. **Environmental Context**
   The runtime conditions under which execution occurs, such as local execution, CI, containerized environments, and available credentials.

An experiment is always **explicitly defined** and never inferred from context.

---

### 2.2 Cognitive Engines

A **cognitive engine** defines how interpretation is carried out.

Each engine specifies:

* how prompts are executed,
* how cost is incurred and measured,
* what determinism or replay guarantees may exist.

Engines include:

* cloud-based LLMs,
* local or offline models,
* a mock engine that simulates execution without invoking models.

Execution “modes” are therefore **engine properties**, not global switches.

---

### 2.3 Execution Responsibility

`aura-core` is the **sole executor** of experiments.

It is responsible for:

* validating experiment configuration,
* enforcing declared constraints,
* invoking the selected cognitive engine,
* producing and recording outputs.

No other component may perform interpretation.

---

### 2.4 Constraints as Experiment Properties

Constraints are declared per experiment and enforced during execution.

They include:

* **Cost constraints**, such as token limits or monetary caps.
* **Engine constraints**, which prevent accidental or implicit model usage.
* **Reproducibility expectations**, which express intent but do not guarantee determinism.

Constraints are treated as **hard boundaries**, not advisory hints.

---

### 2.5 Experiment Outputs

Each experiment defines an explicit **output policy**.

Outputs may be:

* printed to standard output for exploratory use,
* written to disk for inspection,
* stored as CI artifacts,
* committed back to a repository when explicitly enabled.

There is no implicit persistence or side effect.

---

### 2.6 Control Boundaries

The architecture explicitly forbids:

* implicit execution without an experiment definition,
* hidden or background model invocation,
* unaccounted cost,
* mutation of narrative inputs,
* bypassing the experiment configuration layer.

These prohibitions preserve interpretive accountability.

---

### 2.7 Relationship to Pipelines

Pipelines **instantiate experiments** and materialize configuration choices.
They do not introduce new semantics or analytical behavior.

---

### 2.8 Experiment Configuration Schema (Conceptual)

The experiment configuration is a **single structured unit**, independent of format.

* **`text`**
  Identifies and locates the narrative input being analyzed.

* **`llm`**
  Declares the selected cognitive engine and its parameters.

* **`analysis_profiles`**
  Defines the analysis to be performed and its interpretive intent.

* **`budget`**
  Specifies non-negotiable execution limits.

* **`priors`**
  Declares baseline interpretive assumptions.

* **`pipelines`**
  Controls which execution steps are enabled or disabled.

* **`outputs`**
  Defines how results are emitted and persisted.

This schema is **normative at the conceptual level**.

---

## 3. Analysis Units and Interpretive Protocols

---

### 3.1 Analysis as a First-Class Concept

An **analysis** is a bounded interpretive procedure applied to a narrative input.

Analyses are:

* explicitly declared,
* repeatable within declared limits,
* non-mutating with respect to input text.

They produce artifacts, not conclusions.

---

### 3.2 Analysis Units

An **analysis unit** is the smallest indivisible interpretive component.

Each unit:

* consumes narrative input and configuration,
* produces a structured result,
* is executed by a cognitive engine,
* maintains no hidden state across executions.

---

### 3.3 Interpretive Protocols

An **interpretive protocol** defines how analysis units are orchestrated.

It specifies:

* execution order,
* dependencies between units,
* aggregation or transformation steps.

Protocols describe **procedure**, not reasoning.

---

### 3.4 Composition of Analyses (v1.0)

In architecture v1.0, each experiment defines **exactly one analysis profile**.

That profile:

* includes one interpretive protocol,
* may contain multiple analysis units,
* produces a single coherent result set.

Comparative or alternative interpretations are expressed as **separate experiments**, not parallel profiles.

---

### 3.5 Non-Agent Assumption

AURA explicitly rejects agent-based interpretation.

There is:

* no persistent memory,
* no goal-seeking behavior,
* no autonomous control flow,
* no hidden internal narrative.

All interpretation is externalized and inspectable.

---

### 3.6 Role of Cognitive Engines

Cognitive engines execute analysis units but do not define protocol structure.
They are interchangeable instruments, not epistemic authorities.

---

### 3.7 Analysis Results

Results are structured artifacts tied to:

* the analysis unit,
* the protocol,
* the experiment configuration.

They are not treated as truth claims.

---

### 3.8 Constraints on Analysis

Explicitly forbidden:

* implicit chaining of analyses,
* reliance on hidden state,
* engine-driven control decisions,
* unbounded or self-triggering loops.

---

### 3.9 Summary

Interpretation in AURA is modular, procedural, and bounded.

---

### 3.10 Taxonomy of Analysis Types

AURA recognizes the following non-exhaustive categories:

* **Surfacing analyses**, which expose salient textual features descriptively.
* **Semantic analyses**, which explore meaning relations and thematic structure.
* **Inferential analyses**, which generate provisional hypotheses under assumptions.
* **Comparative analyses**, which contrast outputs across experiments or engines.
* **Reflective / meta-analyses**, which examine analysis outputs themselves.

These categories guide design but do not constrain execution.

---

## 4. Reproducibility, Traceability, and Cost Accounting

---

### 4.1 Execution Trace

Every experiment produces an **execution trace** capturing:

* configuration,
* selected engine,
* analyses executed,
* outputs produced,
* cost incurred.

---

### 4.2 Raw vs Structured Outputs

For each analysis unit:

* **Raw output**
  The direct textual response produced by the cognitive engine, preserved prior to processing.

* **Structured output**
  Representations derived from raw output for comparison or aggregation.

Raw output is treated as an **epistemic artifact**, not a debug artifact.

---

### 4.3 Output Preservation Policy

Experiments define how outputs are persisted.

Architectural rule:
AURA must be capable of preserving raw outputs, even when not persisted by default.

---

### 4.4 Traceability

All outputs must be traceable to:

* the analysis unit that produced them,
* the experiment configuration,
* the cognitive engine used.

---

### 4.5 Reproducibility

Reproducibility is **declared**, not assumed.

Actual replay depends on:

* engine properties,
* availability of cached artifacts,
* preservation of raw outputs.

---

### 4.6 Cost Accounting

All execution incurs **accounted cost**, including simulation.

Cost is cumulative, visible, and enforced.

---

### 4.7 Architectural Guarantees

* No execution without a trace.
* No structured output without a raw origin.
* No cost without accounting.

---

## 5. Evaluation, Measures, and Epistemic KPIs

---

### 5.1 Role of Evaluation

Evaluation in AURA is comparative, descriptive, and epistemic.
It does not validate correctness or truth.

---

### 5.2 Scope of Measures

Measures operate on execution traces, outputs, and cost records.
They are derived artifacts.

---

### 5.3 KPI Categories

AURA recognizes:

* **Stability KPIs**, describing output variance across runs.
* **Sensitivity KPIs**, describing dependence on parameters or priors.
* **Cost–output KPIs**, relating interpretive output to incurred cost.
* **Structural KPIs**, characterizing abstraction and representation.
* **Comparative KPIs**, contrasting outputs across experiments or engines.

---

### 5.3.1 Illustrative KPI Examples (Non-Normative)

Examples include:

* similarity or divergence across repeated executions,
* impact of changing priors or engines,
* cost per unit of raw or structured output,
* ratio between raw and structured representations.

These examples are illustrative, not prescriptive.

---

### 5.4 KPI Configuration

KPIs are explicitly configured, computed post-execution, and forbidden from invoking cognitive engines.

---

### 5.5 Constraints

Disallowed:

* KPIs asserting semantic correctness,
* hidden evaluation logic,
* mutation of experiment results.

---

### 5.6 Summary

KPIs describe interpretation; they do not judge it.

---

## 6. Evolution and Versioning (Non-Normative)

This document defines **AURA Architecture v1.0** at a conceptual level.

Future extensions will be introduced through **minor version updates**, informed by implementation experience.
No forward-compatibility guarantees are assumed.
