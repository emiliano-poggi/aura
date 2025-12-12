# A.U.R.A. Architecture
_Implementation-oriented notes that complement `vision.md` and `README.md` (focus: how we will operationalize the A.U.R.A. paradigm with code, APIs, and automation)._

## 1. Repository Topology (Ecosystem)
A.U.R.A. is intentionally split into distinct repositories to separate concerns and licensing:

### `aura` (research hub)
- Contains the paper, conceptual framework, methodology, and research notes.
- No executable code.
- Licensed as **CC BY-NC-SA 4.0**.

### `aura-core` (execution engine)
- Contains the actual implementation of A.U.R.A.:
  - CLI and/or library
  - LLM provider adapters (OpenAI, Anthropic, local models, etc.)
  - analysis modules (chunking, inference protocols, structured outputs)
  - cost/budget enforcement
  - caching and reproducibility mechanisms
  - development simulation (mock mode)
- Designed to run:
  - locally (developer workstation)
  - in CI (GitHub Actions / other CI engines)

### `aura-pipeline` (automation layer)
- Contains reusable workflows/templates that demonstrate:
  - how to run A.U.R.A. in GitHub Actions
  - how to pass user-defined cost/model constraints
  - how to store outputs as artifacts or commit them back (optional)
- Provides reference pipelines for narrative projects (`aura-*`) to adopt.

### Narrative projects (`aura-*`)
- Separate repos (or repo folders) containing manuscripts + configs + results.
- They are treated as *datasets* and do not “cost” anything by themselves.
- Costs happen only when `aura-core` runs analyses and calls external providers.

---

## 2. Execution Model: User-Owned Costs, User-Owned Limits
A core principle: **AURA-core does not “own” any usage rights.**

AURA-core is a deterministic engine that:
- receives **provider credentials** from the user (API key / token / endpoint),
- receives **budget + analysis level** constraints (tokens, model, max cost),
- executes only what is allowed by those constraints.

### Implications
- An external user can adopt AURA-core depending on his accounts.
- AURA-core is compatible with multiple providers and cost models.
- AURA-core can be used in both “economical” and “deep analysis” modes.

---

## 3. Configuration-Driven Architecture (`aura.yml`)
Every narrative repo (or run) should be controllable via a configuration file, e.g. `aura.yml`.

### Recommended config sections
- **provider**: selected vendor and model
- **budget**: hard limits (tokens and/or max cost)
- **analysis_profile**: “economical | standard | deep | custom”
- **modes**: structured vs unstructured Stream of Consciousness experiments
- **pipelines**: which steps are enabled/disabled
- **caching**: reuse previous outputs if inputs unchanged
- **outputs**: where to write JSON/Markdown and artifacts

Example skeleton:
```yaml
provider:
  name: openai              # openai | anthropic | local | custom
  model: gpt-4.1-mini       # user-controlled
budget:
  max_total_usd: 2.00       # optional if price tables exist
  max_input_tokens: 12000
  max_output_tokens: 2000
analysis_profile: economical # economical | standard | deep
modes:
  soc_paradigm: structured   # structured | unstructured
pipelines:
  enabled:
    - ingest
    - chunk
    - infer_world_model
    - narrative_reconstruction
    - bias_probe
caching:
  enabled: true
  strategy: content_hash
outputs:
  format: [json, md]
  dir: analysis/
````

---

## 4. Two Experimental Modes (Operationalized)

AURA focuses on two distinct SoC experimental paradigms, implemented as **explicit run modes**:

### Mode A — Structured SoC (Hidden Narrative)

Assumption: there is an underlying narrative logic, hidden by fragmentation.

AURA-core must:

* infer candidate entities, events, causal relations
* propose multiple competing reconstructions (N-best)
* track uncertainty explicitly (confidence, alternative hypotheses)

Outputs:

* inferred timeline(s)
* entity graph
* “most likely” narrative reconstruction
* ambiguity report (what cannot be resolved from text alone)

### Mode B — Unstructured SoC (No Intended Narrative)

Assumption: there is no underlying plot; the text is a pure mental flow.

AURA-core must:

* detect *pressure toward coherence* in model outputs (over-structuring bias)
* record what structure the model “injects”
* compare across models (bias fingerprinting)

Outputs:

* “imposed narrative” hypotheses + evidence snippets
* bias report (teleology, causal completion, entity invention)
* comparison matrix across providers/models

---

## 5. Provider Adapter Layer (Multi-LLM Design)

AURA-core should implement a stable internal interface, e.g.:

* `generate(prompt, settings) -> completion`
* `structured_generate(schema, prompt, settings) -> json`
* `embed(text) -> vector` (optional)
* `estimate_cost(tokens_in, tokens_out, model) -> cost` (optional best-effort)

This allows:

* swapping providers without changing core logic
* running multi-model comparisons as first-class experiments

---

## 6. Cost, Budget, and Safety Controls (Hard Stops)

AURA-core must enforce constraints **before** making a call and **during** pipelines.

Recommended controls:

* `max_calls_per_run`
* `max_total_tokens_per_run`
* `max_total_cost_usd` (when estimable)
* `max_concurrency`
* “deep steps” must be explicitly enabled (no silent premium usage)

Behavior:

* if the budget is exceeded, AURA-core stops and writes a partial results report.

---

## 7. Reproducibility: Logging, Versioning, and Deterministic Runs

AURA experiments should be repeatable.

Minimum reproducibility metadata to store per run:

* timestamp
* git commit hash (narrative repo + aura-core repo if possible)
* provider + model + model version (if available)
* parameters: temperature/top_p/max_tokens
* prompts/templates version IDs
* input hashes (content hash per chunk)
* caching hits/misses
* token usage & estimated cost

Store as:

* `analysis/run.json` (machine-readable)
* `analysis/run.md` (human summary)

---

## 8. Caching Strategy (Token-Saving by Design)

AURA-core should avoid re-calling providers for identical inputs.

Recommended approach:

* compute content hash: `SHA256(normalized_text + prompt_template_id + model + settings)`
* store the completion keyed by that hash
* reuse results if the hash matches

This is essential for:

* iterative development
* re-running pipelines in CI
* long manuscripts with frequent small edits

---

## 9. Development Simulation Mode (No-API / Low-API Workflows)

AURA-core must support a “simulation” mode to reduce token burn during development.

### Option 1: Mock provider (recommended baseline)

* return deterministic canned outputs
* optionally use lightweight heuristics (regex/entity extraction) to emulate structure

### Option 2: Local model fallback (optional)

* use local LLMs (via Ollama/llama.cpp) for cheap iterations
* reserve paid APIs for “deep analysis” runs

### Option 3: Record/Replay

* run once against real APIs
* store raw responses
* replay during development/tests

---

## 10. `aura-pipeline`: Reference GitHub Actions Patterns

The pipeline repo should provide reusable workflow templates that:

* install `aura-core`
* load `aura.yml` from the narrative repo
* accept secrets for provider keys (`OPENAI_API_KEY`, etc.)
* run selected AURA pipelines
* publish artifacts: `analysis/*.json`, `analysis/*.md`

Suggested triggers:

* `workflow_dispatch` (manual run)
* `push` on manuscript changes
* scheduled runs (nightly) for longitudinal analysis

---

## 11. Suggested `aura-core` Implementation Language

AURA-core should prioritize **Python** due to:

* strongest ecosystem for NLP, embeddings, clustering, evaluation tooling
* easy CLI packaging
* straightforward CI integration
* rapid prototyping for research-driven iteration

(Other languages may exist at the edges: e.g., PowerShell for wrappers, TypeScript for web UI later, but the core engine is best in Python.)

---

## 12. Minimal CLI Surface (First Practical Milestone)

AURA-core should ship early with a small stable CLI:

* `aura analyse --config aura.yml`
* `aura analyse --profile economical|standard|deep`
* `aura compare --models gpt-4.1-mini,claude-haiku,...`
* `aura cache purge`
* `aura doctor` (validate config, provider, budgets, environment)

This provides immediate usability and a foundation for richer pipelines.

---

## 13. Output Contract (Stable Formats)

To enable downstream automation, AURA-core should produce structured outputs with predictable schema.

Minimum:

* `analysis/results.json` (machine-readable)
* `analysis/report.md` (human-readable)
* optional: `analysis/graphs/*.json` (entity/event graph, timeline)

---

## 14. Practical Consequence: AURA as a Reusable Research Instrument

With the above, A.U.R.A. becomes:

* a reproducible experimental framework
* provider-agnostic (within adapter limits)
* cost-controlled by design
* suitable for open-source adoption (engine + pipelines + narrative datasets)

This architecture is intentionally modular so that future work can add:

* evaluation metrics (consistency, hallucination pressure, narrative recoverability)
* visualization dashboards
* multi-agent protocols (analyzer vs critic vs judge)
* fine-tuning experiments (optional, later stage)
