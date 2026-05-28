# fmJAML – One Page Overview[^1]

## What is fmJAML?

**fmJAML (FileMaker J(A)SON Markdown Language)** is a compact, both human-friendly and AI-friendly format for generating JSON structures in FileMaker (a so called domain specific language/DSL).

It is designed to:

- define JSON structures in a clear and concise way, using a simple (but powerfully enhanced) line-based path = value syntax
- be writable and readable directly in text form equally by man and machine (AI+U+ME)
- feel familiar to FileMaker developers
- provide solutions to missing or wanting functionality in the FileMaker JSONSetElement function, particularly
  - minimal data type specification with
    - intelligent defaults (`object`/`null`/`string`)
    - memorable single-letter data types (`T`/`N`/`D`/`I`/`M`/`R`/`B`/`A`/`O`/`J`/`U`)
    - standard (opinionated?) formats for common data types (date, time, timestamp, container)
  - optional values
    - allowing  empty values to be completely dropped
  - support path inheritance, array appending and a little data processing logic
- compile to valid JSON - using a simple FileMaker custom function (or script - planned).

Above all, fmJAML moves the task of creating JSON structures from the realm of code into the realm of text.

## Core Concepts

## 1. Line-based path = value syntax

Each line defines a JSON element with a path = value syntax:

```fmjaml
path = ;optional-data-processing-string; value
```

For example:

```fmjaml
person.name = "John"
person.age  = 42
```

produces the following JSON

```json
{
  "person": {
    "age": "42",
    "name": "John"
  }
}
```

Values are treated as text, unless explicitly defined otherwise in the [data processing string](#5-data-processing-string).

For example:

```fmjaml
age = ;N; 42
```

produces the following JSON

```json
{
  "age": 42
}
```

## 2. Path stubs

A 'naked' path with no `=` and no expression creates an empty object

```fmjaml
person
```

->

```json
{
  "person": {}
}
```

A path with `=` but with a missing value creates a `null` value.

```fmjaml
person=
```

->

```json
{
  "person": null
}
```

## 3. Building arrays with `[+]` and `[:]`

The FileMaker array path syntax can be used to build arrays of objects. The syntax is:

- `[+]` → append to the array
- `[:]` → refers to last object

With `[+]` and `[:]` you can quickly build arrays of objects, that is a table structure:

For example:

```fmjaml
[+].name = "Alice"
[:].owns = "looking glass"
```

defines a JSON array with one object with the keys "name" and "owns":

```json
[
  {
    "name": "Alice",
    "owns": "looking glass"
  }
]
```

Note: Whilst FileMaker only started supporting the `[+]/[:]` syntax in FM …er…21(?) … fmJAML supports the syntax in all versions of FileMaker.

## 4. Path Inheritance (..)

An empty element, or with only whitespace, inherits the path from the previous path:

```fmjaml
[+].person
[:]..name = "John"
[:]..age  = "42"
```

→ becomes:

```json
[
  {
    "person": {
      "name": "John",
      "age": "42"
    }
  }
]
```

Note:
2026-04-28 @mrwatson-de: This is currently only correctl implemented for arrays. Object-Path inheritance is still under construction, so the following is only theoretical

A leading . reuses the previous path:

```fmjaml
person
.name = "John"
.age  = "42"
```

→ becomes:

```json
{
  "person": {
    "name": "John",
    "age": "42"
  }
}
```

## 5. Data Processing String

The Data Processing String is an optional sequence of operators which process the element and value, allowing you to

- specify the data type
  - inc. built-in support for Date, Time, Timestamp and Container
- specify optional values
  - drop elements if value is empty
  - specify the data type of empty values
- manipulate the data,
  - trim values (inc. trimming or formatting JSON)
  - remove/ignore certain values
  - handle EOLs (replace/standardise/remove EOLs or just use first line)
  - map values between value domains (e.g. `yes`/`no` -> `true`/`false`)
  - replace strings
    - Substitute
    - RegEx (with MBS plugin)
  - output values (default values / error messages)

The Data Processing String is located between the equals and the value, delimited by semi-colons:

 `;…;`

For example:

```fmjaml
age = ;N; "42"
```

outputs the value as a number instead of a string:

```json
{
  "age": 42
}
```

Data Processing String Operators:

- data types `«letter»`
  - `T` = string
  - `N` = number
  - `D` = date
  - `I` = time
  - `M` = timestamp
  - `R` = container
  - `B` = boolean
  - `A` = json array
  - `O` = json object
  - `J` = json raw (=object or array)
  - `U` = null
- optional operator `?` (tests for `not IsEmpty(«value»)` )
  - `?` optional - drops if the value is empty
  - `N?` outputs number or drops the element if `GetAsNumber(«value»)` is empty
  - `N?N:T` outputs number or `""` if `GetAsNumber(«value»)` is empty
  - `N?N:U` outputs number or `null` if `GetAsNumber(«value»)` is empty
  - `N?:T'error'` drops a number value otherwise outputs the word 'error' if `GetAsNumber(«value»)` is empty
- data manipulation
  - white-space handling `*…`
    - `*` = trim (= spaces)
    - `**` = horizontal trim (= spaces + tabs)
    - `***` = vertical trim (= horizontal trim + newlines)
    - `****` = collapse JSON
    - `*****` = format JSON
  - value strings `'string'`
    - `'default'` = set value to `default`
  - eval strings `` `calculation` ``
    - `` `1+1` `` = set value to `Evaluate ( "1+1" )` -> `2`
  - not operator `!` (sticky)
    - `!'x'` = remove value if equal to `x`
    - `!'0''1'` = remove value if equal to `0` or `1`
  - EOL operator `¶`
    - `¶1` = GetValue ( value ; 1 )
    - `¶''` = remove EOLs
    - `¶'string'` = replace EOLs with string
    - `¶'¶'` = replace EOLs with CR
  - mapping operator `-` (tests `IsEmpty(«value»)`)
    - use in combination with the `!` not operator to map values…
    - `!'Russel'-'Russell'` - maps `Russel` to `Russell` - otherwise returns the value as is
      - In other words: it corrects a common misspelling of my name :-D
    - `!'ja'-'yes':!'nein'-'no'` - maps `ja` to `yes` and `nein` to `no`
    - `N!''-'error'`… outputs "error" if `GetAsNumber(«value»)` is empty…
    - …`:N!'0'-T'ZERO'` otherwise outputs "ZERO" if `value="0"`…
    - …`:N` otherwise outputs the number
  - Replace operator: `>`
    - `'s'>'r'` = Substitute ( value ; "s" ; "r" )
  - Only when MBS plugin is present:
    - Regex strings: `~regex~`
      - Regular expressions are delimited by tilde `~` characters and can be used only when MBS plugin is present and in the following operations:
      - not regex match operation `!~regex~`
        - `!~Russell?~` Removes a value containing `Russell` or `Russel`
      - Regex replace operation `~s~>~r~`
        - `~s~>~r~` Replaces `s` with `r`, e.g. `sad` -> `rad`
        - `~s([aeiou])d~>~s$1d~` Replaces `sad` with `rad`, `sed` with `red`, `sid` with `rid`, `sod` with `rod`, `suddy` with `ruddy`
        - `~(s)(t)~>~$2$1~` Replaces occurences of `s` followed by `t` with `t` followed by `s`: `cast` → `cats`, `fist` → `fits`, `just` → `juts`, `lost` → `lots`, `pest` → `pets`, `rust` → `ruts`, `vest` → `vets`, etc.
        - `~^\s*(.*)$)~>~$1~` Removes leading white space
      - Note: Beware when entering strings from the keyboard that you don't get a hidden control character in the string

Example:

```fmjaml
value = ;N?; ""
```

→ key removed if the number value is empty

## 6. Heredoc values (===)

Multi-line values can be specified comfortably using a 'heredoc', avoiding the need for escaping and encoding:

```fmjaml
text = ===
Hello
World
===
```

→ becomes:

```json
{
  "text": "Hello¶World"
}
```

Note: the trailing EOL is part of the closing heredoc marker, thus to add a trailing EOL you need to leave a blank line before the closing marker.

```fmjaml
text = ===
Hello
World

===
```

→ becomes:

```json
{
  "text": "Hello¶World¶"
}
```
# ⚙️ Technical Section

## Execution Model

fmJAML is interpreted inside a FileMaker While() loop, processing one line at a time.

High-level flow:

for each line:  
  parse, resolve path, resolve value, process operators, update JSON

## Internal State

```js
¢lines // input lines
¢result // JSON result
$fmjaml_path_prev // previous path
```

## Path Resolution

1. Parse left-hand side into path segments
2. If line starts with ".":
   - reuse previous path
   - append suffix
3. Store as new previous path

## Array Paths (FM 21 Compatibility)

- Native: [+], [:]
- Pre-FM21: index via ValueCount(JSONListKeys(...))

## Value (& Heredoc) Parsing

The value is

1. parsed

   - either as the rest of the line after the "=" and data processing string
   - or, if a heredoc marker is found (`===` with an optional identifier, as a heredoc (multi-line raw value) up to the next occurence of the same marker.

2. evaluated as a FileMaker expression, allowing for dynamic values.

## Data Process String - Operator Evaluation

Operators are applied sequentially from left to right:

"N!'0''2'-'even':!'1''3'-'odd':''"

Steps:

1. `N` - Number -> GetAsNumber // FileMaker function: "" -> "" otherwise number
2. `!` - Not operator (sticky!)
3. `'0'` - (not!) String -> Remove `'0'` value, if present.
4. `'2'` - (not!) String -> Remove `'2'` value, if present.
5. `-` - Mapping operator -> test for empty value
6. `'even'` - String -> Output "even" (run if test true, otherwise skip)
7. `:` - Otherwise operator (run if test false, otherwise skip)
8. `!` - Not operator (sticky!)
9. `'1'` - (not!) String -> Remove `'1'` value, if present.
10. `'3'` - (not!) String -> Remove `'3'` value, if present.
11. `-` - Mapping operator -> test for empty value
12. `'odd'` - String -> Output "odd" (if test true, otherwise skip)
13. `:` - Otherwise operator (run if test false, otherwise skip)
14. `''` - String -> Output "" // don't output anything else

Strict left-to-right preocessing, no operator precedence rules.

## Testing values, trimming and Data Type Specification

## Optional values

Drop 'empty' values using the optional operator `?` and the various trim operators.

- `?` = drop empty values
- `*?` = drop empty values after trimming spaces
- `**?` = drop empty values after trimming horizontal whitespace (spaces and tabs)
- `***?` = drop empty values after trimming vertical whitespace (spaces, tabs, newlines)

Specifying just the optional operator `?` tests the raw value to see if it is empty and drops it if so.

## Optional values of a specific data type

Specify a data type *before* testing in order to test the coerced - and thus cleaned - value. Coercing the value to the target data type before testing tidies up the input data and lets you test the output format. This makes for simpler, cleaner code with more predictable results.

For example

- `?N` - `?` allows the value `"abc"` to pass through, but then `N` coerces `GetAsNumber("abc")` to `""`.
- `N?` on the other hand first coerces the value to a number (`N`), which for the value `"abc"` gives `""`, which is then correctly dropped by the `?`.

Note that this coercion uses the FileMaker GetAsNumber function - which usefully returns `""` for text- only inputs - and is not a JSON number coercion which would return `0` for text-only inputs.

- `T?` = optional text -> `not IsEmpty ( GetAsText ( $JAML_value ) )`
- `N?` = optional number -> `not IsEmpty ( GetAsNumber ( $JAML_value ) )`
- `B?` ≠ optional boolean ⚡️ -> `not IsEmpty ( GetAsBoolean ( $JAML_value ) )`
- `D?` = optional date -> `not IsEmpty ( GetAsDate ( $JAML_value ) )`
- `I?` = optional time -> `not IsEmpty ( GetAsTime ( $JAML_value ) )`
- `M?` = optional timestamp -> `not IsEmpty ( GetAsTimestamp ( $JAML_value ) )`

- `R?` -> `not IsEmpty ( $JAML_value )`  // Is this a thing at all? What should 'coerce to container mean at all'?
- `A?` ≠ optional array ⚡️ -> `not IsEmpty ( JSONGetElement ( $fmJAML_value ; "" ) )`
- `O?` ≠ optional object ⚡️ -> `not IsEmpty ( JSONGetElement ( $fmJAML_value ; "" ) )`

⚡️: The data types `B`, `A`, and `O` never output an empty value, so the simple optional wiht `?` does not make sense alone.  
    You have to explicitly state the specific empty value you want to check:

- `***?B` = optional boolean (trilean) -> `SuperTrim( $JAML_value )` -> if `not IsEmpty` -> `GetAsBoolean` -> otherwise drop
- `B!'0'?` = optional boolean (truelean) -> `GetAsBoolean ( $JAML_value ) ≠ false`
- `A!'[]'?` = optional array -> `JSONGetElement ( $fmJAML_value ; "" ) ≠ []`
- `O!'{}'?` = optional object -> `JSONGetElement ( $fmJAML_value ; "" ) ≠ {}`

When outputting values, the data type (if specified) determines how the value is processed before being output:

##

empty(value) = "" OR null OR missing

## JSON Construction

JSONSetElement ( result ; path ; value ; type )

## Design Principles

- Minimal syntax
- Single pass
- No precedence rules
- Fully executable in FileMaker

# ✅ Typical Use

Since fmJAML values are parsed, fmJAML can be used as a template language, for example for defining the data handling for an API.

input data + fmJAML text -> fmJAML CF -> output JSON data for API

- Can be used for any json
- Originally used for fmIDE action scripts

# 🔚 Summary

fmJAML provides:

- Compact JSON authoring
- Path inheritance
- Inline data processing

Ideal for:

- fmIDE ActionScripts
- JSON pipelines
- Text-first FileMaker workflows

[^1]: The one page format requires DIN A444444444444 paper :D
