---
{"dg-publish":true,"dg-path":"Cheat sheets/Obsidian.md","permalink":"/cheat-sheets/obsidian/","tags":["tech/obsidian"],"dg-note-properties":{"created":"2023-08-04T11:25:58-05:00","modified":"2026-05-19T09:19:34-05:00","tags":["tech/obsidian"]}}
---


# Callout types

> [!abstract]

> [!info]

> [!todo]

> [!tip]

> [!success]

> [!question]

> [!warning]

> [!failure]

> [!danger]

> [!bug]

> [!example]

> [!quote]

# Inline footnotes

Use the below syntax to add inline, auto-numbered footnotes^[like this]

```
some text^[footnote text]
```

# MathJax

- Use `\,` to force a small space, and `\;` for a larger space

# Link to page of PDF

```
![[PDF#page=10]]
```

# Embedded queries

- set the code block language to `query`

## Highlights

```
/\s==.+==\s/ file:foo
```

## Check boxes

```
task:/.+/
task-todo:/.+/
task-done:/.+/
```

# Math blocks (Numerals plugin)

```math
# Math
apples = 5
cherries = 10
bananas = 3
@total

$redFruits = apples + cherries
@prev + 10 => # highlight answer with =>

fraction(1/3) + fraction(1/4)

# Conversions
1 ft + 12 in
1 cup to floz
```

```math
# variables starting with $ are shared between all math blocks on the page
count = $redFruits * 2
```

# Emulate mobile (for development)

- Open the developer tools and paste `this.app.emulateMobile(!this.app.isMobile)` into the console
    - run again to toggle between mobile & desktop

# List all command IDs

```js
editor.commands.commands
```

# See also

- [[Development/Cheat sheets/Markdown\|Markdown]]
