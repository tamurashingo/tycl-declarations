# TyCL Declarations

Type declaration files for Common Lisp libraries, used by [TyCL](https://github.com/tamurashingo/tycl) type checker.

Similar to TypeScript's `.d.ts` files, `.d.tycl` files provide type information for external libraries without modifying the original source code.

## Examples

The `packages/` directory contains sample declaration files that can be used as references:

- [common-lisp](packages/common-lisp/) - Common Lisp standard library
- [cl-batis](packages/cl-batis/) - cl-batis SQL mapping library

## Usage

### Project-local declarations

Place `.d.tycl` files in a `tycl-declarations/` directory at your project root:

```
my-project/
  my-project.asd
  tycl-declarations/
    cl-ppcre.d.tycl
    alexandria.d.tycl
  src/
    main.tycl
```

TyCL automatically loads declarations from this directory when running `check-all` or `transpile-all`.

### User-global declarations

Place `.d.tycl` files in `~/.config/tycl/declarations/` to make them available across all projects.

## Writing Declaration Files

Use `template.d.tycl` as a starting point:

```lisp
;;; -*- mode: lisp -*-
;;;; Type declarations for <LIBRARY-NAME>
;;;;
;;;; Description:    <brief description of the library>
;;;;
;;;; Original Repository: <URL of the original library's repository>
;;;; Original License:    <license of the original library>
;;;;
;;;; Declared by:    <author of this declaration file>

(in-package #:<PACKAGE-NAME>)

;; Function declarations
(defun [function-name :return-type] ([param1 :type1] [param2 :type2]))

;; Multiple return values
(defun [floor (:values :integer :number)] ([number :number] &optional [divisor :number]))

;; Variable declarations
(defvar [*variable-name* :type])
```

### Type Syntax

- Basic types: `:integer`, `:string`, `:boolean`, `:number`, `:t` (any), `:null`, `:void`, etc.
- Generic types: `(:list (:string))`, `(:hash-table (:string) (:integer))`
- Union types: `(:integer :string)`, `((:list (:string)) :null)`
- Multiple values: `(:values :integer :number)`
- Parameters: `&optional`, `&key`, `&rest` are supported

See the [TyCL README](https://github.com/tamurashingo/tycl) for full type syntax documentation.

## Contributing

### Reporting Issues

If you find incorrect or missing type declarations, please [open an issue](../../issues/new?template=bug_report.yml).

### Adding New Packages

1. Create a directory under `packages/<package-name>/`
2. Copy `template.d.tycl` to `packages/<package-name>/<package-name>.d.tycl`
3. Fill in the header (library name, repository URL, license, your name)
4. Add type declarations
5. Submit a pull request using the new package template

## License

MIT
