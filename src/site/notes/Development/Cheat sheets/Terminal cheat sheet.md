---
{"dg-publish":true,"dg-path":"Cheat sheets/Terminal cheat sheet.md","permalink":"/cheat-sheets/terminal-cheat-sheet/","tags":["language/terminal"]}
---


# Bash

## Chaining commands

- `a && b`: do `b` if `a` succeeded
    - not to be confused with [[#^8f431a|`a & b`]]
- `a || b`: do `b` if `a` failed
    - these can be chained: `command && echo "success" || echo "fail"`
- `a; b`: do `b` either way
---
- `<`: use file on right as *stdin* for command on left
- `|`: pipe stdout of command on left to *stdin* of command on right
- `>`: write file on right with *stdout* of command on left
    - write to `/dev/null` to discard output
- `>>`: same as `>`, but appends
- `&>`: redirect *stdout* & *stderr*

## Background commands

- `command &`: run command in the background
{ #8f431a}

    - to avoid spamming the terminal, redirect output to `/dev/null`: `command &>/dev/null &`
- <kbd>Ctrl+z</kbd>: suspend (pause) the foreground command
    - `fg`: resume suspended command in foreground
    - `bg`: resume suspended command in background
- `jobs`: list all background and suspended commands
- `kill %1`: kill job #1
- `disown`: disconnect a background command, so it keeps running when the terminal is closed

## Substitution

- `$VARNAME`: variable substitution
    - happens in double quotes or when unquoted, but **not** in single quotes
- `$(command)`: command substitution (substitutes the result of the command)
- `${param}`: parameter substitution (substitutes the parameter value)
- `!!`: repeat the last command entered (ex. `sudo !!`)
- `!$`: last argument of the last command (ex. in `cat foo.txt`, `foo.txt`)
- `$?`: exit code of the last command
{ #a8f207}

    - `0` means success, non-zero means failure

## Function parameters

- access function parameters using `$1`, `$2`, etc
- `"$@"`: all parameters
    - use double quotes to avoid issues with spaces or wildcards
- `$#`: number of parameters given

## Control flow & comparisons

- for loop over every file in working directory

```bash
for i in *; do echo "$i"; done
```

- if statement
    - the spaces around the condition are important!
    - 0 is true and 1 is false (this matches [[Development/Cheat sheets/Terminal cheat sheet#^a8f207\|exit codes]])

```bash
if [[ condition ]]; then
	echo "condition 1 passed"
elif [[ condition2 ]]; then
	echo "condition 2 passed"
else
	echo "both conditions failed"
fi
```

- `[[ condition ]]`: conditional test (the spaces are important)
    - 0 is true and 1 is false (matches exit codes)
    - `=`: string comparison
    - `-eq`: numeric comparison (also: `-lt`, `-le`, `-gt`, `-ge`, `-ne`)
    - `-f`: test if something is a file

## Wildcards

- `cp m*.txt scifi/`
    - `*/` matches only folders
    - `file ^*foo*`: exclude files containing foo
        - requires `setopt extendedglob`
- `?` acts as a single character wildcard
    - ex. `ls *.?s` would list all `.js`, `.ts`, etc. files
- `**` lists files recursively, ex. `ls **/*.json`

## Hotkeys

- <kbd>Ctrl+r</kbd> and <kbd>Ctrl+s</kbd>: search command history backwards/forwards
- <kbd>Ctrl+a</kbd>: move to beginning of line
- <kbd>Ctrl+e</kbd>: move to end of line
- <kbd>Ctrl+w</kbd>: delete word
- <kbd>Ctrl+u</kbd>: clear from start of prompt to cursor
- <kbd>Ctrl+k</kbd>: clear from cursor to end of prompt
- <kbd>Ctrl+y</kbd>: undo deleting text
- <kbd>Ctrl+l</kbd>: refresh screen

# Fish

## Basics

- `set -x VARIABLE SomeValue`: export a variable
    - `set -U` sets a universal variable (persists across sessions)
- `alias --save name="definition"`: save alias
- `>?`: redirect output to file, but avoid overwriting existing file
- `psub`: lets you save stdout to a temporary file
    - ex: `diff (sort a.txt | psub) (sort b.txt | psub)`
- command substitution: `(command)` (no `$`)
- access arguments with `$argv[1]` instead of `$1`
- use `$status` instead of `$?` for last exit code
- `if type -q command ...`: check if command exists
- `set_color`: set the text color
    - supports 3 or 6 digit hex colors, or reserved color names
        - use `-c` to print reserved color names
        - `normal` will reset the color
    - `-o`, `-i`, `-u`: set bold/italic/underline
    - `-b`: set the background color

## History recall

- <kbd>Alt+Up Arrow</kbd>: recall individual arguments
- <kbd>Alt+s</kbd>: recall last command with `sudo` prefixed

## String matching

- `string match 'word' 'string1' 'string2'... `
    - `-a`: print all matches
    - `-e`: print entire string, not just match
    - `-i`: case insensitive
    - `-n`: report each match as a 1-based start position and length
    - `-r`: interpret as Perl-style regex
        - without `-r` or `-e`, the pattern must match the entire string
        - if the regex contains capturing groups, multiple items will be reported for each match, one for the entire match and one for each capturing group
        - *automatically sets variables for all named capturing groups* `(?<name>expression)`
            - if `-a` is used, each variable will contain a list of matches
    - `-v`: only print lines that do not match the pattern

## Snippets

### Get name of frontmost app (macOS)

```shell
set result (string match -r '"LSDisplayName"="(.+)"' (lsappinfo info -only name (lsappinfo front)))
echo $result[2]
```

# Commands

## Text search and manipulation

### grep

Returns lines that match a word or pattern

- `grep 'word' filename`
    - filename can also be a directory or pattern, like `.` or `*.js`
    - `grep -F` (or `fgrep`) is faster but finds only fixed patterns (not regexp)
    - `grep -E` (or `egrep`) handles extended regular expressions
    - `grep -P` uses Perl-style regexps (allows things like [[Development/Cheat sheets/Regex cheat sheet#Lookarounds\|look-around assertions]])
- `-i`: case insensitive
- `-w`: whole word matches
- `-v`: invert match (match only lines that do not contain the word)
- `-R`: search directory recursively (ex. `grep -R foo ~/bar`)
    - `-l`: show matching filenames
    - `-h`: do not output file names

#### Output configuration

- display surrounding lines: `-B` (before) and `-A` (after)
- print only what matches pattern: `-o`
- count number of matches: `-c`
    - prepend line number: `-n`

### awk

Searches files for pattern matches and performs an action based on them

- format: `awk pattern { action }`
- the entire program (pattern and action) should be quoted
- lines are split into chunks by whitespace, numbered `$1`, `$2`, etc.
    - `$0` matches the whole line
    - pass `-F regexp` to split by `regexp` instead
- action examples
    - `{ print $1 }`: print the first whitespace-separated chunk
    - `length($0) > 72`: print lines longer than 72 characters
    - `{ print $2, $1 }`: print first two fields in opposite order
- if `pattern` is missing it will match every line, if `action` is missing it will print all matching lines

### sed

- `sed`: modify stdin based on regex
    - ex. `sed 's/foo/bar/' file.txt`
    - end regex with `/g` for global search

### wc (word count)

- `wc`: output number of lines, words, and characters (in that order)

### cat/less

- `cat`: print file contents
- `less`: show file contents in a scrollable view

### head/tail

- `head -#`: output first # lines
- `tail -#`: output last # lines

### sort

- `sort`: sort alphabetically (doesn't modify original)
    - `-r` to reverse, `-h` for numeric sorting

### uniq

 - `uniq`: filter out duplicate lines **if they are adjacent**
    - combine `sort` and `uniq` to remove all duplicates

## Files & folders

- `folder` refers to a folder itself, `folder/` refers to the contents of the folder
    - `cp folder destination` will copy `folder` itself to `destination`, but `cp folder/ destination` will copy the **contents** of `folder`

### ls

- `-1`: show results in single column
- `-a`: list hidden files
- `-t`: sort by modified date

### file

- `file`: show information about file

### stat

- `stat -f "%Xf" file.txt`: if file is hidden, result will be 8000

### find

- `find dir -type f -name foo*`: find all *files* starting with "foo" in dir (recursive)
    - use `-type d` for folders (directories)
- `find dir -name .DS_Store -delete`: delete all *.DS_Store* files in dir
- `find . -name foo* -exec command`: execute `command` on all matching files

### lsof (list open files)

- `lsof +d dir`: shows open files in dir
- `lsof +D dir`: shows open files in dir and its subfolders
- `lsof -c Finder`: list all files Finder has open

#### Find process using port

- `lsof -i :80`: find out which processes are using port 80

### cp

- move/copy multiple files: `cp a.txt b.txt c/`

### rsync

- uses modification times to sync only changed files
- `rsync -av dir1 dir2`: copy directory 1 to directory 2
    - `-a` (archive): sync recursively and preserve symlinks and metadata
    - `-v`: verbose
- `rsync -azP dir1 username@host:/path/to/dir2`: copy over SSH
    - `-z`: use compression
    - `-P`: show progress bar, and allow resuming transfers
- other options:
    - `--exclude=pattern`: exclude files matching `pattern`
    - `-n`: dry run

### ln

- `ln -s source target`: create a symbolic link from `source` to `target`

## Processes

### ps

- `ps -ef`: list running processes
    - `-e`: include processes from other users
    - `-f`: show more information
- to filter the list while including headers:
    - bash: `ps -ef | { head -1; grep Finder; }`
    - fish: `ps -ef | begin; head -1; grep Finder; end;`

### top

- get CPU usage of a process
    - `-l 2`: take 2 samples (CPU usage is based on the delta between samples)
    - for memory usage, replace `cpu` with `mem` and omit `-l 2`

```shell
top -stats "command,cpu" -l 2 | grep WindowServer | tail -1
```

### pkill

- send a signal to kill all matching processes by name
- the default signal is `-15` which tells the process to exit gracefully
    - send `-9` to kill the process immediately
- the name uses regex matching, to match an exact name use ex. `"^node$"`
    - use the `-f` flag to match against the full argument list, ex. `pkill -f "node index.js"`
- use `-n` or `-o` to kill the newest or oldest matching process, respectively

```shell
pkill -9 "node"
```

## Network

### ipconfig

#### Get list of network adapters

```shell
ipconfig getiflist
```

#### Get network adapter details

- replace `en0` with the network adapter name

```shell
ipconfig getsummary en0
```

#### Get IP address

- replace `en0` with the network adapter name

```shell
ipconfig getifaddr en0
```

### curl

- `-v`: verbose mode, prints connection steps
- `-X`: set request method (`GET`, `POST`, etc)
- `-d "body"`: set the request body
- `-H "header"`: set a header
- `-s`: silent mode, disables progress bar and error messages
    - `-sS`: disable progress bar but keep error messages
- `-L`: follow redirects
- `-I`: fetch headers only

Send a POST request with a JSON body:

```shell
curl -X POST -H "Content-Type: application/json" -d '{"name": "Oscar", "species": "cat"}' https://example.com/api/pets
```

## nano

- <kbd>Ctrl+Shift+6</kbd>: mark text
    - <kbd>Ctrl+k</kbd>: cut/delete marked text

## tmux

- `tmux`: create a new session
- `tmux attach`: reattach to existing session

### Keyboard shortcuts

- <kbd>Ctrl+b "</kbd>: split horizontally
- <kbd>Ctrl+b %</kbd>: split vertically
- <kbd>Ctrl+b {arrow}</kbd>: move focus
- <kbd>Ctrl+b+{arrow}</kbd>: resize focused pane
- <kbd>Ctrl+b x</kbd>: close pane (y to confirm)
- <kbd>Ctrl+b d</kbd>: detach from session

<div class="rich-link-card-container"><a class="rich-link-card" href="https://www.tecmint.com/tmux-to-access-multiple-linux-terminals-inside-a-single-console/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.tecmint.com/wp-content/uploads/2016/01/Tmux-Manage-Multiple-Linux-Terminals.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">How to Use 'Tmux Terminal' to Access Multiple Terminals Inside a Single Console</h1>
		<p class="rich-link-card-description">
		tmux (short for Terminal MUltipleXer), a simple and modern alternative to the well-known GNU screen, and will enable you to access and control a number of terminals (or windows) from a single terminal.
		</p>
		<p class="rich-link-href">
		https://www.tecmint.com/tmux-to-access-multiple-linux-terminals-inside-a-single-console/
		</p>
	</div>
</a></div>

## crontab

- `crontab -l`: print crontab
- `crontab -e`: edit crontab (auto reloads on exit)
    - format: `minute hour dayOfMonth month dayOfWeek command`
    - separate multiple times with commas, ex `1,2,3`
    - use `*/#` to specify an interval, ex. `*/20 * * * * command` means "every 20 minutes" (without the \*/ this would mean "on minute 20 of every hour")
    - `@reboot command` to run on reboot
- using `sudo` edits a separate crontab for `root`

<div class="rich-link-card-container"><a class="rich-link-card" href="https://crontab.guru/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://crontab.guru/favicon.ico')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Crontab.guru - The cron schedule expression editor</h1>
		<p class="rich-link-card-description">
		An easy to use editor for crontab schedules.
		</p>
		<p class="rich-link-href">
		https://crontab.guru/
		</p>
	</div>
</a></div>

## fzf

<div class="rich-link-card-container"><a class="rich-link-card" href="https://github.com/junegunn/fzf" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://repository-images.githubusercontent.com/13807606/6ecaf000-3f27-11eb-9d7b-68c540f91d33')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">GitHub - junegunn/fzf: A command-line fuzzy finder</h1>
		<p class="rich-link-card-description">
		:cherry_blossom: A command-line fuzzy finder. Contribute to junegunn/fzf development by creating an account on GitHub.
		</p>
		<p class="rich-link-href">
		https://github.com/junegunn/fzf
		</p>
	</div>
</a></div>

- lets you interactively filter data from *stdin* and output the selected item to *stdout*

## node

- `node --watch script.js`: re-run script whenever it changes

## npm

[[Development/Cheat sheets/NPM and Yarn cheat sheet\|NPM and Yarn cheat sheet]]

## python

### Simple web server

```shell
python3 -m http.server 3000
```

## ffmpeg

> [!note]
> The build on Homebrew doesn't support `webm`. Use one of the downloads [here](https://evermeet.cx/ffmpeg/) instead.

- `-c`: specify codec
    - use `-codecs` to get a list of available codecs
    - use `-vcodec` and `-acodec` to specify video/audio codecs separately
    - for webm use `-vcodec libvpx -acodec libvorbis`
    - `-c copy` **avoids re-encoding** (useful for trimming, changing metadata, etc)

### Trim video

- `ffmpeg -i input.mp4 -ss 00:01:40 -to 00:02:16 -c copy output.mp4`: trim to between 1m:40s and 2m:16s

### Convert video to audio

- `ffmpeg -i input.m4a -c:v copy -c:a libmp3lame -q:a 4 output.mp3`: convert m4a to mp3 without significant quality loss

### Extract frames

- `ffmpeg -i input.mp4 -ss 00:00:14.435 -vframes 1 out.png`: extract one frame from 0h:0m:14sec:435msec to *out.png*
- `ffmpeg -i input.mp4 frame_%04d.jpg`: extract all frames to *frame_0001.jpg*, *frame_0002.jpg*, etc

## youtube-dl/yt-dlp

- `--simulate`: dry run
- `--match-title keyword`: only download items matching *keyword*
- `-F url`: list all available formats
    - `-f # url`: download video with format number
- `-f bestaudio[ext=m4a] --add-metadata --embed-thumbnail url`: save audio with thumbnail and chapter markers
- `-o "%(playlist_index)s-%(title)s.%(ext)s" <playlist_link>`: save a playlist with numbered files
- `--playlist-start 2`: start at video 2 in the playlist (1-indexed)
- `--playlist-end 5`: end at video 5
- `--playlist-items 1,2,5-7`: download certain items

## ImageMagick

### Input/Output

- To read from stdin or stdout, replace the input or output filename with `-`
    - Explicit file formats can be provided like this: `png:-`

- Quick Look result (fish)

```fish
qlmanage -c public.image -p (magick convert image.png ... - | psub)
```

- Select frames from a GIF

```shell
magick 'image.gif[0]' image.png
magick 'image.gif[0-3]' image-%d.png # outputs image-0.png thru image-3.png
magick 'image.gif[0,3]' image-%d.png # only frames 0 and 3
```

### Resizing and scaling

- Resize image to fit in a specific size (keeps aspect ratio)
    - Percentages can also be used

```shell
magick convert image.png -resize 100x100 resized.png
```

- Stretch the image to the exact size: `-resize 100x100\!`
- Only shrink larger images: `-resize 100x100\>`
- Only enlarge smaller images: `-resize 100x100\<`
- Resize the smallest dimension to fit: `-resize 100x100^`
    - Above plus cropping (like `image-fit: cover`): `-resize 100x100^ -gravity center -extent 100x100`

---

- Other scaling algorithms (replace `-resize` with these):
    - `-scale`: nearest neighbor
    - `-adaptive-resize`: can reduce blurring
- To double the image size using Scale2X use `-magnify` (with no size argument)

## jq

- pipe JSON (ex. from `curl`) into `jq` to pretty-print it
- `jq -s '.[0] * .[1]' --indent 4 old.json patch.json > new.json`: recursively merge *patch.json* into *old.json*
- live preview filters using [[Development/Cheat sheets/Terminal cheat sheet#fzf\|#fzf]]

```shell
echo '' | fzf --preview 'jq {q} < filename.json'
```

### Flags

- `-f path`: read filter from `path`
- `--indent n`: change output indentation
- `-r`: output strings without quotes

**JSON for examples:**

```json
{
    "foo": {
        "bar": [
            { "name": "Apple", "color": "Red" },
            { "name": "Orange", "color": "Orange" },
            { "name": "Banana", "color": "Yellow" }
        ]
    }
}
```

### List all values for key in array of objects

 - `.[]` spreads an array

 ```jq
 .foo.bar | .[].name
 ``` 

## pandoc

> [!important]
> Pandoc can convert to PDF, but not from PDF.

```shell
pandoc file.docx -o file.html
```

## macOS-specific

### chflags

#### Hide/unhide files & folders

See [[Development/Cheat sheets/Terminal cheat sheet#stat\|#stat]] to check if a file/folder is hidden

```shell
chflags hidden file.txt
chflags nohidden file.txt
```

### defaults

#### Dim Dock icons of hidden apps

```shell
defaults write com.apple.Dock showhidden -boolean yes; killall Dock
```

# PowerShell

- `explorer .`: open Explorer window in current working directory
    - or `ii .`: short for `Invoke-Item .`
- `taskkill /F /IM name`: kill process matching name (wildcard * can be used)
- `New-Item -ItemType SymbolicLink -Path "dest" -Target "src"`: create symbolic link to *src* at *dest*
    - must be elevated
    - `-ItemType HardLink` for hard links
- `Get-Content urls.txt | ForEach-Object {Invoke-WebRequest $_ -OutFile $(Split-Path $_ -Leaf)}`: download list of URLs
    - `Split-Path $_ -Leaf` gets the last part of the URL as the file name

## Snippets

### Delete hidden macOS files

``` powershell
$theSource = "E:\"       # <<<<< insert drive here
Get-Childitem $theSource -Include @("._*", ".DS_Store", ".fseventsd", ".TemporaryItems", ".Trashes", ".Spotlight-V100") -Recurse -Force -ErrorAction SilentlyContinue | Remove-Item -Force -Recurse
```
