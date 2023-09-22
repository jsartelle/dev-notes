---
{"dg-publish":true,"dg-path":"Cheat sheets/VSCode cheat sheet.md","permalink":"/cheat-sheets/vs-code-cheat-sheet/"}
---


# Keyboard Shortcuts

## Cursor movement

| Shortcut | Action                                    |
| -------- | ----------------------------------------- |
| ⌃⌥(←/→)  | Move between word parts (add ⇧ to select) |
| ⇧⌘\\     | Jump to matching bracket                  |
| ⌘K ⌘Q    | Jump to last edit position                |
| ⌘U       | Undo cursor move                          |
| ⌥F5/⇧⌥F5 | Jump to next/previous change              |

## Selection

| Shortcut | Action                                  |
| -------- | --------------------------------------- |
| ⌘D       | Select next occurrence of selected text |
| ⇧⌘L      | Select all occurrences of selected text |
| ⌃⇧⌘(←/→) | Contract/expand current selection       |
| ⇧⌥I      | Add cursors to line ends                |
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
| ⌘G/⇧⌘G   | Find next/previous               |
| ⌘⌥C      | Toggle case in find widget       |
| ⌘⌥W      | Toggle whole word in find widget |
| ⌘⌥R      | Toggle regex in find widget      |

## Folding

| Shortcut | Action                              |
| -------- | ----------------------------------- |
| ⌘K ⌘L    | Toggle folding                      |
| ⌘K ⌘,    | Create folding range from selection |
| ⌘K ⌘.    | Remove folding ranges in selection  |

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

- add globals and types to a `.d.ts` file anywhere in the project

## See also

[[Development/Cheat sheets/JavaScript cheat sheet#JSDoc\|JavaScript cheat sheet#JSDoc]]
