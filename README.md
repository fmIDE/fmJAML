```
    __                _         __  __ _
   / _|              | |  /\   |  \/  | |
  | |_ _ __ ___      | | /  \  | \  / | |
  |  _| '_ ` _ \ _   | |/ /\ \ | |\/| | |
  | | | | | | | | |__| / ____ \| |  | | |____
  |_| |_| |_| |_|\____/_/    \_\_|  |_|______|
```

# fmJAML
fmJAML -  FileMaker JaSON Markdown Language - is a simple `path=value` format for writing JSON structures in a machine and human readable shorthand format.

fmJAML supports 

- append array path shorthand `[+]`/`[:]`
- DRY paths by inheriting empty keys `..`
- multiline values `===`
- data type specification `;…;`
  - data types `T`, `N`, `D`, …
  - optional values `N?`
  - conditional values `?N:T`
  - simple data manipulation
    - trim `*`
    - format `*****`
    - not value `!0`
    - add value `missing value`

Example

FIXME - simple(ish) (real-world) example


fmJAML was originally conceived for specifying fmIDE Action Scripts, but is (currently aims to be) suitable for defining any JSON.

fmJAML is only 'FileMaker' JAML in so far that it is inspired by a cut down version the FileMaker JSONSetElement format, it uses FileMaker keyboard shortcut letters to specify data type, and a fmJAML custom function exists to interpret fmJAML, but apart from that it has no direct dependency on FileMaker and could be used outside of the FileMaker world.

