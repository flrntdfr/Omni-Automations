# OmniFocus Automation — Examples

Copy-adapt these patterns. All use the `xyz.dufour.of.*` identifier namespace.

## 1. Clear tags from selected tasks (sync, tutorial baseline)

```javascript
/*{
	"type": "action",
	"targets": ["omnifocus"],
	"author": "Dufour, Florent",
	"identifier": "xyz.dufour.of.clear-tags-from-tasks",
	"version": "1.0",
	"description": "Removes all tags from the selected tasks.",
	"label": "Clear Tags from Tasks",
	"shortLabel": "Clear Tags",
	"paletteLabel": "Clear Tags",
	"image": "tag.slash"
}*/
(() => {
	const action = new PlugIn.Action(function(selection, sender) {
		selection.tasks.forEach(task => task.clearTags());
	});
	action.validate = function(selection, sender) {
		return selection.tasks.length > 0;
	};
	return action;
})();
```

## 2. Push due dates +24h (async, mixed selection, preferences)

From [omni-automation.com](https://omni-automation.com/omnifocus/plug-in-push-due-dates-one-day.html) — canonical pattern for date math + Shift preferences + error handling.

Key techniques: `selection.databaseObjects`, `instanceof Project || instanceof Task`, `Calendar.current`, `Preferences`, `Form.show` on `app.shiftKeyDown`.

## 3. Find-or-create tag

```javascript
const tag = tagNamed("Deep Work") || new Tag("Deep Work");
selection.tasks.forEach(task => task.addTag(tag));
```

## 4. Inbox capture with transport text

```javascript
const parsed = Task.byParsingTransportText("Review paper #Research @Mac", true);
// parsed[0] is the new task with project/tag tokens applied
```

## 5. Global action (no selection)

```javascript
action.validate = function(selection, sender) {
	return true;
};

// Example: add task to inbox
new Task("Weekly review", inbox.beginning);
```

## 6. User prompt then act

```javascript
const action = new PlugIn.Action(async function(selection, sender) {
	try {
		const nameField = new Form.Field.String("projectName", "Project name", "");
		const form = new Form();
		form.addField(nameField);
		const result = await form.show("New Project", "Create");
		const name = result.values.projectName.trim();
		if (!name) { return; }
		const folder = folderNamed("Work") || new Folder("Work");
		const project = new Project(name, folder.ending);
	} catch (err) {
		if (!err.causedByUserCancelling) {
			new Alert(err.name, err.message).show();
		}
	}
});
```

## 7. Reveal and select in outline (OF4)

```javascript
const win = document.windows[0];
win.perspective = Perspective.BuiltIn.Projects;
win.selectObjects([project]);
const nodes = win.content.selectedNodes;
if (nodes.length > 0) {
	nodes[0].reveal();
}
```

## 8. Append timestamp to note

```javascript
const fmt = Formatter.Date.withFormat("yyyy-MM-dd HH:mm");
const stamp = fmt.stringFromDate(new Date());
selection.tasks.forEach(task => {
	task.appendStringToNote(`\n---\n${stamp}: processed`);
});
```

## 9. Complete with confirmation

```javascript
const action = new PlugIn.Action(async function(selection, sender) {
	const alert = new Alert("Complete tasks?", `Mark ${selection.tasks.length} task(s) complete?`);
	alert.addOption("Complete");
	alert.addOption("Cancel");
	const idx = await alert.show();
	if (idx === 0) {
		selection.tasks.forEach(t => t.markComplete());
	}
});
action.validate = function(selection, sender) {
	return selection.tasks.length > 0;
};
```

## 10. Export task link as Markdown

```javascript
selection.tasks.forEach(task => {
	const md = `[${task.name}](omnifocus:///task/${task.id.primaryKey})`;
	task.appendStringToNote(`\n${md}`);
});
```

## Category → SF Symbol suggestions

See [images.md](images.md) for the full curated list, repo usage, and rules. Quick picks:

| Category | `image` examples |
|----------|------------------|
| Tags | `tag`, `tag.fill`, `tag.slash.fill` |
| Dates / review | `calendar`, `arrow.triangle.2.circlepath`, `24.circle.fill` |
| Projects | `folder.fill`, `plus.rectangle.on.folder.fill` |
| Tasks | `checkmark.circle.fill`, `plus.circle.fill` |
| Undo / reset | `arrow.uturn.backward.circle.fill`, `bolt.circle.fill` |
| Notes | `note.text`, `doc.text.fill` |
| Export / import | `square.and.arrow.up`, `square.and.arrow.down.fill` |
| Maintenance | `trash.fill`, `tag.slash.fill` |
