---
name: code-style
description: Apply the user's personal code style when writing, editing, or reviewing code, particularly blank lines between logical blocks, multiline statements, and control flow. Applies across languages; style-only requests preserve behavior.
---

# Code style

Organize code into visual blocks separated by a single blank line. The recorded preference concerns spacing and grouping; preserve other project conventions.

## Spacing

- Keep consecutive single-line declarations together when they belong to the same logical step. Avoid mechanically inserting blank lines between variables.
- Separate every complete statement spanning two or more lines from preceding and following code with a blank line. This includes declarations, standalone calls, chained calls, and returns. Keep the statement's internal arguments, elements, and continuation lines together.
- Separate distinct logical steps with a blank line, even when each step occupies only one line.
- Separate control-flow blocks (`if`, loops, `match`, `switch`, `try`, and equivalents) from surrounding code with a blank line. Keep associated branches, such as `else` and `catch`, connected according to the language's syntax.
- Apply the same grouping inside functions and loops. Preserve the specific Rust declaration-and-check exception below; it is not a general exemption for any setup statement preceding a condition.
- Separate the final return from the preceding step with a blank line.
- Use truly empty lines, without spaces or tabs. At body boundaries, start and end directly with code, without blank lines immediately inside opening or closing delimiters. A boundary does not require padding merely because the first or last statement spans multiple lines.

## Examples

Pseudocode illustrates visual grouping only. Use the target language's actual syntax and idioms.

### Related short declarations and control flow

```text
function verify(directory) {
    root = open(directory)
    manifest = read(root, "manifest")
    snapshot = parse(manifest)

    if snapshot.version != supportedVersion {
        fail("Unsupported version")
    }

    validate(snapshot)

    return snapshot
}
```

### Complete multiline statements

Declarations and standalone calls receive spacing on both sides; their internal lines remain together.

```text
validate(request)

files = openDirectory(
    root,
    "files",
    readOnly
)

expected = emptySet()

checkFiles(
    files,
    expected
)

return expected
```

## Application and verification

When modifying a function or method, normalize spacing throughout that function or method. Leave other functions untouched unless the task includes them. Outside functions, apply these rules to the code being written or changed.

For style-only requests, preserve behavior, names, and structure. Examples establish spacing preferences, not preferences about APIs, architecture, error messages, or abstractions.

Before delivering, inspect the diff: related short declarations remain grouped; complete multiline statements have spacing on both sides where adjacent code exists; controls and distinct steps are separated; the Rust exception remains intact where applicable; untouched functions have no incidental formatting changes. If you run the project's formatter, inspect the final result. When a mandatory project rule prevents this spacing, report the concrete conflict and preserve the existing configuration.
