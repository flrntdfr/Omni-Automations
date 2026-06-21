# AGENTS.md — OmniAutomations

Guidance for AI agents working in this repository.

## Purpose

**OmniAutomations** is a version-controlled collection of [OmniFocus](https://www.omnifocus.com/) plug-ins built with [Omni Automation](https://omni-automation.com/omnifocus/). Each plug-in is a single `.omnifocusjs` file installed in OmniFocus on macOS, iOS, or iPadOS via **Automation → Plug-Ins…** (link the repo’s `plugins/` folder).

## Authoring plug-ins

Follow conventions from `.cursor/skills/make-omnifocus-automation/`:

| Rule | Value |
|------|-------|
| Layout | `plugins/<category>/<action-name>.omnifocusjs` (e.g. `plugins/bulk/`, `plugins/maintenance/`) |
| Extension | `.omnifocusjs` (OmniFocus-only; required) |
| Filenames | lowercase, hyphens only — no spaces |
| Identifier | `xyz.dufour.of.<action-name>` (reverse-DNS, unique per action) |
| Version | bump `version` in metadata when changing installed plug-ins |
| Deliverable | installable `.omnifocusjs` file — not throwaway console snippets unless explicitly requested |

Default structure: metadata block (`/*{ ... }*/`) + IIFE + `PlugIn.Action` + optional `validate`. Use `async function` + `await` when calling `Form.show`, `Alert.show`, file pickers, or other Promise-returning APIs.

## Project skill

**Location:** `.cursor/skills/make-omnifocus-automation/`

**Use the `make-omnifocus-automation` skill when** creating, editing, or debugging OmniFocus automations, plug-ins, omnijs scripts, task/project/tag workflows, or any work in this repo.

The skill provides:

- One-shot workflow (clarify intent → pick template → write → self-review → save)
- Plug-in skeleton, metadata keys, object model, selection patterns
- Common operations, forms, OmniFocus 4 extras, pre-delivery checklist
- Installation and debugging notes

Companion files in the same directory:

- `reference.md` — API patterns and pitfalls
- `examples.md` — complete working plug-ins

## Reference documentation (read these first)

When generating OmniFocus automation code, **rely on this repo and its local references — not memory or external web search alone.**

### Primary: `kb/omnifocus/` (offline API dictionary)

- **`kb/omnifocus/API-reference.md`** — official OmniFocus API reference exported from OmniFocus (Markdown). See [`kb/omnifocus/README.md`](kb/omnifocus/README.md) for attribution and disclaimer. Search for class names, properties, and method signatures before writing or changing API calls.

### Secondary

| Source | Use for |
|--------|---------|
| `.cursor/skills/make-omnifocus-automation/SKILL.md` | Conventions, workflow, checklist |
| `.cursor/skills/make-omnifocus-automation/reference.md` | Patterns, pitfalls, OF4 notes |
| `.cursor/skills/make-omnifocus-automation/examples.md` | Full plug-in templates |
| Existing plug-ins under `plugins/` | In-repo patterns that already work |
| [omni-automation.com/omnifocus](https://omni-automation.com/omnifocus/) | Tutorials and big-picture context only when local refs are insufficient |

## Workflow

1. **Read** `kb/omnifocus/API-reference.md` for the APIs you need.
2. **Follow** `.cursor/skills/make-omnifocus-automation/SKILL.md` (and `examples.md` / `reference.md` as needed).
3. **Write** the plug-in to `plugins/<category>/<action-name>.omnifocusjs`.
4. **Self-review** with the skill’s one-shot checklist (metadata, async rules, `validate`, selection types, no expensive DB scans in `validate`).
5. **Update** `README.md` — add or edit a row in the **Automations** table (plug-in path, menu title, description).

## Current plug-ins

| File | Menu title | Description |
|------|------------|-------------|
| `plugins/bulk/set-review-interval.omnifocusjs` | Set Review Interval | Set “Review every” cadence for selected projects and projects in selected folders; remembers last choice. |
| `plugins/bulk/undo.omnifocusjs` | undø | Activate selected folders, projects, and tasks: set projects active, clear defer/due/flagged on tasks, mark completed tasks incomplete. Logs dropped projects and tasks for manual restore. |
| `plugins/maintenance/prune-empty-tags.omnifocusjs` | Prune Empty Tags | Delete tags with no remaining tasks; log or delete after confirmation; skips Forecast tag. |
| `plugins/reporting/database-stats.omnifocusjs` | Database Stats | Comprehensive database statistics with summary alert and full Automation Console report. |

## Install (for users)

1. OmniFocus → **Automation → Plug-Ins…** → **Add Linked Folder** → choose this repo’s `plugins/` directory.
2. On iOS/iPadOS, enable **Developer Mode** under Settings → Automation if the Automation menu is not visible.
