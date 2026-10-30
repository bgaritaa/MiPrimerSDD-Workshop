# Feature Specification: Curso-PSWE-07 To-Do List

**Feature Branch**: `001-todo-list-app`

**Created**: 2026-10-22

**Status**: Draft

**Input**: User description: "Build a simple to-do list application named \"Curso-PSWE-07\" that helps users organize and track their daily tasks, with a To-Do List screen (add, open, filter All/Pending/Done) and a To-Do Detail screen (edit, mark done/not done, delete). Tasks have a required title, optional description, and completion status, and persist across application restarts. Clean, minimal, accessible, responsive visual design. Out of scope: accounts, categories, due dates, priorities, search, sync."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Add and view tasks (Priority: P1)

A user opens Curso-PSWE-07, sees their task list, and adds a new task with a title and an
optional description. The task appears in the list immediately and is still there after the
application is closed and reopened.

**Why this priority**: Capturing and seeing tasks is the core value; without it nothing else works.

**Independent Test**: Start with no tasks, add a task, confirm it appears in the list, close and
reopen the application, and confirm it is still listed.

**Acceptance Scenarios**:

1. **Given** no tasks exist, **When** the user opens the To-Do List screen, **Then** the header
   shows "Curso-PSWE-07" and a short friendly empty-state message is shown.
2. **Given** the To-Do List screen, **When** the user selects "Add task", enters a title and
   description, and saves, **Then** the new task appears in the list as not done.
3. **Given** the add-task form, **When** the user saves with an empty title, **Then** a validation
   message is shown and no task is created.
4. **Given** the add-task form, **When** the user enters only a title and saves, **Then** the task
   is created without a description.
5. **Given** the add-task form with entered data, **When** the user cancels, **Then** no task is
   created and the list is unchanged.
6. **Given** tasks exist, **When** the application is closed and reopened, **Then** all tasks are
   shown with the same title, description, and status.

---

### User Story 2 - View and update a task (Priority: P2)

A user opens a task from the list to see its details, marks it as done or not done, and edits its
title or description.

**Why this priority**: Tracking progress (completing tasks) and correcting tasks is the next most
important value after capturing them.

**Independent Test**: With one existing task, open it, toggle its status, edit its title, return
to the list, and confirm the changes are reflected and persist after reopening the application.

**Acceptance Scenarios**:

1. **Given** a task in the list, **When** the user selects its row, **Then** the To-Do Detail
   screen shows the title prominently, the description below it, and a status label "Pending" or
   "Done".
2. **Given** a task with no description, **When** its detail screen is shown, **Then** a muted
   "No description" text is displayed.
3. **Given** a pending task's detail screen, **When** the user selects "Mark as done", **Then** the
   status label changes to "Done" and the action label changes to "Mark as not done".
4. **Given** a done task's detail screen, **When** the user selects "Mark as not done", **Then** the
   status label changes to "Pending" and the action label changes to "Mark as done".
5. **Given** a task's detail screen, **When** the user selects "Edit", changes the title or
   description, and selects "Save", **Then** the detail screen shows the updated values.
6. **Given** edit mode, **When** the user clears the title and selects "Save", **Then** a validation
   message is shown and the task is not changed.
7. **Given** edit mode with changes entered, **When** the user selects "Cancel", **Then** the task
   keeps its previous values.
8. **Given** a task's detail screen, **When** the user uses the back control, **Then** the To-Do
   List screen is shown reflecting any changes.

---

### User Story 3 - Filter tasks by status (Priority: P3)

A user filters the task list to see All tasks, only Pending tasks, or only Done tasks.

**Why this priority**: Filtering helps focus once the list grows, but the app is usable without it.

**Independent Test**: With a mix of pending and done tasks, select each filter option and confirm
only the matching tasks are shown and the active option is highlighted.

**Acceptance Scenarios**:

1. **Given** pending and done tasks exist, **When** the user selects "Pending", **Then** only tasks
   not done are shown and "Pending" is highlighted.
2. **Given** pending and done tasks exist, **When** the user selects "Done", **Then** only done
   tasks are shown, with strikethrough and muted titles, and "Done" is highlighted.
3. **Given** any filter is active, **When** the user selects "All", **Then** all tasks are shown.
4. **Given** no tasks match the active filter, **When** the list is shown, **Then** a short friendly
   empty-state message is displayed.

---

### User Story 4 - Delete a task (Priority: P4)

A user deletes a task they no longer need, after confirming the action.

**Why this priority**: Removing tasks keeps the list tidy but is less critical than adding,
completing, and filtering.

**Independent Test**: With one existing task, open it, delete it, confirm, and verify it no longer
appears in the list, including after reopening the application.

**Acceptance Scenarios**:

1. **Given** a task's detail screen, **When** the user selects "Delete", **Then** a confirmation
   prompt is shown before anything is deleted.
2. **Given** the delete confirmation, **When** the user confirms, **Then** the task is removed and
   the To-Do List screen is shown without it.
3. **Given** the delete confirmation, **When** the user cancels, **Then** the task remains unchanged
   and the detail screen stays open.

---

### Edge Cases

- A title consisting only of spaces is treated as empty and shows the validation message.
- Leading and trailing spaces in titles and descriptions are removed when saved.
- Very long titles or descriptions wrap within the screen instead of overflowing horizontally.
- Deleting the last task, or the last task matching the active filter, shows the empty state.
- Changing a task's status while a filter is active updates the list so it only shows matching
  tasks when the user returns to it.
- If saved tasks cannot be loaded or saved, the user sees a friendly error message and no
  previously saved tasks are silently discarded.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The application MUST display "Curso-PSWE-07" as the application/window title and in
  the header of the To-Do List screen.
- **FR-002**: The To-Do List screen MUST show the user's tasks, each row with a status indicator
  and the task title.
- **FR-003**: Completed tasks MUST appear in the list with a strikethrough, muted-color title.
- **FR-004**: Users MUST be able to open a form from a clearly visible "Add task" button to create a
  task with a title and an optional description.
- **FR-005**: The system MUST reject saving a task whose title is empty or only spaces and MUST
  show a validation message.
- **FR-006**: New tasks MUST be created with the status not done.
- **FR-007**: Users MUST be able to filter the list by All, Pending, or Done, with exactly one
  option active and visually highlighted; All is active when the application opens.
- **FR-008**: The To-Do List screen MUST show a short friendly empty-state message when there are
  no tasks or none match the active filter.
- **FR-009**: Selecting a task row MUST open the To-Do Detail screen for that task.
- **FR-010**: The To-Do Detail screen MUST show a back control, the title prominently, the
  description below it (or a muted "No description" if empty), and a status label "Done" or
  "Pending".
- **FR-011**: The To-Do Detail screen MUST offer Edit, a status toggle labeled "Mark as done" or
  "Mark as not done" according to the current status, and Delete.
- **FR-012**: Edit MUST reuse the add-task form, prefilled with the task's values, with Save and
  Cancel buttons; Cancel MUST discard unsaved changes.
- **FR-013**: Delete MUST use a distinct warning color and MUST ask for confirmation before
  removing the task.
- **FR-014**: All task creations, edits, status changes, and deletions MUST persist so they remain
  after the application is closed and reopened.
- **FR-015**: The interface MUST use a light background, generous white space, one sans-serif font
  family, and a single accent color for primary actions and the active filter; titles MUST be
  larger and bolder than descriptions.
- **FR-016**: Text MUST meet sufficient contrast (WCAG 2.1 AA, at least 4.5:1 for body text) and
  all touch/click targets MUST be at least 44x44 px.
- **FR-017**: The layout MUST adapt to mobile and desktop screen widths without horizontal
  scrolling.
- **FR-018**: The application MUST NOT include accounts, categories, due dates, priorities,
  search, sync, or any capability not described in this specification.

### Key Entities

- **Task**: An item the user wants to track. Attributes: title (required, non-empty text),
  description (optional text), completion status (done / not done).

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A first-time user can add a task in under 30 seconds without instructions.
- **SC-002**: 100% of tasks, including their title, description, and status, are present
  unchanged after closing and reopening the application.
- **SC-003**: Users can mark a task as done in no more than 2 interactions from the To-Do List
  screen.
- **SC-004**: Changing the filter updates the visible list instantly, with no perceptible delay for
  lists of up to 500 tasks.
- **SC-005**: 100% of interactive elements measure at least 44x44 px and all text meets the
  stated contrast ratio, on both a 360 px wide mobile screen and a 1280 px wide desktop screen.
- **SC-006**: At least 90% of test users complete add, complete, edit, filter, and delete tasks on
  their first attempt.

## Assumptions

- The application is used by a single person on a single device; tasks are stored on that device.
- Tasks are listed newest first.
- The active filter is not remembered between sessions; the list opens with All selected.
- Canceling the add-task form discards entered data without confirmation.
- No maximum length is imposed on titles or descriptions beyond what wraps reasonably on screen.
- English is the interface language.
