# Changelog

## 1st Nov 2024

- Arithmetic operators:
  - Division operators can't have a wrapping or saturating version
  - Use `%` prefix for wrapping version (instead of `@`)
  - Removing skipping version of the operators
  - New extending type of operators
- Changing `=?`, `=!`, `>=?` `<=?` to `=?=`, `=/=`, `>?=`, `<?=`
- Leaving `_ |? _ |: _` for ternary operator and changing pattern matching to `>| _ => _`
- Clarifying syntax for generic closure function arguments with `fx _: F: a -> b => a, F -> b`
