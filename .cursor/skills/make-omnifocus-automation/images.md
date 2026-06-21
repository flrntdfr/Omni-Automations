# Plug-in `image` metadata — SF Symbols

The `image` key in plug-in frontmatter is an **Apple SF Symbol name** (a string). OmniFocus resolves it via `Image.symbolNamed(name)` (see `kb/omnifocus/API-reference.md` → `Image.symbolNamed`).

There is **no closed list** enforced by Omni Automation: any valid SF Symbol name for the OS version running OmniFocus may work. Names are case-sensitive, use dots as separators, and often end in `.fill` for solid toolbar/menu icons.

Browse the full catalog in Apple’s free **SF Symbols** app (macOS) or [developer.apple.com/sf-symbols](https://developer.apple.com/sf-symbols/).

## Rules

- Use the **symbol name only** — not a file path or emoji.
- Prefer **filled** variants (`.fill`) for menu/toolbar legibility at small sizes.
- Bundle plug-ins may instead use a PNG filename in `manifest.json`; single-file `.omnifocusjs` plug-ins use SF Symbol names only.
- In `validate`, you can change the icon at runtime: `sender.image = Image.symbolNamed("star.fill")` (when `sender` is a `MenuItem` or `ToolbarItem`).

## Used in this repo

| Plug-in | `image` |
|---------|---------|
| undø | `arrow.uturn.backward.circle.fill` |
| Set Review Interval | `arrow.triangle.2.circlepath` |
| Prune Empty Tags | `tag.slash.fill` |

## Curated symbols (OmniFocus plug-in collection + common choices)

### General / settings

`gearshape`, `gearshape.fill`, `gearshape.2`, `gearshape.2.fill`, `slider.horizontal.3`, `wrench.and.screwdriver`, `wrench.and.screwdriver.fill`, `info.circle`, `info.circle.fill`, `info.square.fill`, `questionmark.circle`, `ellipsis.circle`, `ellipsis.circle.fill`

### Undo / redo / reset / activate

`arrow.uturn.backward`, `arrow.uturn.backward.circle`, `arrow.uturn.backward.circle.fill`, `arrow.counterclockwise`, `arrow.clockwise`, `arrow.triangle.2.circlepath`, `gobackward`, `goforward`, `bolt`, `bolt.fill`, `bolt.circle.fill`, `play.circle`, `play.circle.fill`

### Tasks / completion

`checkmark`, `checkmark.circle`, `checkmark.circle.fill`, `checkmark.square`, `checkmark.square.fill`, `circle`, `circle.fill`, `plus`, `plus.circle`, `plus.circle.fill`, `minus.circle`, `xmark.circle`, `xmark.circle.fill`, `trash`, `trash.fill`, `archivebox`, `archivebox.fill`, `archivebox.circle`, `archivebox.circle.fill`

### Projects / folders

`folder`, `folder.fill`, `folder.badge.plus`, `folder.badge.minus`, `tray.full`, `tray.full.fill`, `tray.and.arrow.down`, `tray.and.arrow.down.fill`, `plus.rectangle.on.folder`, `plus.rectangle.on.folder.fill`, `rectangle.stack`, `rectangle.stack.fill`

### Tags

`tag`, `tag.fill`, `tag.circle`, `tag.circle.fill`, `tag.square`, `tag.square.fill`, `tag.slash`, `tag.slash.fill`

### Dates / calendar / review

`calendar`, `calendar.circle`, `calendar.circle.fill`, `calendar.badge.clock`, `calendar.badge.plus`, `clock`, `clock.fill`, `clock.arrow.circlepath`, `24.circle`, `24.circle.fill`, `xcalendar.badge.clock`

### Notes / text / links

`note.text`, `note.text.badge.plus`, `doc.text`, `doc.text.fill`, `doc.on.doc`, `doc.on.doc.fill`, `link`, `link.circle`, `link.circle.fill`, `paperclip`, `text.append`, `text.quote`

### Import / export / share

`square.and.arrow.up`, `square.and.arrow.up.fill`, `square.and.arrow.down`, `square.and.arrow.down.fill`, `square.and.arrow.down.on.square`, `square.and.arrow.down.on.square.fill`, `arrow.up.doc`, `arrow.down.doc`, `square.and.arrow.up.on.square`, `square.and.arrow.up.on.square.fill`

### Communication

`envelope`, `envelope.fill`, `phone`, `phone.fill`, `phone.circle`, `phone.circle.fill`, `video`, `video.fill`, `facetime`, `message`, `message.fill`

### Lists / capture

`list.bullet`, `list.bullet.rectangle`, `list.bullet.rectangle.fill`, `list.number`, `list.dash`, `text.badge.plus`, `tray.and.arrow.up`, `tray.and.arrow.up.fill`

### Navigation / focus

`eye`, `eye.fill`, `location`, `location.fill`, `map`, `map.fill`, `scope`, `binoculars`, `binoculars.fill`, `flag`, `flag.fill`, `flag.slash`, `flag.slash.fill`

### Numbers (dynamic validate icons)

`0.square.fill` … `50.square.fill`, `greaterthan.square.fill` — used to show selection counts on toolbar/menu items.

### Misc

`star`, `star.fill`, `heart`, `heart.fill`, `bell`, `bell.fill`, `bell.badge`, `bell.badge.fill`, `newspaper`, `newspaper.fill`, `cart`, `cart.fill`, `creditcard`, `creditcard.fill`, `person`, `person.fill`, `person.2`, `person.2.fill`, `building.2`, `building.2.fill`, `chart.bar`, `chart.bar.fill`, `function`, `sparkles`

## Picking an icon

1. Match the **verb** (delete → `trash.fill`, copy → `doc.on.doc.fill`, review cadence → `arrow.triangle.2.circlepath`).
2. Prefer icons already used in [omni-automation.com plug-ins](https://omni-automation.com/omnifocus/actions.html) for consistency.
3. Test in OmniFocus Automation menu and toolbar — if missing, OmniFocus may show a blank or default icon; pick a symbol available on your minimum OS target.
