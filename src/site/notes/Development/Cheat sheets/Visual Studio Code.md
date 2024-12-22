---
{"dg-publish":true,"dg-path":"Cheat sheets/Visual Studio Code.md","permalink":"/cheat-sheets/visual-studio-code/"}
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

- add `// @ts-check` comment to top of JS file for type checking
- type annotations: `/** @type {number} */`
    - More info: [[Development/Cheat sheets/JavaScript#JSDoc\|JavaScript#JSDoc]]
- to type check an entire JS project, create a `jsconfig.json` file in the root with the following:

```json
{
    "compilerOptions": {
        "checkJs": true
    }
}
```

- to type check all JS code, set `js/ts.implicitProjectConfig.checkJs` to true in your user settings
- add globals and types to a `.d.ts` file anywhere in the project

# Debug NPM scripts and ignore `node_modules`

- add this config to `.vscode/launch.json` (replace `dev` with the script name)

```json
{
        "type": "node-terminal",
        "request": "launch",
        "name": "Run Dev Script",
        "skipFiles": [
            "<node_internals>/**",
            "${workspaceFolder}/node_modules/**/*.js"
        ],
        "command": "npm run dev",
    }
```

# Other

- Hold <kbd>Cmd</kbd>+<kbd>Opt</kbd> when clicking a result line in the search editor to open that line in a split pane
