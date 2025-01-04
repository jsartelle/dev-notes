---
{"dg-publish":true,"dg-path":"Notes/Notes/Obsidian.md","permalink":"/notes/notes/obsidian/","tags":["tech/obsidian"]}
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

# Embed numbr calculations

> [!tip]
> Make the iframe at least 300px high to leave room for the menu.
>
> If you make edits, choose **Share document** from the menu and then update the iframe URL.

```html
<iframe src="https://numbr.dev/#IYBxBsFMGcAIF5YFYBQB7ATsAdgcxgrAIwAMKoEBA1LJjvtCikA=" width="100%" height="300px"></iframe>
```

<iframe src="https://numbr.dev/#IYBxBsFMGcAIF5YFYBQB7ATsAdgcxgrAIwAMKoEBA1LJjvtCikA=" width="100%" height="300px"></iframe>

# Emulate mobile (for development)

- Open the developer tools and paste `this.app.emulateMobile(!this.app.isMobile)` into the console
    - run again to toggle between mobile & desktop

# See also

- [[Development/Notes/Markdown\|Markdown]]
