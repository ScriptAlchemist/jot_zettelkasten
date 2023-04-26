# Creating a LinkedIn Commenting Bot Walk Through

Creating a program in Go that reacts and comments on every new post from
your connections on LinkedIn requires the use of the LinkedIn API.
Before starting, make sure you have Go installed on your machine and you
have a LinkedIn Developer Account.

1. Create the `linkedin-bot` directory
2. Initialize the Go module: `go mod init
   github.com/your-username/linkedin-bot`
3. Install the required packages for parsing JSON and handing oauth2:
  - `go get golang.com/x/oauth2`
  - `go get -u github.com/tidwall/gjson`
4. We want to create out command we plan to use.
  - `mkdir cmd/lnkcmtr`
  - `vim cmd/lnkcmtr`

Inside of `lnkcmtr/main.go`:

```go
package main

import linkedincommenter "github.com/ScriptAlchemist/linkedin-commenter"

func main() { linkedincommenter.Run() }
```

Now we will make a `linkedin_commenter.go`

```go
package linkedincommenter

import "fmt"

func Run() {
  fmt.Println("Starting LinkedIn Comment AI...")
}
```

This is going to be the command line argument that we install and use.

For example: `go install cmd/lnkcmtr` it can then be used like `lnkcmtr`
as long as there are no other stdin needed.

We can also run it with `go run cmd/lnkcmtr/`


