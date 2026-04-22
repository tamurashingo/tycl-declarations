# TyCL Declarations

Type declaration files for Common Lisp libraries, used by [TyCL](https://github.com/tamurashingo/tycl) type checker.

Similar to TypeScript's `.d.ts` files, `.d.tycl` files provide type information for external libraries without modifying the original source code.

## Package Structure

Each package in `packages/` has the following structure:

```
packages/<package-name>/
  meta.sexp                          # Package metadata (optional, for versioned packages)
  <package-name>.d.tycl              # Unversioned declaration file
  <package-name>-<version>.d.tycl    # Versioned declaration files (optional)
```

Packages can be **unversioned** or **versioned**:

- **Unversioned**: Only `<package-name>.d.tycl` exists. No `meta.sexp` or versioned files. Suitable for libraries where version-specific type differences are not needed.
- **Versioned**: Has `meta.sexp` and versioned declaration files. `tycl install` resolves the latest version from `meta.sexp`.

`meta.sexp` contains package metadata used by `tycl install` to resolve versions:

```lisp
(:package "cl-ppcre"
 :latest "0.2.0"
 :versions ("0.1.0" "0.2.0"))
```

When `meta.sexp` is not present, `tycl install` downloads the unversioned file directly.

## Examples

The `packages/` directory contains declaration files that can be used as references:

- [common-lisp](packages/common-lisp/) - Common Lisp standard library
- [cl-batis](packages/cl-batis/) - cl-batis SQL mapping library

## Usage

### Installing declarations with TyCL

```bash
# Install latest version
tycl install common-lisp

# Install a specific version
tycl install cl-ppcre 0.2.0

# Install all dependencies from an .asd file
tycl install-from my-project.asd
```

Downloaded files are saved to `tycl-declarations/` in the current directory.

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

Use the `add-package` script to create a new package:

```bash
# Unversioned (no meta.sexp, just the declaration file)
ros roswell/add-package.ros <package-name>

# Versioned (creates meta.sexp and versioned declaration file)
ros roswell/add-package.ros <package-name> <version>
```

Edit the generated `.d.tycl` file to add type declarations and submit a pull request.

### Adding a New Version to an Existing Package

Use the `add-version` script to add a new version:

```bash
ros roswell/add-version.ros <package-name> <version>
```

This copies the current unversioned declaration file as a new versioned file and creates or updates `meta.sexp`. This also works on unversioned packages — `meta.sexp` is created automatically on the first version.

After adding a version, edit the unversioned file with updated type definitions and copy it to the versioned file:

```bash
cp packages/<pkg>/<pkg>.d.tycl packages/<pkg>/<pkg>-<version>.d.tycl
```

## License

MIT
