 **Arguments, Options, Flags -- What’s the difference?**

 traditional definitions:

- *Arguments* are all strings that follow a CLI command.
- *Options* are arguments with dashes (single or double) that are followed by user input and modify the operation of the command.
- *Flags* are boolean options that do not take user input.

**Flag Package**

Go’s [flag package](https://golang.org/pkg/flag/) offers a standard library for parsing command line input.

Flags can be defined using `flag.String()`, `Bool()`, `Int()`, etc.

```
examplePtr := flag.String("example", "defaultValue", " Help text.")
```

`examplePtr` will reference the value for either `-example` or `--example`. The initial value at `*examplePtr` is `“defaultValue”`. Calling `flag.Parse()` will parse the command line input and write the value following `-example` or `--example` to `*examplePtr`.

Flag syntax:
`-example -example=text -example text // Non-boolean flags only`
`-` and `--` may be used for flags

Let’s add some flags.

```go
package main

import (
    "flag"
    "fmt"
)

func main() {
    textPtr := flag.String("text", "", "Text to parse.")
    metricPtr := flag.String("metric", "chars", "Metric {chars|words|lines};.")
    uniquePtr := flag.Bool("unique", false, "Measure unique values of a metric.")
    flag.Parse()

    fmt.Printf("textPtr: %s, metricPtr: %s, uniquePtr: %t\n", *textPtr, *metricPtr, *uniquePtr)
}
```

*Boolean flags* :

 true if the flag has been set

 default value can used to determine if the flag  was set

A boolean flag may be set with user input using an "=", optionally

*Arguments:* After parsing, the arguments following the flags are available as the slice `flag.Args()` or individually as `flag.Arg(i)`. The arguments are indexed from 0 through `flag.NArg()-1`.

**Error Handling**

all required flags are not provided or flags provided is unknown, a usage message will be automatically generated using the help text from each flag.

`flag.PrintDefaults()` can be used to print the usage message explicitly, in cases where you want to do any error handling yourself.

**Determine if a Flag Was Set**

To check if a flag was set, you can compare the value at `*flagPtr` to the flag's default value after parsing



**Sub Commands**
Subcommands can be useful when your app needs to perform multiple functions, and each function has a unique set of flags and arguments. A common example of a CLI tool with subcommands is git:

```
git **commit** -m "message" git **push**
```

Subcommands are implemented by creating a `flag.FlagSet`. The `FlagSet` has a subcommand name, error handling behavior, and a set of flags associated with it. Add flags to your `FlagSet` by creating them the same way you would on your main command. The flag package contains constants for different error handling behaviors, which can be found in the package docs.

Let’s add two new subcommands, `list` and `count`. For the count subcommand, let's add a new metric choice called `substring`, and a new `--substring` flag. We'll implement choice flag functionality for the `--metric` flag as well. Please read the inline comments for an explanation of all implementation details.

```go
package main

import (
    "flag"
    "fmt"
    "os"
)

func main() {
    // Subcommands
    countCommand := flag.NewFlagSet("count", flag.ExitOnError)
    listCommand := flag.NewFlagSet("list", flag.ExitOnError)

    // Count subcommand flag pointers
    // Adding a new choice for --metric of 'substring' and a new --substring flag
    countTextPtr := countCommand.String("text", "", "Text to parse. (Required)")
    countMetricPtr := countCommand.String("metric", "chars", "Metric {chars|words|lines|substring}. (Required)")
    countSubstringPtr := countCommand.String("substring", "", "The substring to be counted. Required for --metric=substring")
    countUniquePtr := countCommand.Bool("unique", false, "Measure unique values of a metric.")

    // List subcommand flag pointers
    listTextPtr := listCommand.String("text", "", "Text to parse. (Required)")
    listMetricPtr := listCommand.String("metric", "chars", "Metric <chars|words|lines>. (Required)")
    listUniquePtr := listCommand.Bool("unique", false, "Measure unique values of a metric.")

    // Verify that a subcommand has been provided
    // os.Arg[0] is the main command
    // os.Arg[1] will be the subcommand
    if len(os.Args) < 2 {
        fmt.Println("list or count subcommand is required")
        os.Exit(1)
    }

    // Switch on the subcommand
    // Parse the flags for appropriate FlagSet
    // FlagSet.Parse() requires a set of arguments to parse as input
    // os.Args[2:] will be all arguments starting after the subcommand at os.Args[1]
    switch os.Args[1] {
    case "list":
        listCommand.Parse(os.Args[2:])
    case "count":
        countCommand.Parse(os.Args[2:])
    default:
        flag.PrintDefaults()
        os.Exit(1)
    }

    // Check which subcommand was Parsed using the FlagSet.Parsed() function. Handle each case accordingly.
    // FlagSet.Parse() will evaluate to false if no flags were parsed (i.e. the user did not provide any flags)
    if listCommand.Parsed() {
        // Required Flags
        if *listTextPtr == "" {
            listCommand.PrintDefaults()
            os.Exit(1)
        }
        //Choice flag
        metricChoices := map[string]bool{"chars": true, "words": true, "lines": true}
        if _, validChoice := metricChoices[*listMetricPtr]; !validChoice {
            listCommand.PrintDefaults()
            os.Exit(1)
        }
        // Print
        fmt.Printf("textPtr: %s, metricPtr: %s, uniquePtr: %t\n", *listTextPtr, *listMetricPtr, *listUniquePtr)
    }

    if countCommand.Parsed() {
        // Required Flags
        if *countTextPtr == "" {
            countCommand.PrintDefaults()
            os.Exit(1)
        }
        // If the metric flag is substring, the substring flag is required
        if *countMetricPtr == "substring" && *countSubstringPtr == "" {
            countCommand.PrintDefaults()
            os.Exit(1)
        }
        //If the metric flag is not substring, the substring flag must not be used
        if *countMetricPtr != "substring" && *countSubstringPtr != "" {
            fmt.Println("--substring may only be used with --metric=substring.")
            countCommand.PrintDefaults()
            os.Exit(1)
        }
        //Choice flag
        metricChoices := map[string]bool{"chars": true, "words": true, "lines": true, "substring": true}
        if _, validChoice := metricChoices[*listMetricPtr]; !validChoice {
            countCommand.PrintDefaults()
            os.Exit(1)
        }
        //Print
        fmt.Printf("textPtr: %s, metricPtr: %s, substringPtr: %v, uniquePtr: %t\n", *countTextPtr, *countMetricPtr, *countSubstringPtr, *countUniquePtr)
    }

}
```

**Defining Your Own Flag Types (and List Items)**
Go allows you to define your own flag types! All you need to do is create a type which implements the `flag.Value` interface.

```
type Value interface {
    String() string
    Set(string) error
}
```

`String()` should convert your type to a string. `Set()` will be called during `flag.Parse` to set the value of your flag from it’s string input. In this example, we’ll create a flag type to represent a comma separated list of strings, and add a new flag to the `count` subcommand, `--substringList`. A custom flag type isn't required here, but it is a good example.  Read inline comments for an explanation.

