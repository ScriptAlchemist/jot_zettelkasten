# 🧙 Beginner Boost Week 19

This week is mostly about printing text and color to the terminal in fun
ways including a `termcolor` reusable library.

## Covered

* ANSI escapes as related to ASCII table
* Terminal *emulators*
* Understanding `termcap`, `tput` and `stty` and `$TERM`
* First, how do we do this from bash?
* Overview of how to create your own PS1 prompt
* `tput lines` and `tput cols`
* `stty -noecho` and `stty -echo`
* Standard and high intensity colors
* Make sure to detect non-interactive terminals and disable color
* `const` is immutable. `var` can be changed
* Import package with alias
* `for i in {1..8}; do printf "const Black = \"\\\\033[3${i}m\"\n";
  done` Using bash inside of Vim. running `!!bash`
* You can increment numbers with arrow keys in Vim
* use `%q` when testing escape output
* Infinite looping (`for` for ever looping)
* Control-C (interrupt signal) to stop things
* time.Sleep
* Bash trap command `trap "printf \"$CLEAR$CURON\"; exit; trap -- -
  SIGINT SIGTERM" SIGTERM SIGINT`
* Go trapping interrupts
* Remember `setterm --cursor` in if you get stuck without a cursor

## Colors

* `Black = "\033[30m"`
* `Red = "\033[31m"`
* `Green = "\033[32m"`
* `Yellow = "\033[33m"`
* `Blue = "\033[34m"`
* `Magenta = "\033[35m"`
* `Cyan = "\033[36m"`
* `White = "\033[37m"`
* `Reset = "\033[0m"`
* `Clear = "\033[H\033[2J"`
* `CurOn = "\033[?25h"`
* `CurOff = "\033[?25l"`

## Random Numbers

How are we going to handle random in go?

* `rand.Seed(time.Now().UnixNano())`
* `v := rand.Intn(7)`
* `fmt.Spritf("\033[3%vm", v)`

I want something that is a bit more random and isn't associated with
time.

* `var b [1]byte`
* `rand.Read(b[:])`
* `v := int(b[0]) % 8`

This seems to have more solid change in color. Unlike the seed option
that was moving too fast and producing large chunks of color.

This code generates a single random byte using `rand.Read()` and then
converts it to an integer between 0 and 7 using the modulo operator
`%`. Note that `rand.Read()` returns an error, but we are ignoring it in
this example for simplicity. In real-world application, you should
handle the error appropriately

## Related

* https://www.gnu.org/software/bash/manual/html_node/ANSI_002dC-Quoting.html


