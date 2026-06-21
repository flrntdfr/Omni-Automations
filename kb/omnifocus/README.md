# OmniFocus API reference (local copy)

This directory holds an **offline export** of the Omni Automation API documentation that ships with [OmniFocus](https://www.omnifocus.com/) (the in-app **API Reference** / scripting dictionary).

## Source and attribution

The file [`API-reference.md`](API-reference.md) reproduces documentation **originally authored and published by [The Omni Group, Inc.](https://www.omnigroup.com/)** as part of OmniFocus and its Omni Automation support. Class names, property descriptions, method signatures, and explanatory text in that file are **Omni Group material**, not original work of this repository.

**OmniFocus**, **Omni Automation**, and related marks are trademarks of The Omni Group, Inc. This project is **not affiliated with, endorsed by, or sponsored by** The Omni Group.

## Rights and disclaimer

- **Copyright.** The underlying API documentation remains the intellectual property of The Omni Group, Inc., subject to their applicable terms, copyrights, and trademarks. Nothing in this folder transfers ownership or grants a license beyond what The Omni Group already provides to users of OmniFocus.
- **Unofficial mirror.** `API-reference.md` is maintained here solely as a **convenience for local development** of plug-ins in the OmniAutomations repository. It may be incomplete, outdated, or differ from the version in your installed copy of OmniFocus.
- **Authoritative source.** For the current, authoritative scripting dictionary, use **OmniFocus → Automation → API Reference** (or the API Reference tab in the Automation Console on iOS/iPadOS). Re-export and replace `API-reference.md` when you upgrade OmniFocus or when the in-app reference changes.
- **No warranty.** This copy is provided **“as is”**, without warranty of any kind. The maintainers of OmniAutomations do not represent that this export is error-free or current.
- **No legal advice.** This README describes attribution and intent; it is **not legal advice**. If you redistribute or publish Omni Group documentation outside personal or internal development use, consult The Omni Group’s terms and applicable law.

## Files

| File | Description |
|------|-------------|
| [`API-reference.md`](API-reference.md) | Markdown export of the OmniFocus Omni Automation API reference |

## Updating

When OmniFocus is updated, export the API Reference again from the application and overwrite `API-reference.md`. Note the OmniFocus version in your commit message if helpful.
