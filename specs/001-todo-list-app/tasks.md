---

description: "Task list for Curso-PSWE-07 To-Do List"
---

# Tasks: Curso-PSWE-07 To-Do List

**Input**: Design documents from `/specs/001-todo-list-app/`

**Prerequisites**: plan.md, spec.md, research.md, data-model.md, contracts/, quickstart.md

**Tests**: REQUIRED. Constitution IV (TDD, non-negotiable) requires unit tests for logic and at
least one acceptance test per user story. Each test task MUST be written and seen failing before
its implementation task.

**Organization**: Tasks are grouped by user story so each story can be implemented and tested
independently.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies on incomplete tasks)
- **[Story]**: User story the task belongs to (US1–US4)

## Path Conventions

Single project at repository root: `index.html`, `src/`, `tests/` (see plan.md).
Exact UI labels come from [contracts/ui-contract.md](contracts/ui-contract.md); service and
repository behavior from [contracts/task-repository.md](contracts/task-repository.md).

---

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Project initialization

- [ ] T001 Create `package.json` at repository root with `"type": "module"`, scripts `"dev": "vite"`, `"build": "vite build"`, `"test": "vitest run"`, and devDependencies `vite`, `vitest`, `jsdom` only (no runtime dependencies); run `npm install`
- [ ] T002 [P] Create `vitest.config.js` at repository root setting `test.environment` to `"jsdom"` and `test.include` to `["tests/**/*.test.js"]`
- [ ] T003 [P] Create `index.html` at repository root with `<html lang="en">`, `<title>Curso-PSWE-07</title>`, viewport meta tag, `<link rel="stylesheet" href="/src/styles.css">`, an empty `<div id="app"></div>`, and `<script type="module" src="/src/main.js"></script>`
- [ ] T004 [P] Create `src/styles.css` with CSS custom properties per research.md §9: background `#fafafa`, text `#1f2937`, muted `#4b5563`, accent `#2563eb`, warning `#b91c1c`; system sans-serif font stack; single column `max-width: 640px` centered with generous padding; `min-height: 44px; min-width: 44px` on all `button`, `input`, `textarea`; `.done` title style with `text-decoration: line-through` and muted color; no horizontal overflow (`overflow-wrap: anywhere`)

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Error types, persistence layer, and app shell needed by every story

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T005 [P] Create `src/logic/errors.js` exporting classes `ValidationError`, `NotFoundError`, `StorageError`, each extending `Error` and setting `name`
- [ ] T006 [P] Write failing unit tests in `tests/unit/local-storage-task-repository.test.js` for the TaskRepository contract: `loadAll()` on empty storage returns `[]`; `loadAll()` after `saveAll(tasks)` returns equal tasks using a new repository instance (simulates reopening the app); data stored under key `curso-pswe-07.tasks`; invalid JSON in storage makes `loadAll()` throw `StorageError` and leaves the stored value unchanged; `saveAll` throws `StorageError` when `localStorage.setItem` throws; returned arrays are copies. Clear `localStorage` in `beforeEach`
- [ ] T007 Implement `src/persistence/local-storage-task-repository.js` exporting class `LocalStorageTaskRepository` with `loadAll()` and `saveAll(tasks)` using `localStorage` key `curso-pswe-07.tasks` (JSON array); wrap read/parse/write failures in `StorageError`; never overwrite or clear unreadable data. Make T006 pass
- [ ] T008 [P] Implement `src/persistence/in-memory-task-repository.js` exporting class `InMemoryTaskRepository` with the same `loadAll()`/`saveAll(tasks)` contract, an optional constructor argument of initial tasks, and copies on read/write (used by tests only)
- [ ] T009 Create `src/ui/app.js` exporting `createApp(root, service)` that renders into `root` the current view (initially the list screen; placeholder content until US1) and exposes `showList()` and `showDetail(id)` for screen switching; render a storage error banner with `role="alert"` and text "Your tasks could not be loaded or saved. Please try again." whenever a service call throws `StorageError`
- [ ] T010 Create `src/main.js` that builds `new TaskService(new LocalStorageTaskRepository())` and calls `createApp(document.getElementById("app"), service)` (TaskService is implemented in T013; create an empty exported class stub in `src/logic/task-service.js` now so the app boots)

**Checkpoint**: Persistence works and is tested; the app boots to an empty shell

---

## Phase 3: User Story 1 - Add and view tasks (Priority: P1) 🎯 MVP

**Goal**: Users see their task list, add tasks with a title and optional description, and tasks
persist across restarts.

**Independent Test**: Start with no tasks, add a task, confirm it is listed; create a new app
instance over the same storage and confirm it is still listed.

### Tests for User Story 1 ⚠️ (write first, must fail)

- [ ] T011 [P] [US1] Write failing unit tests in `tests/unit/task-service.test.js` (using `InMemoryTaskRepository`) for: `add({title, description})` returns a task with generated string `id`, `done: false`, numeric `createdAt`; title and description are trimmed; title empty or only spaces throws `ValidationError` ("`title.trim() === ""` → reject") and saves nothing; description optional (defaults to `""`); `add` persists via `saveAll`; `list("all")` returns tasks newest first by `createdAt`; `get(id)` returns the task or `undefined`
- [ ] T012 [P] [US1] Write failing acceptance tests in `tests/acceptance/us1-add-and-view.test.js` mirroring spec US1 scenarios 1–6 by rendering `createApp` into a jsdom container with `TaskService` + `InMemoryTaskRepository`: heading "Curso-PSWE-07" and empty state "No tasks yet. Add your first one!"; "Add task" → fill "Title"/"Description" → "Save" lists the task as Pending; empty title shows "Title is required." and creates nothing; title-only task is created; "Cancel" creates nothing; a new app over the same repository shows the same tasks; `document.title` is "Curso-PSWE-07"

### Implementation for User Story 1

- [ ] T013 [US1] Implement `src/logic/task-service.js` exporting class `TaskService(repository)` with `add({ title, description })`, `list(filter)` (supporting `"all"` for now), and `get(id)` per contracts/task-repository.md; use `crypto.randomUUID()` for `id` and `Date.now()` for `createdAt`; Task fields: `id`, `title` (required, trimmed, non-empty), `description` (optional, trimmed, `""` = none), `done` (`false` on creation), `createdAt`. Make T011 pass
- [ ] T014 [P] [US1] Implement `src/ui/task-form.js` exporting `renderTaskForm(container, { initial, onSave, onCancel })`: labeled "Title" input and "Description" textarea (prefilled from `initial` if given), "Save" and "Cancel" buttons; on Save with empty/whitespace title show "Title is required." next to the Title field and do not call `onSave`
- [ ] T015 [US1] Implement `src/ui/list-screen.js` exporting `renderListScreen(container, { tasks, onAdd, onOpen })`: `h1` "Curso-PSWE-07"; "Add task" button that opens the task form in place; one row `button` per task with a status indicator (accessible label "Done"/"Pending") and the title, `.done` class on completed titles; row click calls `onOpen(id)`; empty state "No tasks yet. Add your first one!" when there are no tasks
- [ ] T016 [US1] Wire the list screen and add flow into `src/ui/app.js`: load `service.list("all")`, render list screen, on form Save call `service.add` and re-render, on Cancel return to the list; catch `ValidationError` to keep the form open. Make T012 pass

**Checkpoint**: MVP — users can add and view persisted tasks

---

## Phase 4: User Story 2 - View and update a task (Priority: P2)

**Goal**: Users open a task, see its details, toggle done/not done, and edit title/description.

**Independent Test**: With one existing task, open it, toggle status, edit the title, go back, and
confirm the changes appear and persist in a new app instance.

### Tests for User Story 2 ⚠️ (write first, must fail)

- [ ] T017 [P] [US2] Add failing unit tests to `tests/unit/task-service.test.js` for: `update(id, {title, description})` trims, keeps `done` and `createdAt`, throws `ValidationError` on empty title (task unchanged) and `NotFoundError` on unknown id; `setDone(id, done)` updates status and throws `NotFoundError` on unknown id; both persist via `saveAll`
- [ ] T018 [P] [US2] Write failing acceptance tests in `tests/acceptance/us2-view-and-update.test.js` (seed `InMemoryTaskRepository` with tasks) mirroring spec US2 scenarios 1–8: clicking a row shows detail with `h2` title, description, status "Pending"/"Done"; muted "No description" when empty; "Mark as done" → status "Done" and button "Mark as not done", and back; "Edit" shows form prefilled, "Save" shows updated values; clearing title shows "Title is required." and leaves task unchanged; "Cancel" keeps previous values; "Back" returns to list reflecting changes (done title has `.done` class)

### Implementation for User Story 2

- [ ] T019 [US2] Add `update(id, { title, description })` and `setDone(id, done)` to `src/logic/task-service.js` per contracts/task-repository.md (same validation as `add`; status unchanged by `update`). Make T017 pass
- [ ] T020 [US2] Implement `src/ui/detail-screen.js` exporting `renderDetailScreen(container, { task, onBack, onEdit, onToggle, onDelete })`: "Back" button; `h2` title; description or muted "No description"; status label "Done"/"Pending"; buttons "Edit", "Mark as done"/"Mark as not done" (by current status), and "Delete" (warning color; handler wired in US4)
- [ ] T021 [US2] Wire the detail screen into `src/ui/app.js`: row click → `showDetail(id)`; toggle calls `service.setDone`; "Edit" renders `renderTaskForm` with the task as `initial`, Save calls `service.update`, Cancel returns to detail; "Back" → `showList()`. Make T018 pass

**Checkpoint**: US1 and US2 both work independently

---

## Phase 5: User Story 3 - Filter tasks by status (Priority: P3)

**Goal**: Users filter the list by All, Pending, or Done.

**Independent Test**: With mixed pending/done tasks, select each filter and confirm only matching
tasks show and the active option is highlighted.

### Tests for User Story 3 ⚠️ (write first, must fail)

- [ ] T022 [P] [US3] Add failing unit tests to `tests/unit/task-service.test.js` for `list("pending")` (only `done === false`) and `list("done")` (only `done === true`), both newest first
- [ ] T023 [P] [US3] Write failing acceptance tests in `tests/acceptance/us3-filter.test.js` (seed mixed tasks) mirroring spec US3 scenarios 1–4: "All" is active on open (`aria-pressed="true"`); "Pending" shows only pending tasks and is pressed; "Done" shows only done tasks with `.done` titles; "All" shows all; no matches shows "No tasks match this filter."

### Implementation for User Story 3

- [ ] T024 [US3] Extend `list(filter)` in `src/logic/task-service.js` to support `"pending"` and `"done"`. Make T022 pass
- [ ] T025 [US3] Add the filter group to `src/ui/list-screen.js`: three buttons "All", "Pending", "Done" with `aria-pressed` and accent style on the active one; accept `filter` and `onFilterChange` options; show "No tasks match this filter." when tasks exist but none match
- [ ] T026 [US3] Keep the active filter in memory in `src/ui/app.js` (default `"all"`, not persisted), pass it to `service.list(filter)` and the list screen, and preserve it when returning from the detail screen. Make T023 pass

**Checkpoint**: US1–US3 work independently

---

## Phase 6: User Story 4 - Delete a task (Priority: P4)

**Goal**: Users delete a task after confirming.

**Independent Test**: With one task, open it, delete and confirm, and verify it is gone, including
in a new app instance.

### Tests for User Story 4 ⚠️ (write first, must fail)

- [ ] T027 [P] [US4] Add failing unit tests to `tests/unit/task-service.test.js` for `remove(id)`: removes and persists via `saveAll`; throws `NotFoundError` on unknown id
- [ ] T028 [P] [US4] Write failing acceptance tests in `tests/acceptance/us4-delete.test.js` mirroring spec US4 scenarios 1–3: "Delete" opens a `dialog` with "Delete this task? This cannot be undone." and nothing is deleted yet; confirming "Delete" removes the task and shows the list without it; "Cancel" closes the dialog and keeps the task on the detail screen; deleting the last task shows the empty state

### Implementation for User Story 4

- [ ] T029 [US4] Add `remove(id)` to `src/logic/task-service.js`. Make T027 pass
- [ ] T030 [P] [US4] Implement `src/ui/confirm-dialog.js` exporting `confirmDelete(container)` returning a Promise<boolean>: native `<dialog>` with text "Delete this task? This cannot be undone.", "Delete" button (warning color) resolving `true`, "Cancel" resolving `false`; remove the dialog after closing (fall back to toggling the `open` attribute if `showModal` is unavailable in jsdom)
- [ ] T031 [US4] Wire "Delete" in `src/ui/app.js`: call `confirmDelete`; on `true` call `service.remove(id)` then `showList()`; on `false` stay on detail. Make T028 pass

**Checkpoint**: All four user stories work independently

---

## Phase 7: Polish & Cross-Cutting Concerns

**Purpose**: Edge cases, storage errors, and final validation across stories

- [ ] T032 Add an acceptance test in `tests/acceptance/us1-add-and-view.test.js` for the storage-error edge case: a repository whose `loadAll` throws `StorageError` makes the app show the `role="alert"` banner and does not call `saveAll`; make it pass in `src/ui/app.js` if needed
- [ ] T033 [P] Add an acceptance test in `tests/acceptance/us3-filter.test.js` for the edge case: marking a task done while "Pending" is active removes it from the list after "Back"; make it pass if needed
- [ ] T034 Refactor `src/ui/*.js` and `src/logic/task-service.js` for small single-purpose functions and clear names (Constitution II) with all tests green; confirm no file in `src/ui/` imports from `src/persistence/` (Constitution III)
- [ ] T035 Run `npm test` (all green) and `npm run build`, then walk through every scenario in `specs/001-todo-list-app/quickstart.md`, including 360 px and 1280 px layout checks, 44x44 px targets, and contrast

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies
- **Foundational (Phase 2)**: Depends on Setup; BLOCKS all user stories
- **User Stories (Phases 3–6)**: Depend on Foundational. US2–US4 reuse the list screen and app
  shell from US1, so the recommended order is P1 → P2 → P3 → P4
- **Polish (Phase 7)**: Depends on all user stories

### User Story Dependencies

- **US1 (P1)**: After Foundational; no story dependencies
- **US2 (P2)**: After US1 (opens tasks from list rows; reuses `task-form.js`)
- **US3 (P3)**: After US1 (extends `list-screen.js`); independent of US2
- **US4 (P4)**: After US2 (Delete button lives on the detail screen)

### Within Each User Story

- Tests written and failing first (Red) → minimal implementation (Green) → refactor
- Logic (`task-service.js`) before UI wiring (`app.js`)

### Parallel Opportunities

- Setup: T002, T003, T004 together after T001
- Foundational: T005, T006, T008 together; T007 after T006
- Each story: its unit-test and acceptance-test tasks are [P] together
- US1: T014 (form) in parallel with T013 (service)
- US4: T030 (dialog) in parallel with T029 (service)
- US3 can proceed in parallel with US2 after US1 if two people work on it (different files except
  `app.js` and `task-service.js`, so coordinate those)

---

## Parallel Example: User Story 1

```text
# Write both failing test files together:
Task: "T011 Unit tests for add/list/get in tests/unit/task-service.test.js"
Task: "T012 Acceptance tests for US1 in tests/acceptance/us1-add-and-view.test.js"

# Then implement service and form together:
Task: "T013 TaskService add/list/get in src/logic/task-service.js"
Task: "T014 Task form in src/ui/task-form.js"
```

## Parallel Example: User Story 4

```text
Task: "T027 Unit tests for remove in tests/unit/task-service.test.js"
Task: "T028 Acceptance tests for US4 in tests/acceptance/us4-delete.test.js"
Task: "T030 Confirm dialog in src/ui/confirm-dialog.js"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Phase 1: Setup
2. Phase 2: Foundational
3. Phase 3: US1
4. **STOP and VALIDATE**: `npm test` green; add a task, reload the page, task still listed

### Incremental Delivery

1. Setup + Foundational → app shell with tested persistence
2. US1 → add and view (MVP)
3. US2 → detail, toggle, edit
4. US3 → filters
5. US4 → delete with confirmation
6. Polish → edge cases, refactor, quickstart validation

---

## Notes

- [P] tasks = different files, no dependencies on incomplete tasks
- Every task traces to a spec user story, FR, edge case, or plan decision (Constitution V)
- Do not add anything outside the spec (no accounts, categories, due dates, priorities, search,
  sync)
- Commit after each Green or Refactor step
