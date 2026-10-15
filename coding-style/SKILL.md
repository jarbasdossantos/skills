---
name: code-style
description: Apply the user's personal code style when writing, editing, or reviewing code: blank lines between logical blocks, flat control flow with guard clauses instead of if/else, Clean Code naming and functions, and SOLID design. Applies across languages; style-only requests preserve behavior.
---

# Code style

Organize code into visual blocks separated by a single blank line, keep control flow flat, and follow Clean Code and SOLID. The recorded preference concerns spacing, grouping, control flow, naming, functions, and class design; preserve other project conventions.

## Spacing

- Keep consecutive single-line declarations together when they belong to the same logical step. Avoid mechanically inserting blank lines between variables.
- Separate every complete statement spanning two or more lines from preceding and following code with a blank line. This includes declarations, standalone calls, chained calls, and returns. Keep the statement's internal arguments, elements, and continuation lines together.
- Separate distinct logical steps with a blank line, even when each step occupies only one line.
- Separate control-flow blocks (`if`, loops, `match`, `switch`, `try`, and equivalents) from surrounding code with a blank line. Keep associated branches, such as `else` and `catch`, connected according to the language's syntax.
- Apply the same grouping inside functions and loops. Preserve the specific Rust declaration-and-check exception below; it is not a general exemption for any setup statement preceding a condition.
- Separate the final return from the preceding step with a blank line.
- Use truly empty lines, without spaces or tabs. At body boundaries, start and end directly with code, without blank lines immediately inside opening or closing delimiters. A boundary does not require padding merely because the first or last statement spans multiple lines.

## Control flow

Write **flat** code: handle edge cases first with **guard clauses**, then let the happy path run to the end of the function at the outermost indentation level.

- Open functions and loop bodies with guard clauses: check invalid input, missing data, and edge cases up front and exit immediately with `return`, `throw`, `continue`, or `break`.
- When a branch exits, close its block and continue the remaining path after it at the same level. `else` and `else if` appear only when both paths fall through to shared code after the conditional.
- Keep at most one level of conditional nesting inside a function body. When logic needs more, invert the condition into a guard or extract the inner block into a named function.
- Map values with a lookup structure (map, dictionary, object literal) instead of an `if`/`else if` chain or `switch` whose cases only return or assign a value. Handle unknown keys explicitly with a default or an error.
- Dispatch on a closed set of variants that carry different behavior with the language's exhaustive construct: `match` in Rust, a discriminated-union `switch` with an exhaustiveness check in TypeScript, `match` in Python, `when` over a sealed type in Kotlin.
- Assign each variable once. Compute a conditional value with a lookup, an expression (`if` expression in Rust or Kotlin, a single ternary elsewhere), or a helper function that returns it, rather than declaring it and reassigning it in branches.
- Reserve ternaries for choosing between two simple values; express anything larger as a guard, lookup, or helper function.
- Name compound conditions: assign a multi-part boolean expression to a descriptive variable or extract it into a predicate function, then branch on the name.
- Use the language's early-exit idioms: `?` and `let ... else` in Rust, `guard` in Swift, optional chaining and nullish coalescing in TypeScript and JavaScript.

## Clean Code

### Names

- Use intention-revealing names that say what a value means in the domain: `elapsedDays`, `activeUsers`, `invoiceTotal`. Spell words out; keep only abbreviations universal in the field (`id`, `url`, `http`).
- Name functions with verbs (`calculateTotal`, `sendInvoice`), classes and values with nouns (`Invoice`, `retryPolicy`), and booleans as predicates (`isExpired`, `hasItems`, `canRetry`).
- Choose a specific name over a generic container word: `PriceCalculator` or `formatCurrency` rather than `Manager`, `Helper`, `Utils`, `data`, or `info`.
- Use one word per concept across the codebase: pick `fetch`, `get`, or `load` for the same idea and keep it.
- Replace magic numbers and strings with named constants: `MAX_LOGIN_ATTEMPTS`, `DEFAULT_TIMEOUT_MS`.

### Functions

- Each function does one thing at one level of abstraction. A high-level function reads as a sequence of named steps; the details live in the functions it calls, ordered below it (the stepdown rule).
- Take at most three parameters. Group related values into an object or type that names the concept.
- Replace boolean flag parameters with two functions named for each behavior: `renderPreview()` and `renderFinal()` rather than `render(isPreview)`.
- Separate commands from queries: a function either changes state or returns information. Keep side effects visible in the name; `getUser` reads, `ensureUser` may create.
- Signal failure with exceptions or the language's result type (`Result` in Rust, `Either` or a discriminated union in TypeScript) instead of error codes or sentinel values. Return empty collections rather than `null`.
- Extract duplicated knowledge into one source of truth. Duplicated lines that represent different rules may stay separate.
- Talk to direct collaborators only (Law of Demeter): `order.shippingCity()` rather than `order.getCustomer().getAddress().getCity()`.

### Comments

Let names and structure express what the code does; write comments for why: a business rule, a workaround, a non-obvious constraint. Delete commented-out code.

## SOLID

Apply SOLID when designing or changing classes, modules, and interfaces:

- **Single Responsibility**: each class or module has one reason to change. Split code that mixes concerns such as I/O, domain rules, and presentation.
- **Open/Closed**: extend behavior for an open set of variants (plugins, strategies, providers) by adding a new implementation rather than editing existing branches. Closed sets keep the exhaustive dispatch from the control-flow rules.
- **Liskov Substitution**: every subtype honors its parent's contract, accepting the same inputs and keeping the same guarantees. When a subtype would need to reject an inherited method, model the hierarchy differently.
- **Interface Segregation**: define small, role-specific interfaces so each client depends only on the methods it uses.
- **Dependency Inversion**: domain logic depends on abstractions and receives its dependencies through the constructor or parameters. Construct concrete infrastructure (databases, HTTP clients, clocks, file systems) at the composition root.

Introduce an abstraction when it pays for itself now: a second implementation, a test seam, or a boundary to infrastructure. Keep a single concrete class where none of those exists yet.

## Examples

Pseudocode illustrates visual grouping and control-flow shape only. Use the target language's actual syntax and idioms.

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

### Guard clauses

Each edge case exits on its own; the happy path stays unindented.

```text
function shippingCost(order) {
    if order.items.isEmpty() {
        return 0
    }

    if not order.address.isValid() {
        fail("Invalid address")
    }

    rate = ratesByRegion[order.address.region]
    weight = totalWeight(order.items)

    return rate * weight
}
```

The same shape applies inside loops:

```text
for user in users {
    if user.isInactive() {
        continue
    }

    notify(user)
}
```

### Lookup instead of a branch chain

```text
statusLabels = {
    "pending": "Waiting",
    "shipped": "On the way",
    "delivered": "Done"
}

label = statusLabels.get(status, "Unknown")
```

## Application and verification

When modifying a function or method, normalize spacing and control flow throughout that function or method. Leave other functions untouched unless the task includes them. Outside functions, apply these rules to the code being written or changed.

Apply the Clean Code and SOLID rules in full to code you write and to refactoring requests. For style-only requests, preserve behavior, names, public interfaces, and class boundaries. Restructure only as the control-flow rules require: guard clauses, removed `else` branches, lookups, extracted predicates or helpers. Keep each condition's semantics identical when inverting it, including short-circuit order and side effects. List the naming and SOLID improvements you found as suggestions in the response. Examples establish spacing and control-flow preferences, not preferences about APIs, error messages, or domain naming.

Before delivering, inspect the diff:

- Related short declarations remain grouped; complete multiline statements have spacing on both sides where adjacent code exists; controls and distinct steps are separated.
- Every edge case exits through a guard clause; every `else` that remains is followed by shared code; conditional nesting stays at one level; value-mapping chains are lookups.
- Every new or changed name reveals intent; every new or changed function does one thing, takes at most three parameters, and has no flag parameter; literals with meaning are named constants.
- Every new or changed class has one responsibility and receives its dependencies; every new abstraction has a second implementation, a test seam, or an infrastructure boundary behind it.
- The Rust exception remains intact where applicable; untouched functions have no incidental changes.

If you run the project's formatter or linter, inspect the final result. When a mandatory project rule prevents this style, report the concrete conflict and preserve the existing configuration.
