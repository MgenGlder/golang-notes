## Learning Go

### The Go Workspace
The default command and workspace for install Go third-party tools is `go install` and `$HOME/go`.

The source code for these tools is stored in `$HOME/go/src` and their compiled binaries in `$HOME/go/bin`.


Regardless of whether or not you use the default location, it's still a good idea to define a `GOPATH` and to put the `$GOPATH/bin` directory in your executable path. This makes it easier to run third-party tools.

If you're on a Unix-like system using `bash`, add the following to your `.profile`.

```
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin
```

You'll need to `source $HOME/.profile` to make the changes take effect in your current window.

### The Go Command

*go run*

`go run <name of go file>`

This will run the go file and delete the binary immediately afterwards.

*go build*

`go build <name of go file>`

This will build the go file in an executable binary. You can name the executable with the `-o` flag:

`go build -o <name of output file> <name of go file>`

Formatting includes using the `goimports` command

## Getting 3rd party tools

You can do this be running `go install <path to githubrepo>@version`.

For example: `go install github.com/rakyll/hey@latest`.

You don't have to download binaries, the source code is a popular way to distribute packages.

## Formatting Code
One of the chief design goals for Go was to create a language that allowed you to write code efficiently. This meant having simple syntax and a fast compiler. It also led Go's authors to reconsider code formatting. Most languages allow a great deal of flexibility on how code is laid out. Go does not. This simplifies the compiler and allows the creation of some clever tools for generating code.[^1]

Go development tools comes with `go fmt` which automatically formats code to match the standard format (e.g. fixing up whitespace issues, lining up fields, ensuring proper spacing everywhere).

There's an enhanced version of `go fmt` called `goimports` that also cleans up your import statements.

You can download `goimports` like so:

`go install golang.org/x/tools/cmd/goimports@latest` and then run it with the following:

`goimports -l -w .`

Note:

No semicolons are necessary in Go because the compiler automatically adds then during compilation.

## Linting and Vetting


It's idiomatic to use `var x byte = 20` instead of `x := byte(20)`.

[^1]: Learning Go by Jon Bodner
