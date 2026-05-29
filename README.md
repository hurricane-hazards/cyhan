# C++/Python Hybrid Architecture Network (CyHAN)
## Standard v2.0

CyHAN (pronounced "cyan") establishes a unified computational architecture for
high-performance scientific and engineering platforms.

It enforces:

- A single canonical execution path
- High-performance C++ compute engines (numerical authority)
- Python orchestration, scaled to need
- An authoritative API boundary
- Native Qt desktop clients
- Cloud-ready web frontends
- Desktop / cloud execution parity
- Module-first decomposition for independent distribution
- A defined root-level integration tier above the modules

All computation flows through one authoritative backend stack. Capability is
partitioned into self-contained modules, composed by a shared integration tier.

This README is a summary. The normative specification is
`docs/CyHAN-Standard-v2.0.md`; where this document and the standard differ, the
standard governs.

---

## What's new in v2.0

v2.0 is a **MAJOR** increment over v1.1:

- **Root-level integration is now in scope.** v1.1 forbade root-level `backend/`
  and `frontend/` directories pending a future amendment. v2.0 is that amendment:
  a shared API surface and shared frontend adapters are specified and permitted.
- **The per-module entry contract is fixed.** Every module ships two mandatory
  files: a user-facing **launcher** (`run_<name>.py`) and a non-user-facing
  **orchestrator** (`main_<name>.py`). The launcher carries options and a launch
  call; the orchestrator carries the logic.
- **Conformance is stated as behavioral.** Compliance is judged by whether each
  architectural role is fulfilled, not by whether a particular file exists. The
  standard adopts the explicit RFC 2119 keyword register.

Carried forward from v1.1: module-first decomposition, C++-first numerical
authority, and orchestration scaled to module complexity.

---

## Core Architecture

```
Desktop Client (Qt C++)
or
Web Client (Browser)
│
▼
Python API
▼
Python Orchestration
▼
C++ Engines
```

Desktop and web clients are peers. Neither owns computation.

The ordering reflects:

- Native-first design philosophy
- Performance authority in C++
- Desktop as the primary engineering environment
- Web as a scalable distribution interface

---

## Architectural Principles

### 1. Single Execution Path

All production compute SHALL traverse:

```
Client → Python API → Python Orchestration → C++ Engines
```

No client may directly invoke C++ engines in compliant deployments.

### 2. C++-First Numerical Authority

C++ engines implement performance-critical numerical kernels, remain
reproducible, and avoid UI dependencies, HTTP routing, and workflow
orchestration. C++ is the default for computation. Python is employed where it
provides structural benefit, never by default.

### 3. The Launcher / Orchestrator Contract

Every module exposes its work through two files with a strict division of
audience:

- **Launcher, `run_<name>.py`** (user-facing). The control surface a scientist
  or operator edits and runs: a header, a clearly delimited block of user
  options (input paths, parameters, settings, toggles), and a call that launches
  the module. It is a readable substitute for a long command-line invocation. It
  contains no orchestration logic.
- **Orchestrator, `main_<name>.py`** (not user-facing). The importable
  realization of the Python Orchestration role: input validation, engine-call
  composition, job lifecycle, lightweight post-processing. It begins as a single
  file and expands into a package as complexity warrants.

The launcher imports and calls the orchestrator. The orchestration role is never
inlined into the launcher, even for trivial modules.

### 4. Orchestration Governance (Scaled to Need)

Orchestration assembles workflows, coordinates engine calls, manages job
lifecycle, performs lightweight post-processing, and remains UI-agnostic. Its
internal form scales with complexity: a single orchestrator file suffices for a
direct engine invocation; a dedicated package is used for multi-step
composition, AI/ML integration, or ensemble coordination. Python coordinates; it
does not numerically dominate.

### 5. API Contract Authority

The Python API layer, realized at the root level, defines the canonical system
boundary: versioned endpoints, input validation, authentication, and dispatch
into module orchestrators. It governs system semantics. The API dispatches only
through orchestrators and never reaches past one to invoke an engine directly.

### 6. Modular Decomposition

Capability is partitioned into modules. Each module is a self-contained vertical
that realizes the engine and orchestration roles for a single, well-defined
capability. Modules build, run, and ship independently of one another, and
remain runnable in isolation through their launcher even when root-level
integration is absent.

---

## Layer Responsibilities

**C++ Engines (per module).** Numerical solvers, Monte Carlo execution,
mesh/grid operations, seed-controlled RNG, parallel compute. Heavy computation
lives here. Engines expose capability to orchestration through a binding layer
that does not leak engine internals upward.

**Python Orchestration (per module).** Workflow assembly, engine coordination,
post-processing, metadata management. Realized in `main_<name>.py`; artifact form
scales with complexity.

**Python API (shared, root-level).** Endpoint exposure, input validation,
authentication, async job handling, dispatch to module orchestrators.

**Qt Desktop Application.** Native OS integration, event loop ownership,
visualization, HTTP client communication with the API. A module may ship its own
desktop component; the shared desktop shell composes them. HTML inside Qt is
optional.

**Web Frontend.** UI rendering, job submission, monitoring, visualization. Web
clients communicate exclusively through the API. A module may ship its own web
component; the shared web frontend composes them.

---

## Modules

A module is the structural unit of CyHAN. Every module SHALL:

- Contain its own C++ engine
- Realize the orchestration role in `main_<name>.py` (expandable to a package)
- Ship a user-facing launcher, `run_<name>.py`, at the module root
- Build, run, and be distributable independently of sibling modules

Modules MAY additionally include module-scoped frontend components, tests,
reference data, and documentation. The module identifier `<name>` is
`snake_case` and identifies the module end-to-end. The standard prescribes no
project-wide prefix or branding; an implementation MAY adopt a consistent
`<prefix>` for derived artifact names.

---

## Root-Level Integration

Above the modules sits a shared integration tier that composes them into one
system:

- **Shared API** at `backend/api/python/` imports per-module orchestrators and
  dispatches external requests into them. It is not itself a module.
- **Shared frontends** at `frontend/desktop/` and `frontend/web/` compose
  module-scoped GUIs and UIs into unified clients.

Root-level integration composes modules but is never required for a module to
build or run. Each module remains independently operable through its launcher.

---

## Deployment Modes

All modes preserve the canonical execution path.

```
Desktop Mode:   Desktop Client → API → Module Orchestration → C++ Engine
Cloud Mode:     Web Client     → API → Module Orchestration → C++ Engine
Launcher / CLI: Launcher or CLI      → Module Orchestration → C++ Engine
```

Launcher and CLI invocation MAY bypass the API role but SHALL NOT bypass the
orchestration role. Direct invocation through a module's launcher is a
first-class deployment posture, not a degraded one.

---

## Compliance Requirements

A system is CyHAN v2.0 compliant if:

- Heavy numerical computation resides in C++
- The orchestration role is realized in a non-user-facing orchestrator the
  launcher invokes
- The launcher is a user-facing control surface with options and a launch call,
  and no orchestration logic
- C++ engine code contains no HTTP, UI, or workflow orchestration logic
- Each module is self-contained and runnable in isolation via its launcher
- Each module ships both mandatory entry files
- The shared API, where deployed, is the sole client-facing boundary and
  dispatches only through orchestrators
- Desktop and web frontends share backend semantics
- Execution parity and reproducible execution are preserved

Compliance is behavioral, not file-presence-based. The two mandatory entry files
are required because they are the minimum realization of the launcher and
orchestrator roles, not as a structural checkbox.

---

## Performance Doctrine

- Heavy compute MUST remain in C++
- Python coordinates, it does not dominate
- Python footprint per module SHOULD be proportional to orchestration complexity
- API overhead SHOULD be negligible relative to engine runtime
- Scaling occurs at the worker or orchestration layer

---

## Recommended Project Structure

```
project-root/
│
├── modules/
│   ├── <name>/
│   │   ├── run_<name>.py                  launcher (user-facing)
│   │   ├── README.md
│   │   ├── backend/
│   │   │   ├── engines/
│   │   │   │   └── cpp/                    C++ engine + bindings
│   │   │   └── python/
│   │   │       └── main_<name>.py          orchestrator (not user-facing)
│   │   ├── frontend/                       (optional, module-scoped)
│   │   │   ├── desktop/
│   │   │   └── web/
│   │   ├── tests/
│   │   ├── data/                           (optional)
│   │   │   ├── inputs/
│   │   │   │   ├── raw/                     unmodified source inputs
│   │   │   │   └── processed/               preprocessed, engine-ready inputs
│   │   │   └── outputs/                     engine / orchestrator results
│   │   └── docs/
│   └── <other modules>/
│
├── backend/
│   └── api/
│       └── python/                         shared API surface
│
├── frontend/
│   ├── desktop/                            shared desktop shell (optional)
│   └── web/                                shared web frontend (optional)
│
├── docs/
│   └── CyHAN-Standard-v2.0.md
│
└── tests/                                  (optional, cross-module)
```

Modules are the canonical units of capability; the root tier composes them. The
launcher sits at the module root for discoverability; the orchestrator sits under
`backend/python/`, separating the operator's surface from the developer's. The
`data/` convention (`inputs/raw/`, `inputs/processed/`, `outputs/`) names the
default targets a launcher points at; a module uses only the subdirectories its
workflow needs.

---

## What CyHAN Is

- Compute-first
- C++-first in numerical authority
- Reproducible
- Multi-frontend
- Cloud-ready
- Desktop-native
- Modular
- Architecturally unified

## What CyHAN Is Not

- UI-first
- Web-only
- Multi-path
- Monolithic C++
- Python-only
- Python-by-default

---

For the complete technical specification, see:

`docs/CyHAN-Standard-v2.0.md`
