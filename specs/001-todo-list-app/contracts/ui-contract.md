# Contract: User Interface

Visible text and accessible roles that acceptance tests rely on. Tests locate elements by role
and visible text, not by CSS classes.

## Global

- Document title: `Curso-PSWE-07` (FR-001).
- Storage error banner (`role="alert"`): "Your tasks could not be loaded or saved. Please try
  again."

## To-Do List screen

| Element | Role / text | Spec |
|---------|-------------|------|
| Header | `heading` level 1, "Curso-PSWE-07" | FR-001 |
| Filter | group of 3 buttons "All", "Pending", "Done"; active one has `aria-pressed="true"` and accent style | FR-007 |
| Add button | `button` "Add task" | FR-004 |
| Task row | `button` per task containing status indicator and title; done rows have strikethrough, muted title | FR-002, FR-003, FR-009 |
| Status indicator | accessible label "Done" or "Pending" | FR-002 |
| Empty state (no tasks) | "No tasks yet. Add your first one!" | FR-008 |
| Empty state (no match) | "No tasks match this filter." | FR-008 |

## Task form (add and edit)

| Element | Role / text | Spec |
|---------|-------------|------|
| Title field | `textbox` labeled "Title" | FR-004 |
| Description field | multi-line `textbox` labeled "Description" | FR-004 |
| Save | `button` "Save" | FR-012 |
| Cancel | `button` "Cancel" | FR-012 |
| Validation message | "Title is required." shown near Title when saving an empty title | FR-005 |

In edit mode the fields are prefilled with the task's current values.

## To-Do Detail screen

| Element | Role / text | Spec |
|---------|-------------|------|
| Back control | `button` "Back" | FR-010 |
| Title | `heading` level 2 with task title | FR-010 |
| Description | task description, or muted "No description" | FR-010 |
| Status label | "Done" or "Pending" | FR-010 |
| Edit | `button` "Edit" | FR-011 |
| Status toggle | `button` "Mark as done" (pending) or "Mark as not done" (done) | FR-011 |
| Delete | `button` "Delete", warning color | FR-013 |

## Delete confirmation dialog

| Element | Role / text |
|---------|-------------|
| Dialog | `dialog` with text "Delete this task? This cannot be undone." |
| Confirm | `button` "Delete" (warning color) |
| Cancel | `button` "Cancel" |

## Layout rules

- Every interactive element measures at least 44x44 px (FR-016).
- Single column, max width 640 px, no horizontal scrolling from 360 px upward (FR-017).
