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

fmJAML - **F**ile**M**aker **Ja**SON **M**arkdown **L**anguage -  is a simple but powerful `path=value` templating language for writing JSON structures in a shorthand format suitable for both 'Machine and Mensch' - for AI+ME+U!

**🚧 fmJAML, and particularly this documentation, is still under construction! 🚧**

For example, the following fmJAML snippet

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

constructs this JSON

```json
[
  {
    "fmJAML" : 
    {
      "date_of_inspiration" : "2026-03-10",
      "implementation_count" : 1,
      "implementations" : [ "fmJAML Custom Function" ],
      "is_cool" : true,
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

fmJAML supports

- **array, object, table construction**
  - shorthand for appending to the array or adding to the last object in the array:  
   `[+]`/`[:]`
  - shorthand for constructing an object:  
    `[+].fmJAML` -> `[{ "fmJAML" : {} }]`
  - shorthand for adding object properties -> just leave the keys empty:  
    `[:]..is_cool = true` -> `[{ "fmJAML" : { "is_cool" : true } }]`
- **value definition**
  - all values are evaluated  
    `[:]..is_for = "AI" & "U" & "ME"` -> `[{ "fmJAML" : { "is_for" : "AIUME" } }]`
  - shorthand for null values:  
    `optional_thing =` -> `{ "optional_thing" : null }`
  - no escaping needed! Use a value operator (`==`, `===`, `====`, `=====`) to embed text directly into your fmJAML without escaping.
    - embed a line of text:

      ```yaml
      avoid_escaping = == "Hi Foo!" said Baz at the bar
      ````

      ->

      ```json
      { "avoid_escaping" : "\"Hi Foo!\" said Baz at the bar" }`
      ```

    - embed a block of text:

      ```yaml
      scene_1 = ===
      Foo: "What's a DSL Baz?"
      Baz: "A DSL is a Domain Specific Language, Foo."
      Foo: 🤯
      ===
      ```

      ->

      ```json
      { "scene_1" : "Foo: \"Whats a DSL Baz?\"\rBaz: \"A DSL is a Domain Specific Language, Foo.\"\rFoo: 🤯" }
      ```

    - (You can even embed calculations (for fmIDE) - juist add two more `=`s
- **simple data type specification**
  - numbers and booleans are recognized automatically

    ```yaml
    [:]..implementation_count = 1
    [:]..is_cool = true
    ```

    ->

    ```json
    {
      "implementation_count" : 1,
      "is_cool" : true
    }
    ```

  - data types are specified by a single letter in the data processing string between `=` and value, separated by a semicolons:

    ```yaml
    start_ms = ;N; Get ( CurrentTimeUTCMilliseconds )
    current_date = ;D; Get ( CurrentDate )
    current_time = ;I; Get ( CurrentTime )
    ```

    ->

    ```json
    {
      "current_date" : "2026-06-29",
      "current_time" : "18:52:00",
      "start_ms" : 63918346916749
    }
    ```

  - much more is possible with the data processing string (DPS): `;DPS;`
    - data types: `T`, `N`, `B`, `D`, `I`, `M`, `R`
    - simple data manipulation:
      - trim/format: `*` / `**` / `***` / `****` / `*****`
      - output value: `'missing value'`
      - not value: `!'0'`
      - EOL operator `¶`:
        - use first line only: `¶1`
        - remove EOLs: `¶''`
        - replace EOLs: `¶' '`
    - conditionals
      - optional operator `?` (=test for non-empty value)
        - optional elements: `N?`
          - conditional data type: `?N:T`
      - mapping operator `-` (=test for empty value)
      - else operator `:`
    - powerful constructs due to operator composablity:
      - `B!0?` (optional truelean)
      - `***?*****` (optional pretty printed JSON)
      - `!'0''1'-:'illegal value' (error handling) 

# Further Example

Here is an fmJAML template to construct a feedback object

```fmjaml
feedback
.title=;¶'//'**;$title
.text=;***;$text
.priority=;**N?N:N'2';$priority
.name=;***¶1?T:'anonymous';$name
.email=;***¶1?T:'error:missing';$email
.timestamp=;M;Get ( CurrentTimestamp )
```

fmJAML was originally conceived for specifying fmIDE Action Scripts in a concise format, but has been extracted from that context since it is (or aims to be) suitable for defining any kind of JSON.

fmJAML is 'FileMaker' JAML in so far that it is inspired by a cut down version the FileMaker JSONSetElement format, it uses FileMaker keyboard shortcut letters to specify data type, and a fmJAML custom function exists to interpret fmJAML, but apart from that it has no direct dependency on FileMaker and could be used outside of the FileMaker world.

See the [fmJAML documentation](https://github.com/fmIDE/fmJAML/wiki) for more information.

# User Quotes

2026-05-13 ChatGPT:

> fmJAML has evolved from: “JSON shorthand” into “a compact line-oriented typed JSON transformation DSL for FileMaker” which is substantially more ambitious.
> And honestly… quite successful now.
