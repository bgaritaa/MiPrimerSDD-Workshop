<!--
Sync Impact Report
Version: unversioned scaffold -> 1.0.0
Modified principles: none
Added principles: SIMPLICITY FIRST (YAGNI); SIMPLE, CLEAR DESIGN;
	STRUCTURED ARCHITECTURE; TEST-DRIVEN DEVELOPMENT; SPEC IS THE SOURCE OF TRUTH
Added sections: Planning and Scope; Verification and Review
Removed sections: none
Deferred TODO: original ratification date is unknown
-->
# Curso-PSWE-07 Constitution

## Core Principles

### I. SIMPLICITY FIRST (YAGNI)
Implementation MUST include only what the spec requires. The simplest working solution MUST be
chosen; features, abstractions, configuration, or dependencies MUST NOT be added "just in case".
Plans MUST justify added complexity. Rationale: unnecessary complexity increases maintenance cost.

### II. SIMPLE, CLEAR DESIGN
Functions and modules MUST be small and single-responsibility, with intention-revealing names.
Duplication MUST be removed only when it is real; single-use abstractions and premature
optimization MUST NOT be introduced. Rationale: clear, focused code is easier to change safely.

### III. STRUCTURED ARCHITECTURE
Code MUST separate UI, application logic, and persistence, with dependencies flowing UI -> logic ->
persistence. UI MUST NOT access storage directly; persistence MUST use a small replaceable interface.
Rationale: explicit boundaries isolate changes and make storage faking in tests possible.

### IV. TEST-DRIVEN DEVELOPMENT (NON-NEGOTIABLE)
Production code MUST NOT be written without a failing test that requires it. Follow
Red-Green-Refactor: write the failing test, make it pass with minimal code, then refactor. Business
logic MUST have unit tests; each user story MUST have at least one acceptance test mirroring its
scenarios. Tests MUST be fast and independent. Rationale: tests guide implementation and provide
repeatable evidence of behavior.

### V. SPEC IS THE SOURCE OF TRUTH
Every implementation task MUST trace to a spec requirement; work outside the spec MUST NOT be
implemented. Ambiguous requirements MUST be clarified before implementation. Rationale: a shared,
explicit scope prevents unrequested behavior.

## Planning and Scope

Plans MUST identify the spec requirements covered by each task and justify any added complexity.

## Verification and Review

Reviews MUST verify spec traceability, architecture boundaries, and required tests before changes
are accepted.

## Governance

This constitution governs project planning, implementation, and review. Amendments MUST be proposed
with a rationale and approved by the project maintainers. Versioning MUST use MAJOR for incompatible
governance changes, MINOR for new or materially expanded principles, and PATCH for clarifications.
Every change review MUST check compliance with these principles.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): original adoption date unknown | **Last Amended**: 2026-10-22
