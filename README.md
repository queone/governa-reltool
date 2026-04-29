# governa-reltool

Git tag, commit, and push orchestration for release flows. Validates a semver tag, inspects the working tree, surfaces every git step it intends to execute, prompts for confirmation, then runs `git add → commit → tag → push tag → push branch` on approval.

## Why

Releases in the governa family follow a two-step pattern: a prep tool stages version bumps and CHANGELOG insertions, then a release tool drives the actual git mechanics. This library is the second half — the part that touches `git`, prompts the operator, and pushes the tag. Before extraction, every governa-family repo carried a copy of this orchestration logic and silently drifted; the library exists to be the single source of truth, semver-versioned, picked up via `go get -u`.

The package is leaf-clean of governa governance — it knows nothing about AC files, CHANGELOG row shapes, or governance docs. It takes a `Config{Tag, Message}` and an `io.Reader`/`io.Writer` pair, and orchestrates git. Usable in any Go release flow that wants the same confirm-then-tag-then-push shape.

## Install

    go get github.com/queone/governa-reltool

## Usage

```go
import (
    "os"
    "github.com/queone/governa-reltool"
)

func main() {
    cfg, helpRequested, err := reltool.ParseArgs(os.Args[1:])
    if err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
    if helpRequested {
        fmt.Println(reltool.Usage())
        return
    }
    if err := reltool.Run(cfg, os.Stdin, os.Stdout, os.Stderr); err != nil {
        fmt.Fprintln(os.Stderr, err)
        os.Exit(1)
    }
}
```

## Versioning

This library follows the policy in [governa/docs/library-policy.md](https://github.com/queone/governa/blob/main/docs/library-policy.md). See `CHANGELOG.md` for version history and deprecations.
