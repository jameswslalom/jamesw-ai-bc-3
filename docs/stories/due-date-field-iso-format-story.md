# Story: Add optional dueDate field in ISO YYYY-MM-DD format

## Acceptance Criteria

- Given a task is created or updated
	When no `dueDate` is provided
	Then the task remains valid without a `dueDate` value.
- Given a task is created or updated with `dueDate`
	When `dueDate` is present
	Then the value uses ISO `YYYY-MM-DD` format.
