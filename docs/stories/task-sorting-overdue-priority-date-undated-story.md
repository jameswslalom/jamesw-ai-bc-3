# Story: Sort tasks by overdue first, priority P1 to P3, due date ascending, and undated last

## Acceptance Criteria

- Given a task list contains overdue and non-overdue tasks
	When sorting is applied
	Then overdue tasks appear first.
- Given tasks are ordered after overdue precedence
	When priorities differ
	Then tasks are ordered by priority from `P1` to `P3`.
- Given tasks remain tied after overdue and priority ordering
	When due dates are compared
	Then tasks are ordered by due date ascending.
- Given tasks have no `dueDate`
	When sorting is applied
	Then undated tasks are placed last.
