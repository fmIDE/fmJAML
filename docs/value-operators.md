# Value operators

fmJAML has three pairs of value forms. Each pair has a single-line and a multiline form.

| Value operator | Form | Meaning |
|---|---|---|
| *(none)* | Single line | Evaluate a calculation |
| `=` | Multiline | Evaluate a calculation (*herecalc*) |
| `==` | Single line | Take text literally (*hereline*) |
| `===` | Multiline | Take text literally |
| `====` | Single line | Turn text into a quoted calculation (*text-to-quote*) |
| `=====` | Multiline | Turn text into a quoted calculation (*text-to-quote*) |

The visual pattern is intentional: **0/1 equals signs** mean calculation, **2/3** mean text, and **4/5** mean text-to-quote. Even counts denote single-line forms; odd counts denote multiline forms.

## No value operator (normal single-line calculation)

**fmJAML:**
```fmjaml
text = "Hello"
number = 42
enabled = true
disabled = false
```

**JSON:**
```json
{"text":"Hello","number":42,"enabled":true,"disabled":false}
```

The fmJAML **`"Hello"`** visually resonates with the JSON encoding **`"Hello"`**. Likewise, **`42`**, **`true`**, and **`false`** visually match their JSON values.

### How it works

```text
value = Evaluate ( «single-line-calculation» )
```

Simple numeric and boolean results can be recognised automatically. The treatment of other calculated results, such as `6 * 7`, belongs in the separate documentation on data types and type handling.

### Syntax

```fmjaml
«path» = «single-line-calculation»
```

## Value operator `=` (multiline calculation operator / 'herecalc' operator)

**fmJAML:**
```fmjaml
debug_log = =
List (
    "Line: " & $line ;
    "Error: " & $last_error
)
===
```

**JSON** (if `$line = 27` and `$last_error = 0`):
```json
{"debug_log":"Line: 27\rError: 0"}
```

### How it works

```text
value = Evaluate ( «multiline-calculation» )
```

The entire calculation is evaluated. The closing `===` marks the end of the block. FileMaker's `List` function separates nonempty entries with a carriage return.

A calculation can also be evaluated without a path, for its side effects, without changing the JSON:

```fmjaml
= Let ( $foo = "bar" ; "" )
```

Or as a herecalc:

```fmjaml
= =
Let ( [
    $foo = "bar" ;
    $answer = 42
] ; "" )
===
```

### Syntax

```fmjaml
«path» = =
«multiline-calculation»
===
```

## Value operator `==` (single-line text-value / 'hereline' operator)

**fmJAML:**
```fmjaml
text = == Hello
expression = == 6 * 7
```

**JSON:**
```json
{"text":"Hello","expression":"6 * 7"}
```

The fmJAML text **`Hello`** is the JSON value **`Hello`**. The text **`6 * 7`** remains text; it is not calculated.

### How it works

```text
value = «single-line-text-as-is»
```

Conceptually:

```text
value = Evaluate ( Quote ( «single-line-text» ) )
```

### Syntax

```fmjaml
«path» = == «single-line-text»
```

## Value operator `===` (multiline text-value operator)

**fmJAML:**
```fmjaml
message = ===
Hello Foo,
said Baz
in the bar.
===
```

**JSON** (showing FileMaker carriage-return line separators):
```json
{"message":"Hello Foo,\rsaid Baz\rin the bar."}
```

The source text **Hello Foo, / said Baz / in the bar.** is the resulting multiline JSON string value.

### How it works

```text
value = «multiline-text-as-is»
```

Conceptually:

```text
value = Evaluate ( Quote ( «multiline-text» ) )
```

The terminating `===` is not part of the text.

### Syntax

```fmjaml
«path» = ===
«multiline-text»
===
```

## Value operator `====` (single-line text-to-quote operator)

**fmJAML:**
```fmjaml
calculation = ==== "Hello Foo", said Baz in the bar.
```

**JSON** (containing a quoted FileMaker calculation):
```json
{"calculation":"\"\\\"Hello Foo\\\", said Baz in the bar.\""}
```

The fmJAML text **`"Hello Foo", said Baz in the bar.`** is transformed into the FileMaker calculation **`"\"Hello Foo\", said Baz in the bar."`**. Evaluating that calculation gives exactly **`"Hello Foo", said Baz in the bar.`**.

### How it works

**Text-to-quote takes the text as written and produces a FileMaker calculation which evaluates back to exactly that text.**

```text
value = Quote ( «single-line-text» )
```

This is useful wherever the generated JSON value will itself be evaluated later as a FileMaker calculation.

### Syntax

```fmjaml
«path» = ==== «single-line-text»
```

## Value operator `=====` (multiline text-to-quote operator)

**fmJAML:**
```fmjaml
calculation = =====
"Hello Foo",
said Baz
in the bar.
===
```

**JSON** (the intermediate calculation string, with carriage returns escaped for JSON):
```json
{"calculation":"\"\\\"Hello Foo\\\",\rsaid Baz\rin the bar.\""}
```

Evaluating the resulting FileMaker calculation produces this exact text, including its line breaks:

```text
"Hello Foo",
said Baz
in the bar.
```

### How it works

```text
value = Quote ( «multiline-text» )
```

The complete block is turned into a FileMaker calculation which evaluates back to the original text. The terminating `===` is not included.

### Syntax

```fmjaml
«path» = =====
«multiline-text»
===
```

## The pattern

```text
                  single line     multiline
calculation            ·              =
text                   ==             ===
text-to-quote          ====           =====
```

The number of equals signs identifies both the kind of value and whether it occupies one line or a block. The six forms are one visual system, rather than six unrelated syntaxes.

> **Verification note:** The quoted-calculation JSON examples above illustrate the expected `Quote` semantics. Verify the exact escaping and line-ending output against fmJAML's automated tests before treating them as normative.
