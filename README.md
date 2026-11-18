# fmJAML

```text
    __                _         __  __ _
   / _|              | |  /\   |  \/  | |
  | |_ _ __ ___      | | /  \  | \  / | |
  |  _| '_ ` _ \ _   | |/ /\ \ | |\/| | |
  | | | | | | | | |__| / ____ \| |  | | |____
  |_| |_| |_| |_|\____/_/    \_\_|  |_|______|
  code from your keyboard
```

**fmJAML — FileMaker JSON as a Markdown Language — is a compact, line-oriented language for constructing JSON in a form that is easy for both people and AI to read and write.**

👉 **[Read the fmJAML documentation](docs/README.md)**

> **Status:** fmJAML and its documentation are still evolving. The syntax is usable, but some areas remain under active development.

A small example:

```fmjaml
person
.name = "Alice"
.age = 42
.is_admin = false
```

produces:

```json
{
  "person": {
    "name": "Alice",
    "age": 42,
    "is_admin": false
  }
}
```

fmJAML is designed to make JSON construction concise without making it cryptic. It supports:

- object and array construction with simple paths
- path inheritance for DRY structures
- calculated values
- literal text and multiline text without manual escaping
- typed and optional values through the Data Processing String (DPS)
- FileMaker-friendly array operators such as `[+]` and `[:]`

fmJAML was originally created for defining **fmIDE Action Scripts**, but the language itself is intended for constructing any JSON. A FileMaker custom function implementation exists today; other implementations can follow the same language.

## Documentation

- [Documentation home](docs/README.md)
- [Introduction](docs/introduction.md)
- [Building JSON structures](docs/json-structures.md)
- [Value operators](docs/value-operators.md)
- [Data Processing String (DPS)](docs/dps.md)

## Licence

See [LICENSE](LICENSE).
