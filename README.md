# pin

pin records what a command and its process tree do on the system: outbound and inbound network connections, file access and started programs. The record is a plain-text `pin.lock` that you commit. Later runs are held to it, so a change in behaviour shows up in a diff and can be reviewed, the same way a dependency lockfile shows version changes.

Status: early development. The binary currently has only the built-in commands from spine. Recording and enforcing behaviour are planned, not implemented. See [SPEC.md](SPEC.md) for the draft specification.

## Install

Pick one channel.

- install.sh (Linux and macOS, needs curl, or a wget that supports `--https-only`): `curl -fsSL https://raw.githubusercontent.com/scoutbin/pin/main/install.sh | sh`
- install.ps1 (Windows, PowerShell 5.1 or pwsh): run [install.ps1](install.ps1) from this repository.
- Homebrew: `brew install scoutbin/tap/pin`
- npm: `npm install -g @scoutbin/pin`
- GitHub Releases: archives `pin_<version>_<os>_<arch>.tar.gz` (`.zip` on Windows) for darwin, linux and windows on amd64 and arm64, with `checksums.txt`.

The installers verify the archive's SHA-256 against `checksums.txt` before they install anything.

Installer variables:

| Variable | Purpose |
| --- | --- |
| `PIN_VERSION` | Install this tag, such as `v0.0.1`. Takes precedence over `PIN_TRACK`. |
| `PIN_TRACK` | `stable` (default), `alpha`, `beta` or `rc`. |
| `PIN_INSTALL_DIR` | Install directory. Default `~/.local/bin` on install.sh, `%LOCALAPPDATA%\pin\bin` on install.ps1. |
| `PIN_NO_MODIFY_PATH` | Set to `1` to keep the installer from changing PATH. |
| `PIN_GITHUB_TOKEN` | Token for the release lookup. Not sent with downloads. |
| `PIN_DOWNLOAD_BASE_URL` | install.sh only. Mirror for release assets. Requires `PIN_VERSION`, and must start with `https://` or `file://`. |

## GitHub Action

The action installs the pin binary on the runner and adds it to PATH. It does not run commands under pin yet.

```yaml
- uses: scoutbin/pin@<ref>
  with:
    version: v0.0.1
```

Inputs:

- `version`: release tag. Overrides `track`.
- `track`: default `stable`.
- `install-dir`: default `$RUNNER_TEMP/pin/bin`.
- `token`: default `github.token`. Used for the release lookup only.

Outputs: `version` and `path`.

## Commands

Available today (built into the spine base):

- `pin version`: version, commit, build time, OS and architecture.
- `pin info`: executable path, install channel and version.
- `pin check`: compares the build with the newest release on its track. Exits 1 when a newer release exists. Reads `GITHUB_TOKEN`.
- `pin upgrade`: runs on Linux and macOS. For install.sh installs, replaces the binary in place. Accepts `--version <tag>`. For npm and Homebrew installs, it prints the command to run. On Windows, run install.ps1 again instead.

Global flags: `--json`, `--no-color` (also set by `NO_COLOR`), `--version`, `-h` and `--help`.

Exit codes: `0` success, `1` update available (from `check`), `2` any other error.

Planned, per SPEC.md. None of these work yet:

| Command | Intended behaviour |
| --- | --- |
| `pin record <command>` | Run the command, watch it, and write or update `pin.lock`. The only command that writes the lock. |
| `pin run <command>` | Run and watch the command. Mode is `--audit` (alias `--monitor`) or `--enforce`. Defaults to audit. |
| `pin audit <command>` | Shorthand for `pin run --audit <command>`. |
| `pin enforce <command>` | Shorthand for `pin run --enforce <command>`. |

Planned modes:

- Audit: log findings that are not in `pin.lock`. Never block.
- Enforce: block actions that are not in `pin.lock`, and exit non-zero if anything was blocked.

The spec also lists what pin will not guarantee, including that it is not a full security boundary and does not check file contents.

## Development

Requires Go 1.24.2 or later.

- `go build .` builds the binary.
- `go run . version` runs it.
- `go test ./...` runs the tests. There are none yet.
- `go tool spine` runs spine's development CLI. `go tool spine gen` regenerates `install.sh`, `install.ps1` and `action.yml`. These files are generated from `spine.json`, so do not edit them by hand.

`main.go` embeds `spine.json` and passes it to spine. `internal/app/app.go` is a placeholder for pin's own commands.

Releases run from the `release.yml` workflow (`workflow_dispatch`). The `tag` input must match a section in `CHANGELOG.md`. `publish` defaults to `false`, which runs a dry run. See the spine README for how the release workflows work.

## License

Apache-2.0. See [LICENSE](LICENSE).
