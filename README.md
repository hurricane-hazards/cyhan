# C++/Python Hybrid Architecture Network (CyHAN)
 
**Standard v1.1**
 
CyHAN (pronounced "cyan") establishes a unified computational architecture for high-performance scientific and engineering platforms.
 
It enforces:
 
- A single canonical execution path
- High-performance C++ compute engines
- Python-based orchestration, scaled to need
- Authoritative API boundary
- Native Qt desktop clients
- Cloud-ready web frontends
- Desktop–cloud execution parity
- Module-first decomposition for independent distribution

All computation flows through one authoritative backend stack. Capability is partitioned into self-contained modules.
 
---
 
## Core Architecture
 
```
Desktop Client (Qt C++)
or
Web Client (React / Browser)
│
▼
Python API
▼
Python Orchestration
▼
C++ Engines
```
 
Both desktop and web clients are peers.
 
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
 
C++ engines:
 
- Implement performance-critical numerical kernels
- Remain reproducible
- Avoid UI dependencies
- Avoid HTTP routing
- Avoid workflow orchestration

C++ is the default for computation. Python is employed where it provides structural benefit.
 
### 3. Orchestration Governance (Scaled to Need)
 
Python orchestration is an architectural **role** realized in one of two artifact forms per module:
 
- A dedicated Python package, when the module performs workflow assembly, multi-step composition, AI/ML integration, or ensemble coordination.
- The module's launcher script (`run_<name>.py`), when the module's workflow reduces to a direct engine invocation.

In either form, orchestration:

- Assembles workflows
- Coordinates engine calls
- Manages job lifecycle
- Performs lightweight post-processing
- Remains UI-agnostic
Python coordinates. It does not numerically dominate.
 
### 4. API Contract Authority
 
The Python API layer (when realized at root level):
 
- Defines the canonical system boundary
- Exposes versioned endpoints
- Validates inputs
- Handles authentication
- Dispatches to orchestration

The API governs system semantics. Root-level API integration is anticipated future work; in v1.1, callers invoke module orchestration directly.
 
### 5. Modular Decomposition
 
Capability is partitioned into **modules**. Each module is a self-contained vertical that realizes the engine and orchestration roles for a single, well-defined capability. Modules build, run, and ship independently of one another.
 
---
 
## Layer Responsibilities
 
### Qt Desktop Application (Native Client)
 
- Native OS integration
- Event loop ownership
- Visualization
- HTTP client communication with API (when API is realized)

HTML inside Qt is optional and not required by the standard.
 
### Web Frontend (Browser Client)
 
- UI rendering
- Job submission
- Monitoring
- Visualization

Web clients communicate exclusively through the API.
 
### Python API (Future Root-Level)
 
- Endpoint exposure
- Input validation
- Authentication
- Async job handling
- Dispatch to module orchestration

### Python Orchestration (Per Module)
 
- Workflow assembly
- Engine coordination
- Post-processing
- Metadata management

Artifact form scales with module complexity (package or launcher).
 
### C++ Engines (Per Module)
 
- Numerical solvers
- Monte Carlo execution
- Mesh/grid operations
- Seed-controlled RNG
- Parallel compute

Heavy computation lives here.
 
---
 
## Modules
 
A module is the structural unit of CyHAN v1.1.
 
Every module SHALL:
 
- Contain its own C++ engine
- Realize the orchestration role in Python (package or launcher form)
- Ship a `run_<name>.py` launcher script
- Build, run, and be distributable independently of sibling modules

Modules MAY additionally include module-scoped frontend components, tests, reference data, and documentation.
 
The module identifier `<name>` is `snake_case` and identifies the module end-to-end.
 
---
 
## Deployment Modes
 
CyHAN supports multiple deployment modes. All modes preserve the canonical execution path.
 
### Desktop Mode
 
```
Desktop Client → API → Module Orchestration → C++ Engine
```
 
### Cloud Mode
 
```
Web Client → API → Module Orchestration → C++ Engine
```
 
### CLI Mode
 
```
Command-Line Interface → Module Orchestration → C++ Engine
```
 
CLI mode MAY bypass the API role but SHALL NOT bypass the orchestration role.
 
In v1.1, prior to root-level API integration, CLI mode against individual modules is the operative deployment posture. Full Desktop and Cloud modes are anticipated future work.
 
---
 
## Compliance Requirements
 
A system is CyHAN Standard v1.1 compliant if:
 
- Heavy numerical computation resides in C++
- Workflow orchestration is implemented in Python (package or launcher form, per §4.2 of the standard)
- C++ engine code contains no HTTP, UI, or workflow orchestration logic
- Each module is self-contained
- Each module ships a launcher script
- When realized, the API surface is the sole client-facing boundary
- Desktop and Web (when realized) share backend semantics
- Reproducible execution is supported

Compliance is behavioral, not file-presence-based.
 
---
 
## Performance Doctrine
 
- Heavy compute MUST remain in C++
- Python coordinates, not dominates
- Python footprint per module SHOULD be proportional to orchestration complexity
- API overhead must be negligible relative to engine execution
- Scaling occurs at the worker orchestration layer
---
 
## Recommended Project Structure
 
```
project-root/
│
├── modules/
│   ├── <name>/
│   │   ├── README.md
│   │   ├── backend/
│   │   │   ├── engines/
│   │   │   │   └── cpp/
│   │   │   └── python/                 (optional)
│   │   ├── frontend/                   (optional)
│   │   │   ├── desktop/
│   │   │   └── web/
│   │   ├── scripts/
│   │   │   └── run_<name>.py
│   │   ├── tests/
│   │   ├── data/
│   │   └── docs/
│   └── <other modules>/
│
└── docs/
    └── CyHAN-Standard-v1.1.md
```
 
Modules are canonical units of capability. Each module is self-contained.
 
Root-level `backend/api/`, `frontend/desktop/`, and `frontend/web/` directories are anticipated future work and SHALL NOT exist until that amendment lands.
 
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
- Monolithic C++
- Python-only
- Python-by-default
- Multi-path
---
 
For the complete technical specification, see:
 
[`docs/CyHAN-Standard-v1.1.md`](docs/CyHAN-Standard-v1.1.md)
 
