# Beginner Boost 22

* Watch the Monty Python Bridgekeeper (and Swallow) skits
* Importance of strict typing in modern languages
* Conditional statements that evaluate as booleans
false: (`if name == "Galahad || "Lancelot"`) should be: (`if name == "Galahad || name == "Lancelot"`)
* `Go work init`
* `Go work add .`
* `go work add ../termcolors`
* Is the stdout going into the terminal?
* Do NOT add color output unless you are SURE you have terminal output
* Discovered `golang.org/x/term` deprecates `crypto/ssh/` version
* Moving the term check into `termcolors` so it will empty all the
  variables on non-interactive terminals
