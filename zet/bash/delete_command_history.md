# How to Delete the History of the Last n Commands?

## Overview

When we work with the command-line under Linux, recent commands will be
kept inside history. In this tutorial, we'll address different ways of
deleting a sequence of lines from history

## Difference Between history and Commands in .bash_history

Linux bash _history is usually stored in a file named ~/.bash_history at
the end of each session_

By default, all the commands issued during the session will be saved to
this file for further reuse. In each session, when we exit the bash, all
the in-memory commands will be written to ~/.bash_history file. So, our
current executed commands won't be saved instantly. Therefore, the
current contents of the history command and the bash_history file are
usually different.

Let's check the last five lines of the ~/.bash_history file:

```bash
$ tail -5 ~/.bash_history
tail
tail
cd
ls
ls
```

With the -5 option with tail, we can have the five recent commands in
the bash_history. Note that these lines belong to the previous session
activity. Let's also check the last five lines in history for
comparison:

```bash
history 5
1987 cd baeldung
1988 ls -l
1989 cat pro.sh
1990 ls -la
1991 echo "${BASH_VERSION}"
```

These lines belong to our session activity and not written to
bash_history yet.

## Delete Last `n` Lines from .bash_history

If we want to delete the last `n` lines for previous sessions or if we
ended our session recently, we should delete lines from
`~/.bash_history`. Let's remove the last five lines from this file:

* `for h in {1..5}; do sed -i '$d' ~/.bash_history; done;`

The `-i` option in `sed` allows for in-place editing. $ matches with the
last lines of the file, and `d` is for deleting the selected line.

Note that we can also open `~/.bash_history` file with any text editor
and delete the lines manually.

## Delete Last n Lines in history

If we notice the history records, we can see that each record has a line
number. We can use this number to delete a sequence of lines:

* `for h in {1987..1991}; do history -d 1987; done`

The `history -d` option will delete the line with a given line number. In
this scenario, we only need to specify the start number in `history -d`,
because after deleting the line 1987, line 1988 will be the line 1987,
and so on.

If we want to remove the evidence, we can change the previous command and
add a command to delete the last line of history, which is our recent
deletion command:

* `for h in {1987..1991}; do history -d 1987; done; history -d $(history
  1 | awk '{print $1}')`

We get the last line in history with `history 1`. Then we get the line
number with `$1`.

## Create a Function


We can add the previous line to a new function in `~/.bashrc` for further calls.

Let's edit the `~/.bashrc` file:

* `vim ~/.bashrc`

Let's create a new function at the end of `~/.bashrc`:

```bash
historyDel() {
  for h in {$1..$2}; do
    history -d $1
  done
  history -d $(history 1 | awk '{print $1}')
}
```

To save you will:

* `esc`: get into command mode
* `:`: start the command
* `w`: save the file
* `q`: quit vim
* `:wq` is all at once before clicking `enter`

To leave the file you will:

* `esc`: get into command mode
* `:`: start the command
* `q`: quit vim
* `!`: force quit (don't save)
* `:q!` is all at once before clicking `enter`

Note the making any changes to `~/.bashrc` wil only work in a new terminal session. To apply changes to the current terminal, we should _reload the .bashrc content_:

* `source ~/.bashrc`

Let's now call our function in the terminal:

* `historyDel 2001 2004`

Note that the `historyDel` function takes 2 parameters as a range. If we want to delete the last n line automatically, we can make another function to get `n` as input:

```bash
historyDeln() {
  n=$(history 1 | awk '{print $1}') # current history number
  historyDel $(( $n-$1 )) $(( $n-1 )) # Call historyDel with ranges
}
```

Let's remove the last 5 lines of history with our new function:

* `historyDeln 5`

## Changes in History Since Bash 5

Due to changes in bash 5, there is now a range option available for deleting lines in history. Let's delete lines of 2001 to 2004 from history.

* `history -d 2001-2004`

Note that the first number is the starting point, and the second one is the endpoint for deletion. The positions are inclusive. Also, we can use backward deletion. Let's delete the last 5 lines of history:

* `history -d -5--1`

A negative index counts back from the end of the history. Therefore, and index of `-1` refers to the current `history -d` command.

## Conclusion

In this article, we have addressed different ways of removing the last `n` lines from the Bash commands history.

First, we saw how to delete lines from the `.bash_history` file. Then, we removed a sequence of commands from history. Finally, we created a function in `.bashrc` to automate the deletion process.


