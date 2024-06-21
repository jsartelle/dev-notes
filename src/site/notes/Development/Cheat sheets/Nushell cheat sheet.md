---
{"dg-publish":true,"dg-path":"Cheat sheets/Nushell cheat sheet.md","permalink":"/cheat-sheets/nushell-cheat-sheet/"}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://www.nushell.sh/book/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.nushell.sh/icon.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Introduction | Nushell</h1>
		<p class="rich-link-card-description">
		A new type of shell.
		</p>
		<p class="rich-link-href">
		https://www.nushell.sh/book/
		</p>
	</div>
</a></div>

# General

- prefix commands with ^ to use an external command rather than the Nu builtin (ex. `^echo`)
    - all data passed to/from external commands is treated as strings
        - Nushell renders lists and tables before passing them to external commands, so to get the raw output use `to text`
- instead of using `>` to save files, pipe to the `save` command (ex. `"hello" | save output.txt`)
- variable assignment: `let x = $x + 1`
    - variables are immutable, so changes to existing variables are actually shadowing and are block-scoped
- environment changes are block-scoped - for example, using `cd` inside a block will only change `PWD` inside the block, so you don't need to undo the `cd` before the next iteration
- wrap pipelines in `()` to create subexpressions, which can be spread over multiple lines

```shell
(
    "01/22/2021" |
    parse "{month}/{day}/{year}" |
    get year
)
```

## PATH configuration

- PATH is a list of paths at `$env.PATH`
- To add a new entry, add this to `.env.nu`
    - To find the location of this file, run `echo $nu.env-path`

```shell
$env.PATH = ($env.PATH | split row (char esep) | append '/opt/homebrew/bin')
```

# Data types

- integers
- decimals (includes `Infinity`)
- strings
    - `''` or ``` `` ``` strings do no processing (no escapes supported), but can't contain their delimiter character
    - `""` strings support standard escape characters using `\`
    - one-word strings can be written without quotes inside data structures or parameters, but it's better to use quotes
    - interpolated strings are wrapped in `$''` or `$""` and use `()` for expressions
        - ex. `let name = "Alice"; $"greetings, ($name)"`
        - `$''` doesn't support escapes, `$""` does
        - parentheses can be escaped using `\`
- booleans (`true` or `false`)
- dates
    - `date now`: get current date
    - can be coerced to a table: `date now | date to-table`
- durations, ex. `2min + 12sec`
    - suffixes: `ns`, `us`, `ms`, `sec`, `min`, `hr`, `day`, `wk`
    - fractions are supported
- file sizes, ex. `64mb`
    - supports both `kb` (kilobyte, 1000 bytes) and `kib` (kibibyte, 1024 bytes)
- ranges
    - inclusive by default: `1..3` includes 1, 2, and 3
    - to make exclusive: `1..<3` includes 1 and 2
    - ranges can be open-ended: `..3` (0, 1, 2, 3) or `3..` (3, 4, 5...)
        - if a range is open-ended on the right side, it will keep counting for as long as possible
- binary
    - group of raw bytes, in hex (`0x[FE FF]`), binary (`0b[1 1010])`, or octal (`0o[377]`)
- `null`
    - represents nothing - **not the same as the absence of a value**, which is marked by ❎
- records
    - ex. `{ name: 'Nushell', lang: 'Rust' }`
    - a record is just a one-row table - **anything that works on a table row works on a record**
    - `transpose` can be used to convert records into tables
        - ex. `{name: sam, rank: 10} | transpose key value` results in: `[{key: name, value: sam}, {key: rank, value: 10}]`
- lists
    - ex. `[0 1 'two' 3]`
        - commas are allowed but not required
    - a list is just a one-column table - **anything that works on a table column works on a list**
- tables
    - ex. `[[x, y]; [12, 15], [8, 9]]`
    - **a table is just a list of records**, and can be specified that way: `[{ x: 12, y: 15 }, { x: 8, y: 9 }]`
    - rows and columns can be accessed using paths
        - `[{langs:[Rust JS Python], releases:60}].0.langs.2` results in: `Python`
        - use `?` to ~mark access as optional - missing values are replaced with `null`
            - ex. `[{foo: 123}, {}].foo?`
        - a single table row is a record, and a single column is a list
- closures
    - ex. `{ |e| $e + 1 | into string}`
    - parameters are encased in `|pipes|` - for most closures, instead of an explicit parameter ==you can access the pipeline input using `$in`==
    - closures can be assigned to variables and run using `do`

```shell
let greet = { |name| print $"Hello ($name)"}
do $greet "Julian"
```

- blocks
    - blocks don't close over variables and can't be passed as values, but can mutate variables in their parent closure

```shell
mut x = 1
if true {
    $x += 1000
}
print $x
```

# Basic commands

- `help {command}`: get help on a command
    - `help commands`: list all commands
    - `help -f {query}`: search for help
- `describe`: list the data type of a value
- `into {type}`: convert a value to a different data type (ex. `"-5" | into int`)
- `do {closure} {params}`: run a closure
- `is-empty`: determines if a string, list, or table is empty
- `sys`: system information
- `http get`: fetch the contents of a URL
    - ex. `http get https://blog.rust-lang.org/feed.xml | table`
- `inc {path?}`: increment a version number (`--major`, `--minor`, `--patch`)
    - ex. `open "Cargo.toml" | inc package.version --minor | save "Cargo_new.toml"`
- `open {filename}`: can parse many different types of files as tables (ex. csv, json, xml, xls(x), sqlite), otherwise loads them as a single string
    - pass `--raw` to always load as a string
    - to get a table from a SQLite database: `open foo.db | get some_table`
    - to run a SQL query: `open foo.db | query db "select * from some_table"`
- `save {filename}`: write to a file

# Working with strings

- `from {format}`: tell Nu to parse a string in the specified format (ex. `from json`)
    - use `from csv|tsv|ssv` to split comma/tab/space separated data
- `lines`: split string by lines
- `split`
    - `split row {delimiter}`: split into a list by delimiter
    - `split column {delimiter}`: split into table columns by delimiter
        - can pass column names after the delimiter, otherwise generic column names are used
    - `split chars`: split into list of characters
- `parse`: split a string into columns
    - `'Nushell 0.80' | parse '{shell} {version}'`
    - regex: `'where all data is structured!' | parse --regex '(?P<subject>\w*\s?\w+) is (?P<adjective>\w+)'`
- `str`: contains many string functions
    - `str trim`: trim string
    - `str contains`: check for a substring match (or use `=~`)
    - `str index-of {string}`
    - `str reverse`
- `fill {--width num} {--alignment left|right} {char}`: pad a string
- `size`: get various string statistics (line count, word count, etc)
- string comparison operators:
    - `==`, `!=`
    - `=~`, `!~`: regex comparisons
        - ex. `'APL' =~ '^\w{0,3}$'`
    - `starts-with`, `ends-with`
        - these are operators, not commands!
- use `ansi {color}` to color strings
    - ex. `$'(ansi purple_bold)This text is a bold purple!(ansi reset)'`
    - always end with `ansi reset`

# Working with tables

> [!tip]
> Remember that column indices are strings, and row indices are numbers.

- `get {path}`: get a list with the values of a table column
    - ex. `sys | get host`
        - you can pass a nested path, ex. `sys | get host.sessions.name`
    - you can use this to get an index that's in a variable - `let index = 1; $names | get $index`
- `select {...index}`: create a new table with the specified column names or row numbers
- `sort-by {column}`: sort a table
- `length`: count rows
- `reverse`: reverse table row order
- `transpose {...colNames}`: swap rows and columns
- `where {column} {operator} {param}`: filter a table
    - ex. `ls | where size > 1kb` or `ps | where cpu > 5`
    - use `==` for equality

- retrieving items:
    - `first {num}` and `last {num}`: get the first or last *num* items
    - `take {num}`: get *num* rows from the beginning
        - **not sure what the difference between `first` and `take` is**
    - `skip {num}`: exclude *num* rows from the beginning
        - ex. `[red yellow green] | skip 1` results in: `[yellow green]`
        - since variables are immutable, the table won't be modified unless you explicitly re-assign to it
    - `drop {num}`: same, but from the end
    - `range`: get a range of items
        - `[a b c d e f] | range 1..3` results in: `[b c d]`

- modifying tables:
    - `insert {index} {value}`: insert a value
    - `update {index} {value}`: replace a value
    - `upsert {index} {value}`: insert or update, depending on whether the value already exists
    - `move {...index} --after {index}`: move one or more rows or columns
        - or `--before`
    - `rename`: rename columns
        - can pass N names to rename the first N columns:
            - `[[a, b]; [1, 2]] | rename foo`: column names will be `foo, b`
        - or rename a specific column with `--column`
            - `[[a, b]; [1, 2]] | rename --column [b bar]`: column names will be `a, bar`
    - `reject {...index}`: drop columns
    - `append`: concatenate two tables with identical column names
        - ex. `$table1 | append $table2`
        - `prepend`: same but at the beginning
    - `merge`: merge two tables (can be chained)
        - ex. `$table1 | merge $table2 | merge $table3`
        - you can merge lots of tables by putting them in a list and using [[#^5ad478|reduce]]: `[$first $second $third] | reduce { |it, acc| $acc | merge $it }`

## Examples

```shell
open package.json | get scripts
```

# Working with lists

- conditions:
    - `in` and `not-in`: operators to check if a value is or isn't in a list
    - `any`: checks if any item in a list matches a condition
    - `all`: same, but checks if every item matches the condition

```shell
# Do any color names end with "e"?
$colors | any {|it| $it | str ends-with "e" } # true
```

- converting lists:
    - `flatten`: flattens a single level (call multiple times to flatten deeper)
    - `wrap {columnName}`: converts a list to a one column table

## Iterating over lists

- `each`: the closure gets a parameter with the current list item
    - use `enumerate` to provide `index` and `item` values
- `par-each`: like `each`, but runs in parallel
    - result order is not guaranteed

```shell
let names = [Mark Tami Amanda Jeremy]
$names | each { |it| $"Hello, ($it)!" }
# Outputs "Hello, Mark!" and three more similar lines.

$names | enumerate | each { |it| $"($it.index + 1) - ($it.item)" }
# Outputs "1 - Mark", "2 - Tami", etc.
```

- `where` lets you filter the list

```shell
let colors = [red orange yellow green blue purple]
$colors | where ($it | str ends-with 'e')
# The block passed to `where` must evaluate to a boolean.
# This outputs the list [orange blue purple].
```

- `reduce` takes a closure that gets 2 parameters, the item and an accumulator
{ #5ad478}

    - to specify an initial value use `--fold`

```shell
let scores = [3 8 4]
$"total = ($scores | reduce { |it, acc| $acc + $it })" # total = 15

$"total = ($scores | math sum)" # easier approach, same result

$"product = ($scores | reduce --fold 1 { |it, acc| $acc * $it })" # total = 96

$scores | enumerate | reduce --fold 0 { |it, acc| $acc + $it.index * $it.item } # 0*3 + 1*8 + 2*4 = 16
```

# Examples

## Kill single process by name

- add `-f` to force

```shell
kill (ps | where name == "Dock" | first).pid
```

## Kill matching processes

```shell
ps | where name == "Dock" | each { kill $in.pid }
```
