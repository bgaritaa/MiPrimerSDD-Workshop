# Research: Curso-PSWE-07 To-Do List

The spec defines no technology. Each decision below picks the simplest option that satisfies the
spec and the constitution (Simplicity First, Structured Architecture, TDD).

## 1. Platform and project type

- **Decision**: Static, single-page web application running in a modern browser.
- **Rationale**: One codebase covers mobile and desktop responsive layouts (FR-017); the browser
  tab title satisfies the window-title requirement (FR-001); no server is needed.
- **Alternatives considered**: Native mobile app (two platforms, heavier toolchain); desktop
  wrapper such as Electron (large dependency, desktop only); server-rendered app (needs hosting,
  not required by spec).

## 2. Language and UI approach

- **Decision**: Plain JavaScript (ES2022 modules), HTML, and CSS. No UI framework.
- **Rationale**: Two screens and one form do not justify a framework; fewer dependencies (YAGNI).
- **Alternatives considered**: React/Vue/Svelte (extra build and runtime complexity for a
  two-screen app); TypeScript (adds a compile step not required by the spec).

## 3. Persistence

- **Decision**: Browser `localStorage`, one JSON array under the key `curso-pswe-07.tasks`, hidden
  behind a `TaskRepository` interface.
- **Rationale**: Survives closing and reopening the app (FR-014); synchronous and simple; ample
  capacity for 500+ short tasks (SC-004). The interface allows an in-memory fake in tests
  (Constitution III).
- **Alternatives considered**: IndexedDB (asynchronous API, more code, no needed capability);
  remote backend (sync and accounts are out of scope, FR-018).

## 4. Testing

- **Decision**: Vitest as test runner with jsdom as DOM environment. Unit tests for logic;
  acceptance tests drive the real UI against an in-memory repository.
- **Rationale**: Fast, zero-config ES module support; jsdom lets acceptance tests mirror the
  spec's Given/When/Then scenarios through the UI (Constitution IV).
- **Alternatives considered**: Node built-in `node:test` (no DOM, still needs jsdom and manual
  wiring); Playwright end-to-end (real browser, slower, heavier install — not needed for the
  acceptance level required).

## 5. Dev server and build

- **Decision**: Use Vite, which Vitest already depends on, as dev server (`npm run dev`) and for
  a static production build (`npm run build`).
- **Rationale**: Browsers block ES modules over `file://`; Vite is already installed with Vitest,
  so it adds no new dependency.
- **Alternatives considered**: A separate static server package (an extra dependency); bundling
  everything into one non-module script (loses module separation of layers).

## 6. Task identity and ordering

- **Decision**: Each task gets an `id` from `crypto.randomUUID()` and a `createdAt` timestamp.
  The list is sorted newest first by `createdAt`.
- **Rationale**: An id is required to open, edit, and delete a specific task; `createdAt`
  implements the spec assumption "Tasks are listed newest first".
- **Alternatives considered**: Array index as id (breaks after deletion); an incrementing counter
  (needs extra stored state).

## 7. Navigation between screens

- **Decision**: In-page view switching: the app renders either the List screen or the Detail
  screen for a task id, with the add/edit form shown in place. No router library; no URL routes.
- **Rationale**: Two screens need no routing library (YAGNI). The back control returns to the
  list.
- **Alternatives considered**: Hash-based routes (browser back button support is not required by
  the spec); a router library (unneeded dependency).

## 8. Delete confirmation

- **Decision**: An in-page confirmation dialog using the native `<dialog>` element with
  "Delete" (warning color) and "Cancel" buttons.
- **Rationale**: Meets FR-013 with consistent styling and 44x44 px targets; testable in jsdom by
  checking the dialog's open state.
- **Alternatives considered**: `window.confirm()` (cannot be styled to meet the warning-color and
  target-size requirements).

## 9. Visual design and accessibility

- **Decision**: One CSS file with custom properties: light background `#fafafa`, text `#1f2937`,
  muted text `#4b5563`, accent `#2563eb`, warning `#b91c1c`, system sans-serif font stack,
  minimum target size 44x44 px, single-column layout capped at 640 px wide.
- **Rationale**: These colors meet WCAG 2.1 AA 4.5:1 on the light background (FR-016); a capped
  single column works on 360 px mobile and 1280 px desktop (SC-005).
- **Alternatives considered**: A CSS framework (unneeded dependency).

## 10. Storage failure handling

- **Decision**: If stored data cannot be read or written, the UI shows a friendly error banner.
  Unreadable stored data is left untouched (never overwritten automatically).
- **Rationale**: Implements the spec edge case "no previously saved tasks are silently
  discarded".
- **Alternatives considered**: Resetting storage on parse error (silently discards data).
