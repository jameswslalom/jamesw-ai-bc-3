# Story: Require title for task creation

## Acceptance Criteria

- Given a user creates a task
	When `title` is provided
	Then the task satisfies the title requirement.
- Given a user creates a task
	When `title` is missing
	Then the task is not accepted as valid.
