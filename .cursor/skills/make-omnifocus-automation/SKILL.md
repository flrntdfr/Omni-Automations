---
name: make-omnifocus-automation
description: >-
  Authors OmniFocus Omni Automation plug-ins (.omnifocusjs) that install and run
  correctly on first try. Use when creating, editing, or debugging OmniFocus
  automations, plug-ins, omnijs scripts, task/project/tag workflows, or anything
  in the OmniAutomations repo. Invoke with make-omnifocus-automation.
---

# OmniFocus Automation (OmniAutomations)

Build **single-file plug-ins** that work one-shot in OmniFocus on macOS, iOS, and iPadOS. Primary reference: [omni-automation.com/omnifocus](https://omni-automation.com/omnifocus/).

## Repo layout

```
plugins/<category>/<action-name>.omnifocusjs
```

- Extension: **`.omnifocusjs`** (required for OmniFocus-only plug-ins).
- Filenames: lowercase, hyphens only — no spaces.
- Identifier namespace: `xyz.dufour.of.<action-name>` (reverse-DNS, unique per action).
- Bump `version` in metadata when changing installed plug-ins.

## One-shot workflow

When the user asks for an automation:

1. **Clarify intent** — selection-based vs global; sync vs async (forms/alerts/files); target objects (task, project, tag, folder, inbox).
2. **Pick template** — sync (simple mutations) or async (user input / file pickers). See [examples.md](examples.md).
3. **Write plug-in** — metadata block + IIFE + `PlugIn.Action` + optional `validate`.
4. **Self-review** with the checklist below before delivering.
5. **Save** to `plugins/<category>/` and tell the user how to install (open file in OmniFocus, or link repo folder in Plug-Ins settings).

Do **not** deliver console-only snippets unless the user explicitly wants a throwaway test. Default deliverable is an installable `.omnifocusjs` file.

## Plug-in skeleton (default)

Use this unless the automation needs no selection (then `validate` returns `true`).

```javascript
/*{
	"type": "action",
	"targets": ["omnifocus"],
	"author": "Dufour, Florent",
	"identifier": "xyz.dufour.of.ACTION-NAME",
	"version": "1.0",
	"description": "What it does.",
	"label": "Menu Title",
	"shortLabel": "Short",
	"paletteLabel": "Toolbar",
	"image": "gearshape.fill"
}*/
(() => {
	const action = new PlugIn.Action(async function(selection, sender) {
		try {
			// --- processing ---
		} catch (err) {
			if (!err.causedByUserCancelling) {
				console.error(err.name, err.message);
				new Alert(err.name, err.message).show();
			}
		}
	});

	action.validate = function(selection, sender) {
		// return true only when preconditions are met
		return selection.tasks.length > 0;
	};

	return action;
})();
```

**Async rule:** use `async function` + `await` whenever calling `Form.show`, `Alert.show`, `FilePicker.show`, `FileSaver.show`, or other Promise-returning APIs. Sync is fine for pure database mutations.

## Metadata (required keys)

| Key | Value |
|-----|-------|
| `type` | `"action"` |
| `targets` | `["omnifocus"]` |
| `author` | `"Dufour, Florent"` |
| `identifier` | `xyz.dufour.of.<unique-name>` |
| `version` | `"1.0"`, increment on updates |
| `description` | Shown in Plug-In management |
| `label` | Automation menu title |
| `shortLabel` / `paletteLabel` | Toolbar labels |
| `image` | SF Symbol name — see [images.md](images.md) |

## Object model (mental map)

```
app                    → Application (platform, modifier keys)
document               → current DatabaseDocument (not app.document)
document.windows[0]    → current window (always index 0)
selection              → passed into action; same as document.windows[0].selection

Database (implicit / selection.database):
  inbox, library, projects, folders, tags
  flattenedTasks, flattenedProjects, flattenedFolders, flattenedTags
  folderNamed(), projectNamed(), tagNamed(), taskNamed()
  projectsMatching(), foldersMatching(), tagsMatching()
  deleteObject(obj), save()
```

**Hierarchy:** Folders → Folders/Projects → Tasks (tasks can nest as action groups). Tags nest as tag groups.

**UI vs API:** OmniFocus UI says "action"; scripts use `Task`.

**Projects:** A project's note/attachments live on its **root task** (`project.appendStringToNote`, etc.).

## Selection handling

`selection` properties for OmniFocus: `tasks`, `projects`, `folders`, `tags`, `allObjects`, `databaseObjects`, `database`, `document`, `window`.

| Pattern | When |
|---------|------|
| `selection.tasks` | Task-only actions |
| `selection.projects` | Project-only actions |
| `selection.databaseObjects` | Mixed task + project (check with `instanceof`) |
| No selection needed | `validate` → `return true` |

```javascript
// Mixed task + project
for (const item of selection.databaseObjects) {
	if (item instanceof Task && item.dueDate) { /* ... */ }
	if (item instanceof Project && item.dueDate) { /* ... */ }
}
```

**Validate must be cheap** — never call `flattenedTasks` or other full-database scans in `validate` (UI lag).

## Common operations

```javascript
// Find or create
const tag = tagNamed("Kitchen") || new Tag("Kitchen");
const task = flattenedTasks.byName("Build Shed") || new Task("Build Shed");
const project = projectNamed("Errands") || new Project("Errands");

// Insert position
new Task("First", inbox.beginning);
new Task("Child", parentTask.beginning);

// Iterate hierarchy
inbox.apply(task => { /* all inbox tasks recursively */ });
tags.apply(tag => { /* all tags */ });

// Tags on task
task.addTag(tag);
task.clearTags();

// Complete / drop
task.markComplete();
task.drop(false);

// Delete
deleteObject(task);

// Parse capture text
Task.byParsingTransportText("Buy milk #Errands @home", false);

// Deep link
const link = `omnifocus:///task/${task.id.primaryKey}`;

// Dates (prefer Calendar API)
const dc = Calendar.current.dateComponentsFromDate(task.dueDate);
dc.day += 1;
task.dueDate = Calendar.current.dateFromDateComponents(dc);

// Window / perspective (OF4+)
const win = document.windows[0];
win.perspective = Perspective.BuiltIn.Inbox;
win.inspectorVisible = false;
win.sidebarVisible = true;

// Reveal in outline (OF4+)
const node = win.content.selectedNodes[0]; // or find via content/sidebar trees
node.reveal();
win.selectObjects([task]);

// Preferences (scoped to plug-in id when constructed with no args)
const prefs = new Preferences();
prefs.write("key", value);
const v = prefs.readBoolean("key"); // null if unset
```

## Forms (async)

```javascript
const field = new Form.Field.String("name", "Label", "default");
const form = new Form();
form.addField(field);
const result = await form.show("Prompt", "Continue");
const value = result.values.name;
```

Checkbox: `Form.Field.Checkbox`. Menu: `Form.Field.Option` with index array + label array. Date: `Form.Field.Date`.

## OmniFocus 4 extras

- **Rich text notes:** `task.note` / `project.note` are `Text` objects — style attributes, links, inline attachments. See [reference.md](reference.md).
- **Node trees:** `document.windows[0].content` and `.sidebar` — `TreeNode` with `reveal()`, `expand()`, `apply()`, `.object` for the underlying Folder/Project/Task/Tag.
- **Quick Open:** installed plug-ins are launchable from Quick Open.

## One-shot checklist (run before delivery)

- [ ] File ends with `.omnifocusjs`; filename has no spaces
- [ ] Metadata JSON is valid inside `/*{ ... }*/`
- [ ] `identifier` is unique and matches `xyz.dufour.of.*`
- [ ] IIFE wraps everything; `return action` is last statement
- [ ] `async` used iff `await` is needed
- [ ] `try/catch` with `err.causedByUserCancelling` guard on async paths
- [ ] `validate` matches real preconditions; no expensive DB scans
- [ ] Correct selection property for object types
- [ ] `instanceof Task` / `instanceof Project` when iterating `databaseObjects`
- [ ] Project note/attachment APIs used on project root task when needed
- [ ] No references to `app.documents` (unsupported)
- [ ] iOS constraints respected (`focus` property is macOS-only; forecast selection differs on iOS)

## Installation (tell the user)

**macOS:** Automation menu → Plug-Ins… → link this repo's `plugins/` folder, or double-click the `.omnifocusjs` file.

**iOS/iPadOS:** Enable Developer Mode (Settings → Automation). Open the file in OmniFocus or add a linked folder in Configure Plug-Ins.

**iOS note:** generic `.omnijs` plug-ins must be copied into a linked folder manually.

## Debugging

1. Reproduce logic in **Automation Console** (Automation menu → Show Console) with small fragments.
2. Use built-in **API Reference** tab for live dictionary lookup.
3. `console.log()` / `console.error()` output appears in the console results pane.
4. For script URLs (`omnifocus://localhost/omnijs-run?script=...`): external execution is **disabled by default** — enable in Automation → Configure → Security. Plug-ins are unaffected.

## Further reading

- **API dictionary (local):** [kb/omnifocus/API-reference.md](../../../kb/omnifocus/API-reference.md)
- API patterns and pitfalls: [reference.md](reference.md)
- SF Symbol names for `image`: [images.md](images.md)
- Complete working plug-ins: [examples.md](examples.md)
- Official tutorial: [omni-automation.com/omnifocus/tutorial/plug-in.html](https://omni-automation.com/omnifocus/tutorial/plug-in.html)
- Plug-in format: [omni-automation.com/plugins/simple.html](https://www.omni-automation.com/plugins/simple.html)
- Big picture: [omni-automation.com/omnifocus/big-picture.html](https://omni-automation.com/omnifocus/big-picture.html)
