# Product Requirements Document (PRD) - TODO App Upgrade (Due Dates, Priority, and Filters)

## 1. Overview

The current TODO app has `title` and `completed` only.

Goal: deliver a simple, teachable MVP for the TODO upgrade with no backend changes.

This PRD includes only requirements explicitly confirmed in:
- `docs/artifacts/09162025-requirements-meeting.vtt`
- `docs/artifacts/09172025-slack-conversation-export.txt`

---

## 2. MVP Scope

- Add `dueDate` to each task as an optional field in ISO format `YYYY-MM-DD`.
- Add `priority` to each task as an enum: `P1 | P2 | P3`.
- Set default `priority` to `P3` when not provided.
- Add filters/tabs: **All**, **Today**, **Overdue**.
- Filter behavior:
  - **All** includes completed and incomplete tasks.
  - **Today** and **Overdue** show incomplete tasks only.
- Keep storage local only (no backend or external storage).
- Enforce MVP data/validation rules:
  - `title` is required.
  - `priority` must be one of `P1`, `P2`, `P3`.
  - `dueDate` is optional and must be ISO `YYYY-MM-DD`; invalid values are ignored (treated as absent).

---

## 3. Post-MVP Scope

- Visually highlight overdue tasks.
- Add task sorting with this order:
  - overdue tasks first
  - then by priority (`P1` → `P3`)
  - then by due date ascending
  - tasks without due date last

---

## 4. Out of Scope

- Notifications
- Recurring tasks
- Multi-user
- Keyboard navigation
- Backend changes
- External storage
