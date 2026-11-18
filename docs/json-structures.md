# Building JSON structures

fmJAML builds JSON by assigning values to paths.

## Simple object properties

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

## Creating an object without assigning a value

A path on its own creates an object:

```fmjaml
person
```

produces:

```json
{
  "person": {}
}
```

## Null values

A path followed by `=` with no value creates a JSON null value:

```fmjaml
person.middle_name =
```

produces:

```json
{
  "person": {
    "middle_name": null
  }
}
```

## Arrays with `[+]` and `[:]`

Use `[+]` to append to an array and `[:]` to continue working with the current / last item.

```fmjaml
people[+].name = "Alice"
people[:].role = "Admin"

people[+].name = "Bob"
people[:].role = "User"
```

produces:

```json
{
  "people": [
    {
      "name": "Alice",
      "role": "Admin"
    },
    {
      "name": "Bob",
      "role": "User"
    }
  ]
}
```

This notation is especially useful for building table-like arrays of objects.

## Path inheritance

fmJAML can reuse parts of the previous path so repeated structure does not have to be written again.

A leading dot continues below the previous object path:

```fmjaml
person
.name = "Alice"
.age = 42
```

is equivalent to:

```fmjaml
person.name = "Alice"
person.age = 42
```

An empty path segment can inherit the corresponding segment from the previous path. For example:

```fmjaml
[+].person
[:]..name = "Alice"
[:]..age = 42
```

keeps the `person` segment from the previous path.

Path inheritance is a core DRY mechanism in fmJAML: keep common structure at the common root, then describe only what changes in each leaf.

## Pathless calculations for DRY construction

A calculation can be run without adding a JSON path.

If there is no path before the assignment, the line itself is dropped from the JSON construction, but the calculation is still evaluated.

That lets you define FileMaker variables at the common root of a set of branches and reuse them below:

```fmjaml
= Let (
    $tax_rate = GetTaxRate ( $country ) ;
    ""
)

invoice
.net = $net
.tax = $net * $tax_rate
.gross = $net * ( 1 + $tax_rate )
```

The same technique works with a multiline herecalc:

```fmjaml
= =
Let ( [
    $tax_rate = GetTaxRate ( $country ) ;
    $currency = GetCurrency ( $country )
] ; "" )
===

invoice.tax_rate = $tax_rate
invoice.currency = $currency
```

This keeps complicated shared calculations at the common root instead of repeating them in several branches or leaves.

## Values and multiline blocks

Structure and value handling are separate concerns.

Once the path is known, the value can be:

- a single-line calculation
- a multiline calculation
- literal text
- multiline literal text
- text converted into a quoted calculation

See [Value operators](value-operators.md) for the complete six-form system.

For data typing, optional values, mapping, trimming, and other transformations, see the [Data Processing String](dps.md).
