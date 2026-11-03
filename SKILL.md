---
name: code-dissolver
description: >-
  [WHAT IT DOES]: Eliminates dead code, abandoned feature flags, and orphaned database models
  by combining static AST call-graph reachability with empirical production execution telemetry (eBPF, APM logs).
  [WHEN TO USE]: Use when safely refactoring massive legacy codebases, pruning zombie functions, removing stale feature flags,
  shrinking bloated dependencies, or eliminating the fear of breaking changes when cleaning technical debt.
  [WHEN NOT TO USE]: Do NOT use on brand-new codebases with no production traffic or for dynamic runtime monkey-patching.
---

# Code Dissolver: Telemetry-Proven Codebase Trimmer

```
  +---------------------------------------------------------------------------------+
  |                                 CODE DISSOLVER                                  |
  |        AST Static Reachability + Empirical Production Telemetry Correlator      |
  +---------------------------------------------------------------------------------+

       [Source Code Repository]                 [Production Observability]
       - Complete Abstract Syntax Tree (AST)     - 90-Day eBPF / APM Invocation Logs
       - Static Caller-Callee Graph              - Zero-Sample Traces
                  |                                           |
                  +---------------------+---------------------+
                                        |
                                        v
                       [Reachability & Telemetry Matrix]
                       - Active In-Use Functions
                       - Statically Unreachable Code
                       - Ghost Paths (Reachable in code, but 0 real-world calls)
                                        |
                                        v
                        +---------------+---------------+
                        |                               |
                  [Zero Drift]                  [Ambiguous Dynamic Call]
                        |                               |
                        v                               v
             [Automated Clean Cut]           [Flag for Human Review]
             - Dissolve Dead Functions
             - Inline 100% Feature Flags
             - Re-Run Test Suite (0 Errors)
```

---

## 1. When to Activate

Trigger `code-dissolver` when:
- **Massive Codebase Bloat:** Repositories with >100,000 LOC where engineers spend hours navigating dead files or obsolete functions.
- **Stale Feature Flag Cleanup:** Flags that have been 100% enabled (or 0% enabled) in production for months and clutter the codebase with conditional branching.
- **Microservice Decomposition:** Carving out domain boundaries and identifying dead endpoints before splitting a monolith.
- **Bundle Size Optimization:** Stripping unreferenced exports from backend and frontend services with mathematical safety confidence.

---

## 2. Dissolution Runbook

### Step 1: Parse the Static AST Call Graph
Run the AST analyzer to identify all function declarations, imports, and caller-to-callee dependencies:
```bash
python scripts/prune_telemetry_analyzer.py --demo
```
The parser builds a directed graph:
* Nodes = Functions, Methods, Classes.
* Edges = Function calls (`visit_Call`).

### Step 2: Correlate with Real-World Production Traces
Map each node against live production telemetry (eBPF traces, OpenTelemetry spans, or Datadog/NewRelic logs) over a 90-day window:
* **Active Nodes:** Executed $> 0$ times in production.
* **Ghost Nodes:** Reachable in theory, but executed **0 times** across millions of requests.
* **Unreachable Nodes:** Isolated from all application entry points.

### Step 3: Formal Pruning Safety Proof
1. Compute the transitive dependency closure: ensure no active node calls the candidate node.
2. Confirm the candidate is not referenced in reflection, dynamic `eval()`, or external public API contracts.
3. Verify all automated unit and integration tests remain green after mock removal.

### Step 4: Atomic Refactor & Pull Request
1. Safely delete the dead code blocks and orphaned imports.
2. Inline stale feature flags (removing the `if/else` wrapper and preserving only the true branch).
3. Export an audit report using [dissolver_report_template.md](./templates/dissolver_report_template.md).

---

## 3. Reference Files
- [prune_telemetry_analyzer.py](./scripts/prune_telemetry_analyzer.py): Python AST and telemetry correlation engine.
- [dissolver_report_template.md](./templates/dissolver_report_template.md): Formal pruning and safety audit report template.
