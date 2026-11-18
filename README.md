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

👉 **[Read the fmJAML documentation](docs/index.md)**

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

fmJAML is designed to make JSON construction concise without making it cryptic. It supports object and array construction, DRY paths, calculated values, literal and multiline text without manual escaping, and typed/optional values through the Data Processing String.

fmJAML was originally created for **fmIDE Action Scripts**, but the language itself is intended for constructing any JSON.

## Documentation

- [Documentation home](docs/index.md)
- [Introduction](docs/introduction.md)
- [Building JSON structures](docs/json-structures.md)
- [Value operators](docs/value-operators.md)
- [Data Processing String (DPS)](docs/dps.md)

## Licence

See [LICENSE](LICENSE).
