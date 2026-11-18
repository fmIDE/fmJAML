# fmJAML documentation

This `docs/` directory is the canonical home of the fmJAML documentation.

The GitHub wiki is deprecated as a documentation location. Documentation should live with the code so that changes to implementation, tests, and documentation can be reviewed and versioned together.

## Start here

1. [Introduction](introduction.md) — what fmJAML is, why it exists, and how it relates to JSON, JSONSetElement, FileMaker calculations, and fmIDE.
2. [Building JSON structures](json-structures.md) — objects, arrays, paths, inheritance, and DRY construction.
3. [Value operators](value-operators.md) — calculations, literal text, multiline blocks, and text-to-quote.
4. [Data Processing String (DPS)](dps.md) — data types, optional values, trimming, mapping, replacement, and related operators.

## Documentation principles

The documentation should be:

- **example first** — show the fmJAML and resulting JSON before explaining the machinery
- **explicit** — avoid invisible semantics
- **versioned with the code** — documentation changes belong in pull requests
- **split by concept** — the root README is an introduction, not the language reference

More reference pages can be added here as the language documentation grows.
