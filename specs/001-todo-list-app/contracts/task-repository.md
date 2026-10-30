# Contract: TaskRepository (persistence interface)

The logic layer depends only on this interface. Two implementations exist:
`LocalStorageTaskRepository` (production) and `InMemoryTaskRepository` (tests).

| Method | Input | Output | Errors |
|--------|-------|--------|--------|
| `loadAll()` | — | `Task[]` (copy; stored order) | Throws `StorageError` if stored data is unreadable |
| `saveAll(tasks)` | `Task[]` | — | Throws `StorageError` if data cannot be written |

Rules:

- `loadAll()` on empty storage returns `[]`.
- `loadAll()` after `saveAll(tasks)` returns tasks equal to `tasks` (including after reopening the
  app, for the localStorage implementation).
- Neither method modifies the arguments or returned arrays shared with callers.
- On unreadable data, `loadAll()` MUST NOT overwrite or clear the stored value.

# Contract: TaskService (application logic)

Used by the UI layer. Validates input and calls the repository.

| Method | Input | Output | Errors |
|--------|-------|--------|--------|
| `list(filter)` | `"all" \| "pending" \| "done"` | `Task[]` newest first | — |
| `get(id)` | task id | `Task` or `undefined` | — |
| `add({ title, description })` | strings | created `Task` (`done: false`) | `ValidationError` if title empty after trim |
| `update(id, { title, description })` | id, strings | updated `Task` (status unchanged) | `ValidationError`; `NotFoundError` |
| `setDone(id, done)` | id, boolean | updated `Task` | `NotFoundError` |
| `remove(id)` | id | — | `NotFoundError` |

Every mutating method persists via `saveAll` before returning; `StorageError` propagates to the
UI, which shows the error banner.
