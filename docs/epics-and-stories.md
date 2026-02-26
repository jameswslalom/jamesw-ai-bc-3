## MVP

- Epic: Task Data Model and Validation
  - Story: Add optional dueDate field in ISO YYYY-MM-DD format
    - Technical Requirements:
      - Frontend form uses HTML date input in `packages/frontend/src/TaskForm.js`; keep date entry/output in `YYYY-MM-DD` and submit through existing `due_date` request field.
      - Backend `POST /api/tasks` and `PUT /api/tasks/:id` in `packages/backend/src/app.js` must continue accepting nullable `due_date` and persist null when absent.
      - Backend response payloads must continue returning `due_date` so `TaskList` rendering in `packages/frontend/src/TaskList.js` remains compatible.
  - Story: Add priority field with values P1, P2, and P3
    - Technical Requirements:
      - Add `priority` column to `tasks` table initialization in `packages/backend/src/app.js` with allowed values `P1`, `P2`, `P3`.
      - Extend backend create/update handlers in `packages/backend/src/app.js` to read and persist `priority`.
      - Add priority input control to `packages/frontend/src/TaskForm.js` and include `priority` in payloads sent by `packages/frontend/src/App.js`.
      - Render priority in task rows in `packages/frontend/src/TaskList.js`.
  - Story: Default priority to P3 when not provided
    - Technical Requirements:
      - Set backend default for `priority` to `P3` in database schema and/or create/update handler fallback in `packages/backend/src/app.js`.
      - Initialize frontend form state in `packages/frontend/src/TaskForm.js` to `P3` for new tasks and preserve existing value for edits.
      - Ensure API tests in `packages/backend/__tests__/tasks.test.js` cover default `priority` behavior.
  - Story: Require title for task creation
    - Technical Requirements:
      - Preserve frontend validation in `packages/frontend/src/TaskForm.js` (`title.trim()` required) and blocking submit when missing.
      - Preserve backend request validation in `packages/backend/src/app.js` for `POST /api/tasks` and `PUT /api/tasks/:id` returning `400` when `title` is missing/blank.
      - Keep API error assertion coverage in tests (`packages/backend/__tests__/tasks.test.js` and `packages/frontend/src/__tests__/App.test.js`) for invalid create flows.
  - Story: Ignore invalid dueDate values as absent
    - Technical Requirements:
      - Validate incoming `due_date` format in backend create/update handlers in `packages/backend/src/app.js`; if not valid `YYYY-MM-DD`, coerce to `null`.
      - Keep frontend normalization in `packages/frontend/src/TaskForm.js` limited to valid date values and submit empty value when invalid/unset.
      - Ensure returned task objects from backend include `due_date: null` for ignored invalid values.

- Epic: Date-Based Filtering
  - Story: Add filter tabs for All, Today, and Overdue
    - Technical Requirements:
      - Add filter state and tab UI in `packages/frontend/src/TaskList.js` (or parent container in `packages/frontend/src/App.js`) with three options: `All`, `Today`, `Overdue`.
      - Reuse existing `GET /api/tasks` fetch flow in `packages/frontend/src/TaskList.js`; add query parameters only if backend filtering is implemented server-side.
      - Update frontend tests in `packages/frontend/src/__tests__/App.test.js` to assert tab rendering.
  - Story: Show completed and incomplete tasks in All
    - Technical Requirements:
      - In `All`, do not apply completion-based exclusion in frontend filtering logic in `packages/frontend/src/TaskList.js`.
      - If server-side filters are introduced, `All` must call `GET /api/tasks` without `completed` restriction in `packages/backend/src/app.js`.
  - Story: Show only incomplete tasks in Today
    - Technical Requirements:
      - Implement filter logic that matches tasks with `due_date` equal to current local date and `completed` false in `packages/frontend/src/TaskList.js` or backend list query in `packages/backend/src/app.js`.
      - Ensure date comparison uses the same `YYYY-MM-DD` basis used by `TaskForm` and backend storage.
      - Add test coverage for Today filter excluding completed tasks in `packages/frontend/src/__tests__/App.test.js`.
  - Story: Show only incomplete tasks in Overdue
    - Technical Requirements:
      - Implement filter logic that matches tasks with `due_date` before current local date and `completed` false in `packages/frontend/src/TaskList.js` or backend list query in `packages/backend/src/app.js`.
      - Keep tasks with `due_date` null excluded from Overdue results.
      - Add test coverage for Overdue filter excluding completed tasks in `packages/frontend/src/__tests__/App.test.js`.

- Epic: Local-Only Storage
  - Story: Keep task storage local only
    - Technical Requirements:
      - Replace current backend-backed persistence path (`fetch('/api/tasks')` in `packages/frontend/src/App.js` and `packages/frontend/src/TaskList.js`) with browser-local persistence.
      - Store and read tasks in frontend-only storage in the frontend package, without adding new backend endpoints.
      - Keep backend package unchanged for MVP local-only scope or remove frontend dependency on backend API endpoints.

## Post-MVP

- Epic: Overdue Visual Treatment
  - Story: Visually highlight overdue tasks
    - Technical Requirements:
      - In `packages/frontend/src/TaskList.js`, add conditional styling for overdue incomplete tasks separate from existing completed-state styling.
      - Determine overdue status from `due_date` compared to current local date using the same date basis as filtering logic.
      - Do not alter backend response contract for visual highlight support.

- Epic: Task Ordering
  - Story: Sort tasks by overdue first, priority P1 to P3, due date ascending, and undated last
    - Technical Requirements:
      - Implement deterministic ordering logic in `packages/frontend/src/TaskList.js` (or backend `GET /api/tasks` query in `packages/backend/src/app.js`) following the exact precedence chain.
      - Map priority values to sort rank (`P1` highest to `P3` lowest) and keep undated tasks after dated tasks.
      - Ensure ordering logic is compatible with current due-date storage format and filter modes.

## Implementation Sequencing

Legend: `Complexity` = `S | M | L`, `Owner` = `Frontend | Backend | Shared`

### Phase 1: MVP Foundation (Data + Validation)

1. Story: Require title for task creation (`Complexity: S`, `Owner: Shared`)
2. Story: Add optional dueDate field in ISO YYYY-MM-DD format (`Complexity: M`, `Owner: Shared`)
3. Story: Ignore invalid dueDate values as absent (`Complexity: M`, `Owner: Shared`)
4. Story: Add priority field with values P1, P2, and P3 (`Complexity: L`, `Owner: Shared`)
5. Story: Default priority to P3 when not provided (`Complexity: S`, `Owner: Shared`)

### Phase 2: MVP User Experience (Filtering + Storage)

6. Story: Keep task storage local only (`Complexity: L`, `Owner: Frontend`)
7. Story: Add filter tabs for All, Today, and Overdue (`Complexity: M`, `Owner: Frontend`)
8. Story: Show completed and incomplete tasks in All (`Complexity: S`, `Owner: Frontend`)
9. Story: Show only incomplete tasks in Today (`Complexity: M`, `Owner: Frontend`)
10. Story: Show only incomplete tasks in Overdue (`Complexity: M`, `Owner: Frontend`)

### Phase 3: Post-MVP Enhancements

11. Story: Visually highlight overdue tasks (`Complexity: S`, `Owner: Frontend`)
12. Story: Sort tasks by overdue first, priority P1 to P3, due date ascending, and undated last (`Complexity: M`, `Owner: Frontend`)
