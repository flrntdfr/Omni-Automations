# OmniFocus Automation — API Reference Notes

Condensed from [omni-automation.com](https://omni-automation.com) and the built-in OmniFocus API dictionary. Read when implementing non-trivial automations.

## Global shortcuts

| Symbol | Meaning |
|--------|---------|
| `app` | Application object |
| `document` | Current database document |
| `inbox`, `tags`, `projects`, `folders`, `library` | Top-level database collections |
| `flattenedTasks`, etc. | Entire hierarchy flattened to arrays |
| `this` | Current database when calling `save()`, etc. |

## Class hierarchy (database)

```
DatabaseObject
  └─ DatedObject (added, modified)
       └─ ActiveObject (active)
            ├─ Folder
            ├─ Tag
            └─ Task
  └─ Project (note/attachments on root task)
```

## Task — key properties

| Property | Notes |
|----------|-------|
| `name` | Title |
| `note` | String (plain) or `Text` (OF4 rich text) |
| `flagged` | Boolean |
| `completed` | Boolean (read-only; use `markComplete` / `markIncomplete`) |
| `taskStatus` | `Task.Status.Available`, `.Blocked`, `.Completed`, `.Dropped`, `.DueSoon`, `.Next` |
| `dueDate`, `deferDate` | `Date` or null |
| `dueDateIncludesTime`, `deferDateIncludesTime` | Boolean |
| `tags` | Array of Tag |
| `children` / `tasks` | Child tasks (action groups) |
| `parent` | Parent Task, Project, or Inbox |
| `project` | Containing project (null if inbox-only) |
| `repetitionRule` | `Task.RepetitionRule` or null |
| `assignedContainer` | Tentative project/parent for inbox items |
| `id.primaryKey` | Stable id for URLs |

## Task — key methods

`addTag`, `addTags`, `removeTag`, `removeTags`, `clearTags`, `markComplete(date?)`, `markIncomplete`, `drop(allOccurrences)`, `appendStringToNote`, `addAttachment`, `addLinkedFileURL`, `apply(fn)`, `taskNamed` / `childNamed`.

Constructor: `new Task(name, position?)` — position = Project, Task, or `ChildInsertionLocation` (`inbox.beginning`, `parent.before`, etc.).

## Project — key properties

`name`, `status` (`Project.Status.Active`, `.OnHold`, `.Done`, `.Dropped`), `tasks`, `folder`, `dueDate`, `deferDate`, `flagged`, `reviewInterval`, `completed`, `singleActions` (non-sequential).

Constructor: `new Project(name, folderOrLocation?)`.

## Tag

`name`, `status` (`Tag.Status.Active`, `.OnHold`, `.Dropped`), `children`, `parent`.

Constructor: `new Tag(name, parentOrLocation?)`.

## Folder

Contains folders and projects. `status`: `Folder.Status.Active` or `.Dropped`.

## Database search & mutation

```javascript
folderNamed("X")      // top-level only
projectNamed("X")
tagNamed("X")
taskNamed("X")        // top-level inbox only
flattenedTasks.byName("X")  // anywhere in hierarchy

projectsMatching("query")
foldersMatching("query")
tagsMatching("query")

deleteObject(databaseObject)
this.save()
moveTags(tags, task, position)  // reorder tags on task
```

`apply(function)` on `library`, `projects`, `folders`, `inbox`, `tags` — recurses into children. Callback may return `ApplyResult` to prune traversal.

## Selection

```javascript
document.windows[0].selection.tasks
document.windows[0].selection.projects
document.windows[0].selection.databaseObjects  // heterogeneous
document.windows[0].selection.database       // Database reference
```

`DocumentWindow.selectObjects([...])` — replaces selection; objects must be visible in current perspective.

## Perspectives

```javascript
const win = document.windows[0];
win.perspective = Perspective.BuiltIn.Inbox;    // Flagged, Forecast, Inbox, Nearby, Projects, Review, Tags
win.perspective = Perspective.Custom.byName("My Perspective");
win.perspective.name;
```

## Window & trees (OmniFocus 4+)

```javascript
const win = document.windows[0];
win.content          // ContentTree — main outline
win.sidebar          // SidebarTree
win.inspectorVisible // Boolean
win.sidebarVisible   // Boolean
win.focus            // macOS only — SectionArray of focused folders/projects

// TreeNode
node.object          // Folder | Project | Task | Tag
node.reveal()
node.expand(true)    // completely
node.collapse()
node.apply(fn)       // walk subtree
node.isExpanded
node.children
```

## Text / rich notes (OmniFocus 4+)

Notes on tasks and projects can be manipulated as `Text`:

```javascript
const text = task.note;           // Text object
text.string;                      // plain string content
text.insert(stringOrObject, pos); // Text.Position
// Style: text.style, Style.Attribute.*
// Links: Markdown ↔ link objects via dedicated plug-in patterns
```

For simple append, `appendStringToNote("line\n")` still works.

## Dates & formatters

```javascript
const cal = Calendar.current;
const dc = cal.dateComponentsFromDate(someDate);
dc.hour += 24;
const newDate = cal.dateFromDateComponents(dc);

const fmt = Formatter.Date.withFormat("yyyy-MM-dd");
fmt.stringFromDate(new Date());
```

## Forms

| Field | Constructor |
|-------|-------------|
| String | `new Form.Field.String(key, label, default?)` |
| Password | `new Form.Field.Password(key, label)` |
| Checkbox | `new Form.Field.Checkbox(key, label, default?)` |
| Date | `new Form.Field.Date(key, label, default?)` |
| Option menu | `new Form.Field.Option(key, label, indices, labels, defaultIndex)` |
| Multi-select | `new Form.Field.MultipleOptions(...)` |

```javascript
const form = new Form();
form.addField(field);
const result = await form.show("Title", "OK Button");
result.values.fieldKey;
```

## Files

```javascript
const picker = new FilePicker();
picker.folders = true;
picker.types = [TypeIdentifier.plainText];
const urls = await picker.show("Choose file", "Select");
// FileSaver, FileWrapper, document.makeFileWrapper(name, typeId)
```

## URLs & HTTP

```javascript
const url = URL.fromString("https://...");
const req = URL.FetchRequest.fromString("https://api.example.com/data");
req.method = "GET";
const response = await req.fetch();
response.bodyString;

// x-callback / app links
URL.fromString("drafts://...").open();
```

## Credentials & preferences

```javascript
const prefs = new Preferences();           // scoped to plug-in identifier
prefs.readString("k"); prefs.write("k", v);
prefs.readBoolean("k"); prefs.readNumber("k");

Credentials.read("service");  // Keychain
```

## PlugIn metadata images

`image` metadata value is an SF Symbol name. In `validate`, `sender.image = Image.symbolNamed("star.fill")` can reflect dynamic state (toolbar/menu).

## Modifier keys in actions

`app.shiftKeyDown`, `app.controlKeyDown`, `app.optionKeyDown`, `app.commandKeyDown` — useful for alternate behavior (e.g. preferences on Shift).

## Common pitfalls (cause "doesn't work first time")

1. **Forgot `async`** on action when using `await` — silent failure or unhandled Promise.
2. **Wrong selection type** — e.g. using `selection.tasks` when user selected a project.
3. **`taskNamed` vs `flattenedTasks.byName`** — former is inbox top-level only.
4. **Project notes** — use `project.appendStringToNote` or root task's note, not a fictional `project.note` string setter in all cases.
5. **Validate too strict** — plug-in greyed out; match actual UX (multi-select, projects, or empty selection).
6. **Validate too heavy** — `flattenedTasks.length` in validate freezes UI.
7. **Missing `return action`** — plug-in won't load.
8. **Invalid metadata JSON** — trailing commas, unquoted keys break parsing.
9. **Duplicate identifier** — install fails or replaces wrong plug-in; always use unique `xyz.dufour.of.*`.
10. **`err.causedByUserCancelling`** — use `err.causedByUserCancelling`, not bare `causedByUserCancelling`.
11. **iOS `focus`** — throws on iOS; guard with `Device.current.mac` or `app.platformName`.
12. **Forecast APIs** — require Forecast perspective active.
13. **Sequential projects** — task availability differs; check `taskStatus` not just dates.
14. **Repeating tasks** — `markComplete` clones; handle return value if needed.

## Script URLs (not plug-ins)

Format: `omnifocus://localhost/omnijs-run?script=<url-encoded-js>`

Optional: `&arg=<encoded>` for parameterized approved scripts.

External URLs require user approval + Security settings. Prefer `.omnifocusjs` plug-ins for repo automations.

## Official API dictionary

**Local (primary):** [`kb/omnifocus/API-reference.md`](../../../kb/omnifocus/API-reference.md) — exported from OmniFocus.

**In-app:** Automation → API Reference (macOS) or Console → API Reference tab (iOS).

**Online fallback:** [omni-automation.com/omnifocus/OF-API.html](https://omni-automation.com/omnifocus/OF-API.html)
