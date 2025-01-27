---
{"dg-publish":true,"dg-path":"Notes/AppleScript.md","permalink":"/notes/apple-script/","tags":["language/applescript"]}
---


# Syntax

- use `--` for comments

## tell

- sets the scope of a block

```applescript
tell tab 0 of window 0
    set tabName to the title
    set tabURL to the url
end tell

-- equivalent to

set tabName to the title of tab 0 of window 0
set tabURL to the url of tab 0 of window 0
```

## repeat

```applescript
set tabCount to the number of tabs in window 0
repeat with t from 1 to tabCount
    -- do stuff
end repeat
```

# Snippets

## Get name of focused app

```applescript
global frontAppName

tell application "System Events"
    set frontAppName to name of first application process whose frontmost is true
end tell

return {frontAppName}
```

## Check if app is focused

```applescript
tell application "Safari"
    if frontmost then
        -- do stuff
    end if
end tell
```
