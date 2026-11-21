---
layout: page
title: fmJAML documentation
---

**fmJAML — FileMaker JSON as a Markdown Language — is a compact, line-oriented language for constructing JSON in a form that is easy for both people and AI to read and write.**

## A taste of fmJAML

The example that inspired this documentation shows several fmJAML ideas at once:

```fmjaml
[+].fmJAML
[:]..name[+] = "FileMaker JaSON Markdown Language"
[:].       ] = "FileMaker JSON A Markdown Language"
[:].       ] = "FileMaker JSON As a Markdown Language"
[:]..date_of_inspiration = ;D; Date ( 3 ; 10 ; 2026 )
[:]..implementation_count = 1
[:]..implementations[+] = "fmJAML Custom Function"
[:]..is_cool = true
[:]..text_logo = ===
    __                _         __  __ _
   / _|              | |  /\   |  \/  | |
  | |_ _ __ ___      | | /  \  | \  / | |
  |  _| '_ ` _ \ _   | |/ /\ \ | |\/| | |
  | | | | | | | | |__| / ____ \| |  | | |____
  |_| |_| |_| |_|\____/_/    \_\_|  |_|______|
===
```

produces:

```json
[
  {
    "fmJAML": {
      "date_of_inspiration": "2026-03-10",
      "implementation_count": 1,
      "implementations": [ "fmJAML Custom Function" ],
      "is_cool": true,
      "name": [
        "FileMaker JaSON Markdown Language",
        "FileMaker JSON A Markdown Language",
        "FileMaker JSON As a Markdown Language"
      ],
      "text_logo": "    __                _         __  __ _\r   / _|              | |  /\\   |  \\/  | |\r  | |_ _ __ ___      | | /  \\  | \\  / | |\r  |  _| '_ ` _ \\ _   | |/ /\\ \\ | |\\/| | |\r  | | | | | | | | |__| / ____ \\| |  | | |____\r  |_| |_| |_| |_|\\____/_/    \\_\\_|  |_|______|"
    }
  }
]
```

It combines arrays, path inheritance, automatic simple types, an explicit date type, and a multiline text value — while the JSON structure remains visually apparent in the source.

## Start here

1. [Introduction](introduction.md) — what fmJAML is, why it exists, and how it relates to JSON, JSONSetElement, FileMaker calculations, and fmIDE.
2. [Building JSON structures](json-structures.md) — objects, arrays, paths, inheritance, and DRY construction.
3. [Value operators](value-operators.md) — calculations, literal text, multiline blocks, and text-to-quote.
4. [Data Processing String (DPS)](dps.md) — data types, optional values, trimming, mapping, replacement, and related operators.

## Documentation home

This `docs/` directory is the canonical home of the fmJAML documentation. The GitHub wiki is deprecated as a documentation location.

Documentation lives with the code so changes to implementation, tests, and documentation can be reviewed and versioned together.

## Documentation principles

- **example first** — show fmJAML and the resulting JSON before explaining the machinery
- **explicit** — avoid invisible semantics
- **versioned with the code** — documentation changes belong in pull requests
- **split by concept** — the repository README is a front door, not the language reference
