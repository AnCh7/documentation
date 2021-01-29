> References:
> https://github.com/spf13/cobra


Cobra provides:

- Easy subcommand-based CLIs: `app server`, `app fetch`, etc.
- Fully POSIX-compliant flags (including short & long versions)
- Nested subcommands
- Global, local and cascading flags
- Easy generation of applications & commands with `cobra init appname` & `cobra add cmdname`
- Intelligent suggestions (`app srver`... did you mean `app server`?)
- Automatic help generation for commands and flags
- Automatic help flag recognition of `-h`, `--help`, etc.
- Automatically generated shell autocomplete for your application (bash, zsh, fish, powershell)
- Automatically generated man pages for your application
- Command aliases so you can change things without breaking them
- The flexibility to define your own help, usage, etc.


**Commands** represent actions, **Args** are things and **Flags** are modifiers for those actions.

The pattern to follow is:

`APPNAME VERB NOUN --ADJECTIVE` or

`APPNAME COMMAND ARG --FLAG`

for example:

```bash
hugo server --port=1313
```


##### Commands

Each interaction that the application supports will be contained in a Command. A command can have children commands and optionally run an action.

##### Flags

A flag is a way to modify the behavior of a command. Cobra supports fully POSIX-compliant flags as well as the Go flag package. A Cobra command can define flags that persist through to children commands and flags that are only available to that command.

Global flags:

```go
rootCmd.PersistentFlags().BoolVarP(&Verbose, "verbose", "v", false, "verbose output")
```

Local flags:

```go
localCmd.Flags().StringVarP(&Source, "source", "s", "", "Source directory to read from")
```

Required flags:

```go
rootCmd.Flags().StringVarP(&Region, "region", "r", "", "AWS region (required)")
rootCmd.MarkFlagRequired("region")
```

##### Structure

```
 ▾ appName/
    ▾ cmd/
        add.go
        your.go
        commands.go
        here.go
      main.go
```

##### Cobra Generator: 

```bash
cobra init # create initial application code
cobra add # create additional commands
```

##### Returning and handling errors

If you wish to return an error to the caller of a command, `RunE` can be used.

##### Positional and Custom Arguments

```

    NoArgs - the command will report an error if there are any positional args.
    ArbitraryArgs - the command will accept any args.
    OnlyValidArgs - the command will report an error if there are any positional args that are not in the ValidArgs field of Command.
    MinimumNArgs(int) - the command will report an error if there are not at least N positional args.
    MaximumNArgs(int) - the command will report an error if there are more than N positional args.
    ExactArgs(int) - the command will report an error if there are not exactly N positional args.
    ExactValidArgs(int) - the command will report an error if there are not exactly N positional args OR if there are any positional args that are not in the ValidArgs field of Command
    RangeArgs(min, max) - the command will report an error if the number of args is not between the minimum and maximum number of expected args.

```

##### Help Command

Cobra automatically adds a help command to your application when you have subcommands. This will be called when a user runs 'app help'.

##### Usage Message

When the user provides an invalid flag or invalid command, Cobra responds by showing the user the 'usage'.

##### PreRun and PostRun Hooks

It is possible to run functions before or after the main `Run` function of your command. The `PersistentPreRun` and `PreRun` functions will be executed before `Run`. `PersistentPostRun` and `PostRun` will be executed after `Run`.  The `Persistent*Run` functions will be inherited by children if they do not declare their own.  These functions are run in the following order:

- `PersistentPreRun`
- `PreRun`
- `Run`
- `PostRun`
- `PersistentPostRun`