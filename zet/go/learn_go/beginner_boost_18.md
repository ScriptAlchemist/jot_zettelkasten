# Boost Week 18

Let's learn more GO!

## Greet

Create a command that greets the user in their common language as detected from the environment.

### Requirements

* Name the program `greet`
* Create an importable module named `greet` with a `greet` command
* If command arguments passed assume they are name
* Combine all arguments into single argument
* Write a separate function to read one line of standard input
* If no arguments passed, greet generically, and prompt for name
* Detect the language from the host system
* Switch to language detected and print simple greeting in the language
* Write code that could easily have additional languages added

Bonus:

* Add some interesting colors in the text output.

## Concepts

* Command line parameters
* Compound variables
* Maps

## Covered

* Creating a package (library) module
* Including commands within library modules
* Importing package
* Naming `main.go` and `<lib>.go` convention
* Testable examples
* Don't repeat yourself
* TDD vs Prototype Driven Development
* Avoid interactive input (in general), prefer arguments or `env` variables
* Runes
* Line endings: `CR LF`, `LF`, `CR`
* Danger of `line, _ := bufio.NewReader(os.Stdin).ReadString('\n')`
* Basics of Unicode
* Multiple return values
* Ignored name `_` for a return value
* Use of `internal` packages
* Golang error handling philosophy
* Don't forget to use `Printf("%q", thing)`
* Comment function documentation
* Checking documentation with `go doc -all .`
* `K` to look up the docs of a function in Vim
* `doc.go` convention to manage docs. Start with `Package internal` or
  whatever the package is.

## Related

* Testable Examples in Go - The Go Programming Language
  - https://go.dev/blog/example
