# Data Model: Curso-PSWE-07 To-Do List

## Entity: Task

| Field | Type | Required | Rules | Source |
|-------|------|----------|-------|--------|
| `id` | string (UUID) | yes | Generated on creation; unique; never changes | Needed to open/edit/delete (research §6) |
| `title` | string | yes | Trimmed; MUST NOT be empty after trimming | Spec Key Entities, FR-005 |
| `description` | string | no | Trimmed; empty string means "no description" | Spec Key Entities, FR-010 |
| `done` | boolean | yes | `false` on creation | FR-006 |
| `createdAt` | number (ms since epoch) | yes | Set on creation; never changes; used for newest-first order | Spec Assumptions |

There are no relationships; Task is the only entity.

## Validation

- `title.trim() === ""` → reject with validation message "Title is required." (FR-005).
- Leading/trailing whitespace is removed from `title` and `description` before saving.
- No maximum length (Spec Assumptions).

## State transitions

```text
          create (done = false)
               │
               ▼
   ┌────────► Pending ──── Mark as done ────► Done
   │           ▲                               │
   │           └──── Mark as not done ─────────┘
   │
   └── Edit (title/description) keeps the current status
Pending or Done ── Delete (confirmed) ──► removed
```

## Filter (view state, not persisted)

| Value | Shows |
|-------|-------|
| `all` (default on open) | every task |
| `pending` | tasks with `done === false` |
| `done` | tasks with `done === true` |

## Stored format

`localStorage` key `curso-pswe-07.tasks` holds a JSON array of Task objects:

```json
[
  { "id": "6f1c…", "title": "Buy milk", "description": "", "done": false, "createdAt": 1761130000000 }
]
```

Missing key → empty list. Invalid JSON → storage error (data left untouched; research §10).
