---
id: go-cobra-cli
title: Cobra CLI Best Practices
description: Patterns for structuring Go CLI apps with spf13/cobra — thin RunE closures, extracted handlers, testable commands, dependency injection.
labels: [go, cobra, cli, best-practices, architecture]
sources:
  - https://cobra.dev/docs/explanations/enterprise-guide/
  - https://qua.name/antolius/making-a-testable-cobra-cli-app
  - https://gopheradvent.com/calendar/2022/taming-cobras-making-most-of-cobra-clis/
  - https://buungroup.com/blog/golang-cli-cobra-viper-guide-2026/
---

# Cobra CLI Best Practices

## 1. Thin RunE — delegate to named functions

RunE closures should parse flags/args and delegate. Business logic lives in a named function.

```go
// Good — RunE is 1 line, handler is testable
cmd := &cobra.Command{
    Use:  "search <query>",
    Args: cobra.ExactArgs(1),
    RunE: func(_ *cobra.Command, args []string) error {
        return runSearch(args[0], scope, kind, format)
    },
}

func runSearch(query, scope, kind, format string) error {
    svc := MustService()
    results, err := svc.Search(ctx, query, buildFilters(scope, kind))
    // ...
}

// Bad — all logic inlined in closure
RunE: func(_ *cobra.Command, args []string) error {
    svc := MustService()
    var labels []string
    if kind != "" {
        labels = append(labels, "kind:" + kind)  // arrow nesting starts
    }
    // 40 more lines inside closure...
}
```

## 2. Constructor functions for subcommands

Each subcommand gets a `NewCommand()` or `XxxCmd()` function. Dependencies passed as parameters, closed over in RunE.

```go
func SearchCmd() *cobra.Command {
    var scope, format string
    cmd := &cobra.Command{
        RunE: func(_, args) error { return runSearch(args[0], scope, format) },
    }
    cmd.Flags().StringVar(&scope, "scope", "", "filter by scope")
    return cmd
}
```

## 3. Never use init() for command registration

Wire commands explicitly in main or a root constructor. `init()` makes the command tree invisible from `main`.

```go
// Good — visible wiring
root.AddCommand(SearchCmd(), SectionCmd(), VocabCmd())

// Bad — magic registration
func init() { rootCmd.AddCommand(searchCmd) }
```

## 4. Use RunE over Run

Always return errors. Let cobra handle exit codes.

```go
cmd.SilenceUsage = true   // don't print usage on runtime errors
cmd.SilenceErrors = true  // suppress cobra's auto error print
```

## 5. Inject io.Writer for testability

Never write directly to `os.Stdout`. Accept a writer.

```go
func runSearch(w io.Writer, query string) error {
    fmt.Fprintf(w, "found %d results\n", len(results))
    return nil
}

// Test:
var buf bytes.Buffer
err := runSearch(&buf, "test")
assert(buf.String(), "found 3 results\n")
```

## 6. Separate CLI layer from business logic

The app struct should not know about cobra. CLI is a thin adapter.

```go
// Business logic — no cobra dependency
type App struct { Store Store }
func (a *App) Search(query string) ([]Result, error) { ... }

// CLI — thin cobra adapter
func SearchCmd(app *App) *cobra.Command {
    return &cobra.Command{
        RunE: func(_, args) error {
            results, err := app.Search(args[0])
            // format and print
        },
    }
}
```

## 7. Multi-subcommand functions are OK at depth 4

Cobra's `Command{RunE: func() { ... }}` struct literal creates structural depth. A function registering 3-4 subcommands will be 60-80 lines at depth 4. This is framework depth, not algorithmic complexity — don't extract unless handlers need independent testing.

## 8. Lazy init via PreRunE

Expensive resources (DB connections, API clients) go in PreRunE, not command construction.

```go
cmd.PersistentPreRunE = func(_ *cobra.Command, _ []string) error {
    db, err = openDB(dbPath)
    return err
}
```
