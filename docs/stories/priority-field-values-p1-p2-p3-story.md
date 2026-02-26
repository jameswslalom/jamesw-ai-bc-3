# Story: Add priority field with values P1, P2, and P3

## Acceptance Criteria

- Given a task is created or updated
	When `priority` is set
	Then `priority` is stored on the task.
- Given `priority` is provided
	When the value is validated
	Then only `P1`, `P2`, or `P3` are accepted.
