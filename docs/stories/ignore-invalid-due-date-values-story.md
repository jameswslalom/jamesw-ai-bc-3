# Story: Ignore invalid dueDate values as absent

## Acceptance Criteria

- Given a task is created or updated
	When no `dueDate` is provided
	Then the task remains valid.
- Given a task is created or updated with `dueDate`
	When the `dueDate` value is invalid
	Then the invalid value is ignored and treated as absent.
