# 🧙 Boost Week 20

* You need to name the file `_test.go`
* You need to have the name `TestXxx`
* You need to import `testing`
* `TestPrint(t *testing.T)`


## Covered

* Basic idea of what functional programming paradigm
* The only thing that can see internal inside of the module. But we
  don't want it to be able to reach out. We move it to internal
* `Output(args ...string)` this `...` means one or more args.
* No dot's it means it's going to pass the slice.
* This is interesting because if the function takes in `os.Args...` it
  can ingest in the function as `args ...string` or it could be
  `os.Args` and `args []string`. In theory as long as they types line up
* Use of `args []string` vs `args ...string` and `foo(os.Args...)`
* Difference between "arrays" and "slices"
* Use of `flags` for options and complex argument
* What about `cobra`?
* What about `bonzai`?
* Mandatory rand against "getopt"
Factoring out commonly used code into own library modules
* Using `go work` to avoid package problems





