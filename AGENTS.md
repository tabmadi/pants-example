# Agent guide

This guide is for any coding agent that works in this repo. **[README.md](README.md) ranks above this file.** It explains what the example shows and why.

- This repo is an example. It teaches one idea. Keep it small, and do not add what the README lists as deliberately missing.
- Every text follows Simple English: common words, short sentences, no emoji, no em dash, semicolon, or parentheses in prose. The full rules are [ADR-0001 in github-project-template](https://github.com/tabmadi/github-project-template/blob/master/docs/adr/0001-documentation-conventions.md).
- Run every command as `mise run <task>`. Before you finish a change, run `mise run check`.
- Commit messages are a Conventional Commits title only, with no body. Never push unless asked to.

## This example

- Pants is the build tool, and the subject of the example. Every build, run, lint, and format goes through `pants`. Do not add a second tool for the same job.
- A new directory of code needs a `BUILD` file. `pants tailor ::` writes one.
- Pants infers dependencies from imports. List a dependency in a `BUILD` file only when inference cannot see it.
- The Pants version is in `pants.toml`. The launcher version is in `.mise.toml`.
