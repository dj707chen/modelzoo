# Type Stubs and Typeshed: Learning Notes

## Questions & Answers

### Q1: What is `dest` at line 56 in add_subparsers?

`dest="cmd"` on line 56 is an `argparse` argument for `add_subparsers`. It tells argparse **which attribute name to use** on the parsed `Namespace` object to store the name of the sub-command the user typed.

**Example:**
- If you run `cszoo fit ...`, then after parsing, `args.cmd == "fit"`
- You can then do `if args.cmd == "fit": ...` to branch on whichever sub-command was selected
- Without `dest`, argparse wouldn't attach the chosen sub-command name anywhere on the result namespace

---

### Q2: How did command-clicking take me to argparse.pyi in typeshed-fallback?

That's the expected behavior with VS Code + Pylance. Here's what's happening:

**Type stubs (`.pyi` files)** are "type hint" files that define function signatures and types without implementation. When you use "Go to Definition" (Cmd/Ctrl+Click) on a standard library function, Pylance prioritizes the `.pyi` stub from **typeshed** over the actual `.py` implementation because:

1. **Cleaner type information**: Stubs contain just the signatures with full type annotations, making them much easier to read than the actual CPython or implementation code.

2. **Authoritative types**: The typeshed stubs are the canonical source of type information that type checkers and IDEs use. They're maintained separately from the implementation.

3. **Better IDE experience**: Showing you the stub gives you:
   - Clear function signatures with all overloads
   - Parameter types and return types
   - Docstrings
   - No implementation clutter

---

### Q3: How can I locate the currently open file in the Explorer left pane?

Use one of these methods:

**Quick Reveal (keyboard):**
- **Windows/Linux**: `Ctrl+K Ctrl+R`
- **Mac**: `Cmd+K Cmd+R`

This runs the "Reveal Active File in Side Bar" command.

**Alternative methods:**
1. **Right-click the file tab** → Select "Reveal in Explorer"
2. **Click the breadcrumb** at the top of the editor — this shows the file path and highlights it in Explorer
3. **Enable auto-reveal** in settings: Open Settings (`Ctrl+,`) and search for `"explorer.autoReveal"`, set it to `"true"` (or `"focusAndExpand"` for more aggressive tracking)

**Note:** For files inside extension folders (like `argparse.pyi` inside Pylance), they might not appear in your workspace Explorer by default.

---

### Q4: How does Pylance know that `dest` is one of the kwargs for add_subparsers?

The official Python version has:
```python
def add_subparsers(self, **kwargs):
```

But Pylance doesn't read the runtime Python code—it reads the `.pyi` **type stub** file instead.

In the stub file, `add_subparsers` is explicitly declared with all parameters:

```python
@overload
def add_subparsers(
    self: _ArgumentParserT,
    *,
    title: str = "subcommands",
    description: str | None = None,
    prog: str | None = None,
    action: type[Action] = ...,
    option_string: str = ...,
    dest: str | None = None,  # ← explicitly declared here
    required: bool = False,
    help: str | None = None,
    metavar: str | None = None,
) -> _SubParsersAction[_ArgumentParserT]: ...
```

**Type stubs are the authoritative source of type information for type checkers.** They:

1. **Expand `**kwargs`** into explicit named parameters so IDEs know what's valid
2. **Document types** that aren't visible in runtime code
3. **Provide overloads** for different call patterns

---

### Q5: Do typeshed maintainers manually trace the code to come up with type signatures?

Yes, exactly. The typeshed maintainers **manually inspect the source code** to reverse-engineer accurate type signatures, since many libraries (especially standard library) don't have built-in type hints.

**For `argparse.pyi` specifically, they would:**

1. **Read the CPython source code** (`/Lib/argparse.py` in the Python repo)
2. **Consult the official docs** to understand parameter meanings and valid values
3. **Study test cases** to understand expected behavior and edge cases
4. **Write and run tests** against the `.pyi` to ensure the signatures are correct
5. **Use community expertise** — the typeshed project accepts PRs from community members familiar with specific libraries

It's **manual detective work**, not automated. The stub authors essentially ask:
- What parameters does this function actually accept?
- What types are they?
- What does it return?
- Are there multiple valid call patterns (→ `@overload`)?

**Why this matters:**
Without stubs, type checkers would treat everything as `Any` and lose all type safety for standard library code. The typeshed project essentially bootstraps type information for the entire Python ecosystem.

---

## Key Takeaways

1. **Type stubs (`.pyi`)** are the source of truth for type checkers, not the actual implementation
2. **Typeshed** is the standard library of type hints maintained by the community
3. **Pylance** uses these stubs to provide IDE features like autocomplete and "Go to Definition"
4. **Manual curation** is necessary because most legacy code (like CPython) doesn't have inline type hints
5. **Overloads** allow handling multiple valid call patterns with different types
