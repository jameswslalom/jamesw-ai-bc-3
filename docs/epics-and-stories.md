## Epics

### MVP

- Epic: Task Data Model and Validation
- Epic: Date-Based Filtering
- Epic: Local-Only Storage

### Post-MVP

- Epic: Overdue Visual Treatment
- Epic: Task Ordering

## Stories

### MVP

- Epic: Task Data Model and Validation
  - Story: Add optional dueDate field in ISO YYYY-MM-DD format
  - Story: Add priority field with values P1, P2, and P3
  - Story: Default priority to P3 when not provided
  - Story: Require title for task creation
  - Story: Ignore invalid dueDate values as absent

- Epic: Date-Based Filtering
  - Story: Add filter tabs for All, Today, and Overdue
  - Story: Show completed and incomplete tasks in All
  - Story: Show only incomplete tasks in Today
  - Story: Show only incomplete tasks in Overdue

- Epic: Local-Only Storage
  - Story: Keep task storage local only

### Post-MVP

- Epic: Overdue Visual Treatment
  - Story: Visually highlight overdue tasks

- Epic: Task Ordering
  - Story: Sort tasks by overdue first, priority P1 to P3, due date ascending, and undated last

## Acceptance Criteria

### MVP

- Story: Add optional dueDate field in ISO YYYY-MM-DD format
  - Given a task is created or updated, when no `dueDate` is provided, then the task remains valid without a `dueDate` value.
  - Given a task is created or updated with `dueDate`, when `dueDate` is present, then the value uses ISO `YYYY-MM-DD` format.

- Story: Add priority field with values P1, P2, and P3
  - Given a task is created or updated, when `priority` is set, then `priority` is stored on the task.
  - Given `priority` is provided, when the value is validated, then only `P1`, `P2`, or `P3` are accepted.

- Story: Default priority to P3 when not provided
  - Given a new task is created, when no explicit `priority` is provided, then the stored `priority` value is `P3`.

- Story: Require title for task creation
  - Given a user creates a task, when `title` is provided, then the task satisfies the title requirement.
  - Given a user creates a task, when `title` is missing, then the task is not accepted as valid.

- Story: Ignore invalid dueDate values as absent
  - Given a task is created or updated, when no `dueDate` is provided, then the task remains valid.
  - Given a task is created or updated with `dueDate`, when the `dueDate` value is invalid, then the invalid value is ignored and treated as absent.

- Story: Add filter tabs for All, Today, and Overdue
  - Given the task list view is available, when filter options are shown, then the options include `All`, `Today`, and `Overdue`.

- Story: Show completed and incomplete tasks in All
  - Given tasks include completed and incomplete items, when the `All` filter is selected, then completed tasks are shown.
  - Given tasks include completed and incomplete items, when the `All` filter is selected, then incomplete tasks are shown.

- Story: Show only incomplete tasks in Today
  - Given tasks include completed and incomplete items for today, when the `Today` filter is selected, then only incomplete tasks are shown.
  - Given tasks include completed and incomplete items for today, when the `Today` filter is selected, then completed tasks are not shown.

- Story: Show only incomplete tasks in Overdue
  - Given tasks include completed and incomplete overdue items, when the `Overdue` filter is selected, then only incomplete tasks are shown.
  - Given tasks include completed and incomplete overdue items, when the `Overdue` filter is selected, then completed tasks are not shown.

- Story: Keep task storage local only
  - Given tasks are persisted, when persistence behavior is validated, then task storage remains local only.
  - Given the MVP scope, when storage integrations are reviewed, then no backend or external storage is introduced.

### Post-MVP

- Story: Visually highlight overdue tasks
  - Given a task is overdue, when tasks are displayed, then the overdue task is visually highlighted.

- Story: Sort tasks by overdue first, priority P1 to P3, due date ascending, and undated last
  - Given a task list contains overdue and non-overdue tasks, when sorting is applied, then overdue tasks appear first.
  - Given tasks are ordered after overdue precedence, when priorities differ, then tasks are ordered by priority from `P1` to `P3`.
  - Given tasks remain tied after overdue and priority ordering, when due dates are compared, then tasks are ordered by due date ascending.
  - Given tasks have no `dueDate`, when sorting is applied, then undated tasks are placed last.

## Technical Requirements

### MVP

- Story: Add optional dueDate field in ISO YYYY-MM-DD format
  - Frontend form uses HTML date input in `packages/frontend/src/TaskForm.js`; keep date entry/output in `YYYY-MM-DD` and submit through existing `due_date` request field.
  - Backend `POST /api/tasks` and `PUT /api/tasks/:id` in `packages/backend/src/app.js` must continue accepting nullable `due_date` and persist `null` when absent.
  - Backend response payloads must continue returning `due_date` so `TaskList` rendering in `packages/frontend/src/TaskList.js` remains compatible.

- Story: Add priority field with values P1, P2, and P3
  - Add `priority` column to `tasks` table initialization in `packages/backend/src/app.js` with allowed values `P1`, `P2`, `P3`.
  - Extend backend create/update handlers in `packages/backend/src/app.js` to read and persist `priority`.
  - Add priority input control to `packages/frontend/src/TaskForm.js` and include `priority` in payloads sent by `packages/frontend/src/App.js`.
  - Render priority in task rows in `packages/frontend/src/TaskList.js`.

- Story: Default priority to P3 when not provided
  - Set backend default for `priority` to `P3` in database schema and/or create/update handler fallback in `packages/backend/src/app.js`.
  - Initialize frontend form state in `packages/frontend/src/TaskForm.js` to `P3` for new tasks and preserve existing value for edits.
  - Ensure API tests in `packages/backend/__tests__/tasks.test.js` cover default `priority` behavior.

- Story: Require title for task creation
  - Preserve frontend validation in `packages/frontend/src/TaskForm.js` (`title.trim()` required) and block submit when missing.
  - Preserve backend request validation in `packages/backend/src/app.js` for `POST /api/tasks` and `PUT /api/tasks/:id` returning `400` when `title` is missing/blank.
  - Keep API error assertion coverage in `packages/backend/__tests__/tasks.test.js` and `packages/frontend/src/__tests__/App.test.js`.

- Story: Ignore invalid dueDate values as absent
  - Validate incoming `due_date` format in backend create/update handlers in `packages/backend/src/app.js`; if not valid `YYYY-MM-DD`, coerce to `null`.
  - Keep frontend normalization in `packages/frontend/src/TaskForm.js` limited to valid date values and submit empty value when invalid/unset.
  - Ensure returned task objects from backend include `due_date: null` for ignored invalid values.

- Story: Add filter tabs for All, Today, and Overdue
  - Add filter state and tab UI in `packages/frontend/src/TaskList.js` (or `packages/frontend/src/App.js`) with three options: `All`, `Today`, `Overdue`.
  - Reuse existing `GET /api/tasks` fetch flow in `packages/frontend/src/TaskList.js`; add query parameters only if backend filtering is implemented server-side.
  - Update frontend tests in `packages/frontend/src/__tests__/App.test.js` to assert tab rendering.

- Story: Show completed and incomplete tasks in All
  - In `All`, do not apply completion-based exclusion in frontend filtering logic in `packages/frontend/src/TaskList.js`.
  - If server-side filters are introduced, `All` must call `GET /api/tasks` without `completed` restriction in `packages/backend/src/app.js`.

- Story: Show only incomplete tasks in Today
  - Implement filter logic for `due_date` equal to current local date and `completed` false in `packages/frontend/src/TaskList.js` or backend list query in `packages/backend/src/app.js`.
  - Ensure date comparison uses the same `YYYY-MM-DD` basis used by form input and backend storage.
  - Add test coverage for Today filter excluding completed tasks in `packages/frontend/src/__tests__/App.test.js`.

- Story: Show only incomplete tasks in Overdue
  - Implement filter logic for `due_date` before current local date and `completed` false in `packages/frontend/src/TaskList.js` or backend list query in `packages/backend/src/app.js`.
  - Keep tasks with `due_date` null excluded from Overdue results.
  - Add test coverage for Overdue filter excluding completed tasks in `packages/frontend/src/__tests__/App.test.js`.

- Story: Keep task storage local only
  - Replace current backend-backed persistence path (`fetch('/api/tasks')` in `packages/frontend/src/App.js` and `packages/frontend/src/TaskList.js`) with browser-local persistence.
  - Store and read tasks in frontend-only storage in the frontend package, without adding new backend endpoints.
  - Keep backend package unchanged for MVP local-only scope or remove frontend dependency on backend API endpoints.

### Post-MVP

- Story: Visually highlight overdue tasks
  - In `packages/frontend/src/TaskList.js`, add conditional styling for overdue incomplete tasks separate from existing completed-state styling.
  - Determine overdue status from `due_date` compared to current local date using the same date basis as filtering logic.
  - Do not alter backend response contract for visual highlight support.

- Story: Sort tasks by overdue first, priority P1 to P3, due date ascending, and undated last
  - Implement deterministic ordering logic in `packages/frontend/src/TaskList.js` (or backend `GET /api/tasks` query in `packages/backend/src/app.js`) following the exact precedence chain.
  - Map priority values to sort rank (`P1` highest to `P3` lowest) and keep undated tasks after dated tasks.
  - Ensure ordering logic is compatible with current due-date storage format and filter modes.
