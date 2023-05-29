---
{"dg-publish":true,"dg-path":"Cheat sheets/VSCode cheat sheet.md","permalink":"/cheat-sheets/vs-code-cheat-sheet/"}
---


- Use `#region` comments to fold code regions in many languages

# Keyboard Shortcuts

> [!attention]
> Shortcuts in **bold** are custom and might require extensions!

## Cursor movement

| Shortcut | Action                                    |
| -------- | ----------------------------------------- |
|⌃⌥(←/→)|Move between word parts (add ⇧ to select)|
| ⇧⌘\\     | Jump to matching bracket                  |
| ⇧⌥\\     | **Jump to matching HTML tag**             |
| ⌘K ⌘Q    | Jump to last edit position                |
| ⌘U       | Undo cursor move                          |
| ⇧⌥(N/P)  |**Next/previous merge conflict**|

## Selection

| Shortcut | Action                                  |
| -------- | --------------------------------------- |
| ⌘D       | Select next occurrence of selected text |
| ⇧⌘L      | Select all occurrences of selected text |
| ⌃⇧⌘(←/→) | Contract/expand current selection       |
| ⌘K F     | **Focus selection**                     |
| ⌘K ⌘F    | Format selection                        |

## Editing

| Shortcut | Action              |
| -------- | ------------------- |
| ⇧⌘Enter  | Insert line above   |
| ⌘Enter   | Insert line below   |
| ⇧⌘K      | Delete current line |
| ⌃⌥(⌫/⌦)  | Delete word parts   |

## Find

| Shortcut | Action                           |
| -------- | -------------------------------- |
| ⌘⌥C      | Toggle case in Find widget       |
| ⌘⌥W      | Toggle whole word in Find widget |
| ⌘⌥R      | Toggle regex in Find widget      |
| ⌘G/⇧⌘G   | Find next/previous               |

## Folding

| Shortcut | Action                       |
| -------- | ---------------------------- |
| ⌘K ⌘L    | Toggle folding               |
| ⌘K ⌘,    | Create manual folding range  |
| ⌘K ⌘.    | Remove manual folding ranges |

# JavaScript Type Checking

- Add `// @ts-check` comment to top of JS file for type checking
- Type annotations: `/** @type {number} */`
- To type check an entire JS project, create a `jsconfig.json` file in the root with the following:

```json
{
    "compilerOptions": {
        "checkJs": true
    }
}
```

- add globals to `globals.d.ts` somewhere in the project
