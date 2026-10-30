# Quickstart: Curso-PSWE-07 To-Do List

## Prerequisites

- Node.js 20 LTS or newer, npm
- A modern browser (current Chrome, Edge, Firefox, or Safari)

## Setup

```powershell
npm install
```

## Run the automated tests

```powershell
npm test
```

Expected: all unit tests (logic, repository) and acceptance tests (one or more per user story)
pass in a few seconds.

## Run the app

```powershell
npm run dev
```

Open the printed local URL. The browser tab title reads `Curso-PSWE-07`.

## Manual validation scenarios

See [ui-contract.md](contracts/ui-contract.md) for exact labels.

1. **US1 Add and view**: On first launch the empty state is shown. Select "Add task", save with an
   empty title → "Title is required." appears. Enter a title and save → the task is listed as
   Pending. Close the tab, reopen the URL → the task is still listed.
2. **US2 View and update**: Select the task → detail shows title, "No description", status
   "Pending". Select "Mark as done" → status "Done", button "Mark as not done". Select "Edit",
   change the title, "Save" → new title shown. Select "Back" → list shows the done task with a
   strikethrough title.
3. **US3 Filter**: Add a second (pending) task. Select "Pending" → only it is shown and "Pending"
   is highlighted. Select "Done" → only the done task. Select "All" → both.
4. **US4 Delete**: Open a task, select "Delete" → confirmation appears. "Cancel" → task remains.
   "Delete" again and confirm → task is gone from the list, also after reopening.
5. **Layout**: Using browser device tools at 360 px and 1280 px widths, confirm no horizontal
   scrolling and that buttons are at least 44x44 px.

## Build for distribution

```powershell
npm run build
```

Output is a static folder (`dist/`) that can be opened from any static file host.
