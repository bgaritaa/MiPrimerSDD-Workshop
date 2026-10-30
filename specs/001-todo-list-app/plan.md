# Implementation Plan: Curso-PSWE-07 To-Do List

**Branch**: `001-todo-list-app` | **Date**: 2026-10-22 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `/specs/001-todo-list-app/spec.md`

**Note**: This template is filled in by the `/speckit-plan` command; its definition describes the execution workflow.

## Summary

A two-screen to-do app (List and Detail) where users add, view, edit, complete, filter, and delete
tasks that persist across restarts. Technical approach: a static single-page web app in plain
JavaScript, HTML, and CSS, with three layers (UI → logic → persistence). Tasks are saved in browser
`localStorage` behind a small `TaskRepository` interface. Tests are written first with Vitest and
jsdom (see [research.md](research.md)).

## Technical Context

**Language/Version**: JavaScript (ES2022 modules), HTML5, CSS3

**Primary Dependencies**: None at runtime; dev-only: Vite (dev server/build), Vitest, jsdom

**Storage**: Browser `localStorage`, key `curso-pswe-07.tasks` (JSON array)

**Testing**: Vitest + jsdom (unit tests and UI-level acceptance tests)

**Target Platform**: Current versions of Chrome, Edge, Firefox, and Safari, desktop and mobile

**Project Type**: Static single-page web application (no backend)

**Performance Goals**: Filter changes and screen switches are instant for up to 500 tasks (SC-004)

**Constraints**: Offline-capable after load, WCAG 2.1 AA contrast, 44x44 px targets, layouts from
360 px to 1280 px wide without horizontal scrolling

**Scale/Scope**: Single user on one device, 2 screens plus 1 form and 1 confirmation dialog,
hundreds of tasks

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Gate | Pre-research | Post-design |
|-----------|------|--------------|-------------|
| I. Simplicity First | No runtime dependencies, framework, router, or configuration beyond the spec | PASS | PASS — only dev tooling; no runtime libraries |
| II. Simple, Clear Design | Small single-purpose modules; no single-use abstractions | PASS | PASS — one module per screen/concern (see structure) |
| III. Structured Architecture | UI → logic → persistence; UI never touches storage; repository interface | PASS | PASS — `ui/` imports only `logic/`; `logic/` depends on `TaskRepository` ([contract](contracts/task-repository.md)) |
| IV. TDD (non-negotiable) | Unit tests for logic; ≥1 acceptance test per user story; fast, independent | PASS | PASS — 4 acceptance test files (US1–US4) with in-memory repository |
| V. Spec Is Source of Truth | Every element traces to an FR/SC/assumption; nothing extra | PASS | PASS — every UI element in the [UI contract](contracts/ui-contract.md) cites an FR |

The `TaskRepository` interface has two implementations (localStorage and in-memory). That is a
constitution requirement (III), not added complexity. No violations, so Complexity Tracking is
empty.

## Project Structure

### Documentation (this feature)

```text
specs/001-todo-list-app/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   ├── task-repository.md   # TaskRepository + TaskService contracts
│   └── ui-contract.md       # Screens, labels, roles
└── tasks.md                 # Created by /speckit-tasks
```

### Source Code (repository root)

```text
index.html                     # <title>Curso-PSWE-07</title>, app root
package.json                   # scripts: dev, build, test
src/
├── main.js                    # Wires LocalStorageTaskRepository → TaskService → UI
├── styles.css                 # Design tokens, layout, 44x44 targets
├── logic/
│   ├── task-service.js        # add, update, setDone, remove, list(filter), get
│   └── errors.js              # ValidationError, NotFoundError, StorageError
├── persistence/
│   ├── local-storage-task-repository.js
│   └── in-memory-task-repository.js
└── ui/
    ├── app.js                 # Switches between list and detail screens, error banner
    ├── list-screen.js         # Header, filter, rows, empty state, "Add task"
    ├── detail-screen.js       # Back, title, description, status, actions
    ├── task-form.js           # Shared add/edit form with validation message
    └── confirm-dialog.js      # Delete confirmation

tests/
├── unit/
│   ├── task-service.test.js
│   └── local-storage-task-repository.test.js
└── acceptance/
    ├── us1-add-and-view.test.js
    ├── us2-view-and-update.test.js
    ├── us3-filter.test.js
    └── us4-delete.test.js
```

**Structure Decision**: Single project at the repository root. The `src/ui`, `src/logic`, and
`src/persistence` folders map one-to-one to the constitution's three layers. Acceptance tests
render the UI with `InMemoryTaskRepository`. The repository unit test checks persistence using
jsdom's `localStorage`.

## Open Items

- Clarification question 1 (can status be toggled from the list row?) was not answered. The plan
  follows the spec as written: tapping a row opens the detail screen, and status changes happen
  only there. If the answer differs, update FR-002/FR-009 and `list-screen.js`.

## Complexity Tracking

No constitution violations to justify.
