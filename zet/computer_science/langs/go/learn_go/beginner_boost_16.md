# Beginner Boost 2022 Go

Go programming from the Linux terminal for absolute coding noobs. For
those that code like "hackers".

* 100% project-based learning
* Absolute minimum required `git` skill
* https://github.com/

## Covered

* `ssh-keygen -t ed25519`
* Creating a `boost_lab` github repo
* `git config --global user.email YOURGITHUBUSEREMAIL`
* `git config --global user.name YOURGITHUBUSERNAME`
* `git clone git@github.com:YOU/boost_lab.git`
* `git add -A .` - add everything recursively in current directory to git
* `git status`
* `git commit`
* `git push` - push to github
* `git pull` - pull changes to repo on Github
* `git commit FILETHEREALREADY -m 'add file'` - ship the `git add`
* `git merget origin/main` - get the conflicts so you can fix them
* `Plug 'fatih/vim-go', { 'do': ':GoInstallBinaries' }`
* Add the Go `.vimrc` configuration settings
* Don't forget to download and install `Golang`

* "If I add/commit this change it will ..." (initial cap, no period)
* `go run file`
* `go build file`
* `go env`
* `go tool dist list`
* `GOOS=darwin go build hello.go` - how you can cross compile a program
* `entr bash -c "clear; go run hello.go" <<< hello.go`
* `watcher hello.go -c 'clear; go run hello.go'`

* arrays cannot be changed. You always have to make a new array
* arrays are immutable and slices are not.


### Hello World Challenge

Create a program name `hello` that prints `Hello World!`.

#### Requirements

* Must be named `hello`
* Must execute from command line using the name only
* Myst print `Hello World!` on a single line

