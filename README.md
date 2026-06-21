# Omni Automations

My [Omni Automations](https://omni-automation.com/omnifocus/) for [OmniFocus](https://www.omnifocus.com/).

## Automations

| Plug-in | Description |
|---------|-------------|
| [set-review-interval](plugins/bulk/set-review-interval.omnifocusjs) | Set the “Review every” cadence for selected projects and all projects in selected folders (days, weeks, months, or years). Remembers your last choice. |
| [undo](plugins/bulk/undo.omnifocusjs) | Activate selected folders, projects, and tasks: set projects to active, clear defer/due/flagged on tasks, mark completed tasks incomplete. Logs dropped projects and tasks that must be restored manually. |
| [prune-empty-tags](plugins/maintenance/prune-empty-tags.omnifocusjs) | Delete tags with no remaining tasks (bottom-up through tag groups). Log candidates first or delete after confirmation. Skips the Forecast tag. |
| [database-stats](plugins/reporting/database-stats.omnifocusjs) | Comprehensive database statistics: projects, tasks, tags, folders, workload estimates, review backlog, busiest projects, and health ratios. Summary alert plus full report in the Automation Console. |

## References

- Local API dictionary: [kb/omnifocus/API-reference.md](kb/omnifocus/API-reference.md) ([attribution & disclaimer](kb/omnifocus/README.md))
- [Omni Automation — OmniFocus](https://omni-automation.com/omnifocus/)
- Project skill for authoring plug-ins: `.cursor/skills/make-omnifocus-automation/`
