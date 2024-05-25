---
{"dg-publish":true,"dg-path":"Cheat sheets/VSCode cheat sheet.md","permalink":"/cheat-sheets/vs-code-cheat-sheet/"}
---


# Keyboard Shortcuts

## File navigation

| Shortcut   | Action                          |
| ---------- | ------------------------------- |
| F4/⇧F4 | Next/previous search result |

## Cursor movement

| Shortcut   | Action                                    |
| ---------- | ----------------------------------------- |
| ⌃⌥(←/→)    | Move between word parts (add ⇧ to select) |
| ⇧⌘\\       | Go to matching bracket                    |
| ⌘K ⌘Q      | Go to last edit position                  |
| ⌘U         | Undo cursor move                          |
| ⌥F5/⇧⌥F5   | Go to next/previous change                |
| ⌥F8/⇧⌥F8   | Go to next/previous problem               |

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

## Find/Search

| Shortcut   | Action                                       |
| ---------- | -------------------------------------------- |
| ⌘G/⇧⌘G     | Find next/previous                           |
| ⌘⌥C        | Toggle case in find widget                   |
| ⌘⌥W        | Toggle whole word in find widget             |
| ⌘⌥R        | Toggle regex in find widget                  |
| F4/⇧F4 | Go to next/previous global search result |

## Folding

| Shortcut | Action                              |
| -------- | ----------------------------------- |
| ⌘K ⌘L    | Toggle folding                      |
| ⌘K ⌘,    | Create folding range from selection |
| ⌘K ⌘.    | Remove folding ranges in selection  |

# JavaScript Type Checking

- Add `// @ts-check` comment to top of JS file for type checking
- Type annotations: `/** @type {number} */`
    - More info: [[Development/Cheat sheets/JavaScript cheat sheet#JSDoc\|JavaScript cheat sheet#JSDoc]]
- To type check an entire JS project, create a `jsconfig.json` file in the root with the following:

```json
{
    "compilerOptions": {
        "checkJs": true
    }
}
```

- add globals and types to a `.d.ts` file anywhere in the project

# Other

- Hold <kbd>Cmd</kbd>+<kbd>Opt</kbd> when clicking a result line in the search editor to jump to that line in a split pane
