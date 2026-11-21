---
layout: page
title: Introduction
---

## What is fmJAML?

**fmJAML — FileMaker JSON as a Markdown Language — is a compact, line-oriented domain-specific language for constructing JSON.**

Its basic shape is deliberately simple:

```fmjaml
path = value
```

For example:

```fmjaml
person.name = "Alice"
person.age = 42
```

produces:

```json
{
  "person": {
    "name": "Alice",
    "age": 42
  }
}
```

The aim is to move JSON construction away from deeply nested function calls and escaped strings and into readable text.

## Why fmJAML exists

FileMaker's `JSONSetElement` is powerful, but large JSON structures quickly become difficult to author and maintain when expressed as calculation code.

fmJAML keeps the useful FileMaker path model while adding a text-first authoring layer:

- one element per line
- compact path notation
- array append and current-item notation
- path inheritance for DRY structures
- calculated values where calculations are useful
- literal and multiline text without manually escaping the contents
- typed, optional, mapped, and cleaned values through the Data Processing String

The result is intended to be readable by both people and machines: **AI + U + ME**.

## Design goals

fmJAML aims to be:

- **concise** without hiding what the JSON structure is
- **readable as text**
- **friendly to FileMaker developers**
- **easy to generate and edit with AI**
- **deterministic**
- **composable**
- **safe from unnecessary escaping work**

The value operators are specifically designed so that, when you do not want to escape a string manually, you do not have to. See [Value operators](value-operators.md).

## Non-goals

fmJAML is not:

- JSON itself
- a Markdown parser
- a replacement for the FileMaker calculation language
- a general-purpose programming language

It is a small language for describing and constructing JSON.

## Relationship to JSONSetElement

fmJAML deliberately borrows FileMaker's JSON path ideas.

For example, array notation such as `[+]` and `[:]` feels familiar to developers already using FileMaker JSON functions:

- `[+]` appends an element
- `[:]` refers to the current / last element when continuing a structure

fmJAML extends that model with text-oriented features such as path inheritance, value operators, multiline blocks, and the Data Processing String.

## Relationship to FileMaker calculations

The right-hand side of a normal fmJAML assignment is a FileMaker calculation.

```fmjaml
answer = 6 * 7
today = Get ( CurrentDate )
```

fmJAML evaluates those expressions while constructing the JSON.

Literal text is handled by the value-operator forms instead of requiring you to turn every string into an escaped FileMaker calculation. See [Value operators](value-operators.md).

## Relationship to fmIDE

fmJAML was originally created for **fmIDE Action Scripts**.

That is still an important use case, but fmJAML itself is intentionally more general: it can describe any JSON structure.

When fmJAML is used inside **fmIDEAS**, there can be a second FileMaker-calculation evaluation layer at action-execution time. That fmIDE-specific behaviour belongs in the fmIDE documentation; the fmJAML documentation describes the language itself.

## Implementations

A FileMaker custom function implementation exists today.

The language is not intended to depend conceptually on FileMaker forever: another implementation can parse the same fmJAML text and produce the same JSON, provided it implements the language semantics consistently.
