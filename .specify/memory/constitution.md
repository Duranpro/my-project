<!--
Sync Impact Report
Version change: 1.0.0 -> 1.0.1
Modified principles:
- I. Modular Boundaries and Maintainable Code (clarified MVP abstraction limits)
- III. Human-Confirmed AI Ingredient Recognition (clarified confidence display rules)
Added sections:
- None
Removed sections:
- None
Follow-up TODOs:
- None
-->
# AI Cooking Mobile App Constitution

## Core Principles

### I. Modular Boundaries and Maintainable Code
Code MUST be clear, maintainable, and modular. Mobile UI, backend services, AI
ingredient recognition, recommendation logic, and persistence MUST remain separated
by explicit interfaces and ownership boundaries. Cross-layer dependencies MUST be
introduced only through documented contracts, not through shared implementation
details or implicit coupling. Interfaces MUST reflect current responsibilities and
accepted requirements; abstractions created only for hypothetical future use MUST
NOT be introduced.

Rationale: independent modules keep the MVP understandable, testable, and ready for
future product growth without adding speculative infrastructure.

### II. Explicit Validation and Visible Errors
All user input, detected ingredients, preferences, persisted records, and external
API responses MUST be validated explicitly before use. Validation failures,
integration failures, and unexpected states MUST produce visible error handling for
users or operators as appropriate. Failures MUST NOT be swallowed silently, hidden
behind stale data, or converted into recommendations without a traceable error path.

Rationale: cooking recommendations depend on user context and external data, so
invalid or missing information must be caught before it affects user-facing results.

### III. Human-Confirmed AI Ingredient Recognition
AI ingredient recognition results MUST be treated as probabilistic predictions, not
facts. Recognition output MAY expose confidence or uncertainty only when the
recognition system provides reliable confidence information suitable for user
display. Confidence values MUST NOT be inferred, fabricated, or shown when the
system does not provide reliable confidence information. Users MUST confirm, remove,
correct, and manually add ingredients before recommendations are generated.
Recommendation flows MUST use the user-confirmed ingredient set, not raw AI
detections.

Rationale: AI mistakes are expected, and user confirmation is required before the
application makes recipe or filtering decisions.

### IV. Privacy, Security, and Data Minimization
Security and privacy MUST be default design constraints. Secrets, credentials, API
keys, signing material, and service tokens MUST NOT be hardcoded, committed to source
control, logged, or exposed to the mobile client unless specifically intended as
public configuration. User data and uploaded images MUST be collected, stored,
retained, and transmitted only when necessary for the active product behavior.

Rationale: cooking preferences, images, and account data can be sensitive; minimizing
collection and exposure reduces both user risk and operational burden.

### V. Reliable Mobile Experience and Critical Testing
The mobile experience MUST remain consistent, responsive, and accessible across
supported devices. User-facing workflows MUST include explicit loading, empty,
success, and error states. Critical functionality MUST be tested at the appropriate
level, especially authentication, ingredient validation, recommendation filtering,
recipe selection, and integrations with AI or external services.

Rationale: users need predictable feedback during image processing, network calls,
and recommendation generation, and tests are required where regressions would break
core cooking workflows.

## Architecture and Product Constraints

The system MUST prefer simple solutions over unnecessary architectural complexity,
especially during the MVP. New dependencies MUST be minimal, justified by a concrete
need, and consistent with the existing platform architecture.

Backend services MUST include useful logging and observability for failures,
latency, integration health, and operational events. Logs, traces, metrics, and
alerts MUST avoid sensitive data, including raw uploaded images, credentials,
personal identifiers, and detailed preference data unless explicitly approved and
redacted.

Performance MUST be considered for image processing, network calls, AI recognition,
recommendation generation, and persistence access. Designs that add meaningful
latency, memory pressure, bandwidth usage, or battery cost MUST document the tradeoff
and include a mitigation or measurement plan.

The architecture MUST allow future product growth, including user-generated recipes,
follows, likes, creator profiles, and other social features. MVP implementation MUST
NOT couple current workflows to those future features unless the coupling is required
by an accepted current requirement. Future extensibility MUST NOT justify premature
abstractions, unused infrastructure, speculative interfaces, or unnecessary
complexity in the MVP.

## Delivery and Quality Gates

Important technical decisions and architectural changes MUST be documented close to
the affected system, such as in design notes, feature plans, ADRs, or implementation
documentation. Documentation MUST capture the decision, rationale, rejected
alternatives when relevant, and operational consequences.

A feature is complete only when its expected behavior is implemented, validated,
tested where relevant, and documented when necessary. Feature review MUST verify
module boundaries, validation behavior, user-visible states, security and privacy
handling, critical test coverage, observability, performance impact, and dependency
justification.

Tests SHOULD be automated when they protect critical behavior or integration
contracts. Manual validation MAY be used for visual mobile behavior, AI output review,
or third-party service behavior when automation is impractical, but the manual checks
and results MUST be documented.

Implementation-critical technology decisions MUST be resolved during planning before task generation and implementation. A plan MUST NOT defer framework, platform, persistence, integration, or runtime choices when those choices are required to produce a runnable feature.

A user-facing feature MUST NOT be considered complete when implemented only as stack-neutral descriptors, mocks, abstract interfaces, or test harnesses unless the specification explicitly defines such an artifact as the intended deliverable. Mobile features intended for production MUST be implemented and runnable in the selected mobile stack before they satisfy the Definition of Done.

Architecture boundaries MAY remain technology-independent, but implementation plans MUST map those boundaries to concrete modules, paths, frameworks, and runtime integrations before implementation begins.

## Governance

This constitution supersedes conflicting implementation practices, informal
conventions, and feature plans. Every specification, plan, task list, code review,
and release decision MUST evaluate compliance with these principles.

Amendments MUST be proposed as a documented change to this constitution, including
the rationale, expected impact, migration or adoption work, and version bump. Changes
MUST be reviewed before adoption. Approved amendments MUST update the version and
last amended date in this file.

Versioning follows semantic governance versioning. MAJOR versions indicate removals
or redefinitions of principles that change compliance expectations. MINOR versions
indicate new principles, new sections, or materially expanded guidance. PATCH
versions indicate clarifications, wording changes, or non-semantic corrections.

Compliance review is required before a feature is considered complete. Any exception
to a MUST-level rule requires documented approval, scope, expiration criteria, and a
follow-up plan.

**Version**: 1.1.0 | **Ratified**: 2026-09-01 | **Last Amended**: 2026-09-02
