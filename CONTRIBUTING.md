# Contributing to pin

Pull requests are not accepted. This file explains how to help with pin without one.

## Why pull requests are closed

pin is a supply-chain tool, so every line that ships has to come from a known author. All code in this project is written by the maintainers. Unsolicited pull requests will be closed without review, and code sent through any other channel will not be merged.

## How to help

- Report bugs in [GitHub Issues](https://github.com/scoutbin/pin/issues).
- Request features in GitHub Issues.
- Add context to existing issues, such as confirming that you can reproduce a problem on another OS or version.

Search existing issues before opening a new one. If a matching issue exists, add your details there.

## Reporting a bug

A report is actionable when a maintainer can reproduce the problem without asking follow-up questions. Include:

- The output of `pin version`, which prints the version, commit, build time and OS/arch.
- The output of `pin info`, which prints the executable path, install channel and version. Replace the executable path with a placeholder if it reveals private information.
- Steps to reproduce, as a numbered list of the exact commands you ran.
- The lockfile involved, or a minimal copy that still shows the problem.
- Expected behaviour and actual behaviour, stated separately.
- Full output, including error messages and logs.
- Whether the problem happens every time or only sometimes.

Remove secrets, tokens, private package names, and private registry URLs before posting. Replace them with placeholders so the report still reproduces.

One problem per issue. If you find two, open two issues.

## Requesting a feature

Describe the problem you are trying to solve before the behaviour you want. A concrete use case from your own work is more useful than a proposed implementation. Mention any workarounds you already use.

## Reporting security vulnerabilities

Do not report security vulnerabilities in public issues. Follow the process in [SECURITY.md](SECURITY.md) instead. Public reports put users at risk before a fix exists.

## Local setup for reproducing bugs

Building from source requires Go 1.24.2 or later, as declared in `go.mod`. pin is built on [spine](https://github.com/scoutbin/spine), which is pulled in as a Go module dependency.

Build from the same commit or tag you are reporting against, so your results match the code the bug was reported on.

The CI pipeline is defined in `.github/workflows/ci.yml`. Release steps are in `.github/workflows/release.yml`. Both use reusable workflows from the spine repository.

## License

pin is released under the Apache License 2.0.
