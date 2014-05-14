Flag
===

Flag is a drop in replacement for Go's flag package with the addition to parse files and environment variables. If you support the [twelve-factor app methodology][], `flag` complies with the third factor; "Store config in the environment".

An example using a gopher:

```go
$ cat > gopher.go
    package main

    import (
        "fmt"
    	"github/namsral/flag"
	)
    
    var age int
    
    flag.IntVar(&age, "age", 0, "age of gopher")
    flag.Parse()
    
    fmt.Print("age:", age)

$ go run gopher.go -age 1
age: 1
```

Same code but using an environment variable:

```go
$ export GOPHER_AGE=2
$ go run gopher.go
age: 2
```
    

Same code but using a configuration file:

```go
$ cat > gopher.conf
age 3

$ go run gopher.go -config gopher.conf
age: 3
```

The following table shows how flags are translated to environment variables and configuration files:

| Type | Flag | Environment | File |
| ---- | ---- | ----------- | ---- |
| int | -age 2 | GOPHER_AGE=2 | age 2 |
| bool | -female | GOPHER_FEMALE=true | female true |
| float | -length 175.5 | GOPHER_LENGTH=175.5 | length 175.5 |
| string | -name Gloria | GOPHER_NAME=Gloria | name Gloria |

This package is a port of Go's [flag][] package from the standard library with the addition of two functions `ParseEnv` and `ParseFile`.

[flag]: http://golang.org/src/pkg/flagconfiguration


Why?
---

Why not use one of the many INI, JSON or YAML parsers?

I find it best practice to have simple configuration options to control the behaviour of an applications when it starts up. Use basic types like ints, floats and strings for configuration options and store more complex data structures in the "datastore" layer.


Usage
---

It's intended for projects which require a simple configuration made available through command-line flags, configuration files and shell environments. It's similar to the original `flag` package.

Example:

```go
import "github/namsral/flag"

flag.String("config", "", "help message for config")
flag.Int("age", 24, "help message for age")

flag.Parse()
```

Order of precedence:

1. Command line options
2. Environment variables
3. Configuration file
4. Default values

The order can be changed by parsing manually:

```go
flag.String("config", "", "help message for config")
flag.Int("age", 24, "help message for age")

flag.CommandLine.ParseEnv(path.Base(os.Args[0]))
flag.CommandLine.Parse(os.Args[1:])
flag.CommandLine.ParseFile("/etc/command.conf")
```

#### Parsing Configuration Files

Create a configuration file:

```go
$ cat > /etc/command.conf
# empty newlines and lines beginning with a "#" character are ignored.
name bob

# keys and values can also be separated by the "=" character
age=20

# booleans can me empty, set with 0, 1, true, false, etc
hacker
```

Add a "config" flag:

```go
flag.String("config", "", "help message for config")
```

Run the command:

```go
$ go run ./command.go -config /etc/command.conf
```

#### Parsing Environment Variables

Prefix the environment variable with the name of your command in uppercase:

```go
$ export COMMAND_AGE=44
$ go run ./command.go
```

If you want to customise the prefix, just parse the environment variables manually:

```go
flag.Int("age", 24, "help message for age")
flag.CommandLine.ParseEnv("MYCOMMAND")
```

For more examples see the [examples][] directory in the project repository.

[examples]: https://github.com/namsral/flag/tree/master/examples

That's it.


License
---


Copyright (c) 2012 The Go Authors. All rights reserved.

Redistribution and use in source and binary forms, with or without
modification, are permitted provided that the following conditions are
met:

   * Redistributions of source code must retain the above copyright
notice, this list of conditions and the following disclaimer.
   * Redistributions in binary form must reproduce the above
copyright notice, this list of conditions and the following disclaimer
in the documentation and/or other materials provided with the
distribution.
   * Neither the name of Google Inc. nor the names of its
contributors may be used to endorse or promote products derived from
this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
"AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
(INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
