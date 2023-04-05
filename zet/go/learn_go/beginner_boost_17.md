# Beginner Boost Week 17

* Half way through the boost. Time to start looking around
* Challenge yourself in different languages


* Overview of learning landscape Go

* `println("Hello World")` prints to os.Stderr 1>
* `fmt.println("Hello World!")` prints to os.Stdout 1>
* `log.Pringln("Hello World!")` prints to os.Stderr 2>

* Only use go after version 1.18
* According to "import this", Go is better than Python (implicit)
* How to organize you (small-ish) Go projects
* What is the difference between a Go module and package?
  - module: single git repo
  - package: can have multiple modules in subdirectories
* How to create a go module
* Don't forget to set `GOBIN="$HOME/.local/bin`,
  `GOPRIVATE=github.com/$GITUSER/*,gitlab.com/$GITUSER/*`(stops caching servers),
  `CGO_ENABLED=0`
* Go Example* test cases
* Initial cap means "export me"
* Use `log` in unit tests when data varies `log.Println('words')`
* Use default export every thing with `internal` with needed

## Covered

* `go mod init FULLURL`
* `go run .` - run a module

