---
layout: page
title: Data Processing String (DPS)
---

# Data Processing String (DPS)

The **Data Processing String**, or **DPS**, is an optional operator sequence placed between the assignment and the value.

General form:

```fmjaml
path = ;DPS; value
```

For example:

```fmjaml
age = ;N; "42"
```

produces a JSON number rather than a JSON string:

```json
{
  "age": 42
}
```

DPS operators are processed from left to right. The language is intentionally composable rather than precedence-based.

## Data types

The common data-type operators are:

| Operator | Meaning |
|---|---|
| `T` | text / JSON string |
| `N` | number |
| `B` | boolean |
| `D` | date |
| `I` | time |
| `M` | timestamp |
| `R` | container |
| `A` | JSON array |
| `O` | JSON object |
| `J` | raw JSON value |
| `U` | JSON null |

Examples:

```fmjaml
count = ;N; 42
created = ;M; Get ( CurrentTimestamp )
enabled = ;B; true
```

## Optional values with `?`

The optional operator can drop an element when its value is empty.

```fmjaml
nickname = ;?; $nickname
```

Type conversion can happen before the optional test:

```fmjaml
quantity = ;N?; $quantity
```

That is useful when the validity of the value should be tested in its output type rather than as raw text.

## Trimming and formatting

A family of `*` operators handles whitespace and JSON formatting:

- `*` — trim spaces
- `**` — horizontal trim
- `***` — vertical trim
- `****` — compact JSON
- `*****` — format / pretty-print JSON

Because DPS is left-to-right, these operators can be combined with later tests:

```fmjaml
comment = ;***?; $comment
```

This trims the text first and then drops the element if the trimmed result is empty.

## Explicit values

Quoted DPS strings can output explicit values.

```fmjaml
status = ;'unknown'; $status
```

They become especially useful inside mapping and fallback expressions.

## Not / exclusion with `!`

The not operator changes a following test so matching values can be excluded.

For example, a common truelean-style pattern is:

```fmjaml
enabled = ;B!'0'?; $enabled
```

The exact result depends on the surrounding operator sequence, but the important rule is that `!` participates in the left-to-right DPS pipeline rather than a separate precedence system.

## Mapping with `-` and fallback with `:`

The mapping operator can turn one value into another, with `:` providing the next / otherwise branch.

A simple example:

```fmjaml
answer = ;!'ja'-'yes':!'nein'-'no'; $answer
```

This can be read as a left-to-right transformation pipeline.

## End-of-line handling with `¶`

The EOL operator can work with multiline values:

- `¶1` — use the first line
- `¶''` — remove line endings
- `¶' '` — replace line endings with a space

Example:

```fmjaml
title = ;***¶1?; $copied_text
```

This trims the value, keeps only its first line, and drops the element if the result is empty.

## Replacement

DPS supports replacement operations. A simple string replacement uses `>` between search and replacement values.

Regex matching and replacement are available in the FileMaker implementation when the required MBS functionality is present.

Because plugin-backed behaviour is implementation-specific, regex support should be documented and tested separately from the core syntax.

## Composition is the point

DPS is designed so small operators can be chained into compact data-cleaning rules.

Examples include:

```text
B!'0'?
***?*****
¶1
```

The first converts/tests a boolean-like value, the second can combine trimming, optionality, and JSON formatting, and the third works with only the first line.

For normative details of an individual operator, automated tests should remain the final authority while this reference is being completed.
