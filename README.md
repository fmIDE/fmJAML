**🚧 Note that fmJAML is still heavily under construction! 🚧**

```
    __                _         __  __ _
   / _|              | |  /\   |  \/  | |
  | |_ _ __ ___      | | /  \  | \  / | |
  |  _| '_ ` _ \ _   | |/ /\ \ | |\/| | |
  | | | | | | | | |__| / ____ \| |  | | |____
  |_| |_| |_| |_|\____/_/    \_\_|  |_|______|
```

# fmJAML

**F**ile**M**aker **J**SON **A** **M**arkdown **L**anguage

fmJAML is a simple but powerful `path=value` templating language for writing JSON structures in a machine and human readable shorthand format.

```
[+].fmJAML
[:]..name[+]="FileMaker JaSON Markdown Language"
[:].       ]="FileMaker JSON A Markdown Language"
[:].       ]="FileMaker JSON As a Markdown Language"
[:]..date_of_inspiration=;D;Date ( 3 ; 10 ; 2026 )
[:]..implementation_count=;N;1
[:]..text_logo====
    __                _         __  __ _
   / _|              | |  /\   |  \/  | |
  | |_ _ __ ___      | | /  \  | \  / | |
  |  _| '_ ` _ \ _   | |/ /\ \ | |\/| | |
  | | | | | | | | |__| / ____ \| |  | | |____
  |_| |_| |_| |_|\____/_/    \_\_|  |_|______|
===
```


fmJAML supports 

- append array path shorthand: `[+]`/`[:]`
- path inheritance  (=DRY paths!) via empty keys: `..`
- multiline values: `===`
- data type specification
  - shorthand for object and null data types (*no* `=` → `object`/ *only* `=` → `null`)
    ```
    [+].document
    [:]..selected=
    ```
  - data type specification string: `;…;`
    - data types: `T`, `N`, `B`, `D`, `I`, `M`, `R`
    - optional elements: `N?`
    - conditional data type: `?N:U`
    - simple data manipulation:
      - trim/format: `*` / `**` / `***` / `****` / `*****`
      - output value: `'missing value'`
      - not value: `!'0'`
       - EOL operator `¶`:
        - replace EOLs: `¶' '`
        - use first line only: `¶1`
    - composablity of operators:
      - `B!0?` (optional truelean)
      - `***?*****` (optional pretty printed JSON)

# For Example

The fmJAML above creates the following JSON

```
[
	{
		"fmJAML" : 
		{
			"date_of_inspiration" : "2026-03-10",
			"implementation_count" : 1,
			"name" : 
			[
				"FileMaker JaSON Markdown Language",
				"FileMaker JSON A Markdown Language",
				"FileMaker JSON As a Markdown Language"
			],
			"text_logo" : "    __                _         __  __ _\r   / _|              | |  /\\   |  \\/  | |\r  | |_ _ __ ___      | | /  \\  | \\  / | |\r  |  _| '_ ` _ \\ _   | |/ /\\ \\ | |\\/| | |\r  | | | | | | | | |__| / ____ \\| |  | | |____\r  |_| |_| |_| |_|\\____/_/    \\_\\_|  |_|______|"
		}
	}
]
```

Here is an fmJAML template to construct a feedback object

```
feedback
.title=;¶'//'**;$title
.text=;***;$text
.priority=;**N?N:N'2';$priority
.name=;***¶1?T:'anonymous';$name
.email=;***¶1?T:'error:missing';$email
.timestamp=;M;Get ( CurrentTimestamp )
```

fmJAML was originally conceived for specifying fmIDE Action Scripts in a concise format, but has been extracted from that context since it is (or aims to be) suitable for defining any kind of JSON.

fmJAML is only 'FileMaker' JAML in so far that it is inspired by a cut down version the FileMaker JSONSetElement format, it uses FileMaker keyboard shortcut letters to specify data type, and a fmJAML custom function exists to interpret fmJAML, but apart from that it has no direct dependency on FileMaker and could be used outside of the FileMaker world.

