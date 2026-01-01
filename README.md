# zl (zed-launcher)

`zl` is a tiny launcher script that helps you store, browse, and open local and remote development projects in **Zed** IDE (or other programs).

It works by keeping a folder of **profiles** (plain text files). Each profile’s **first line** is treated as the **CLI argument(s)** passed to `zed`.

It also uses the `tv` app (Television) to provide an interactive picker UI for selecting profiles, and it uses your local `$EDITOR` (falling back to `vim`) to create/edit profiles.

---

## How it works

- Profiles live in: `~/.zl/`
- Each profile is just a text file.
- The **first line** of the profile is read and used as the arguments to Zed.
  - Example first line values:
    - `~/code/my-project`
    - `ssh://user@host/path/to/repo`
- When you run the launcher with **no arguments**, it uses `tv` to select a profile from `~/.zl/`.
- When you run with `+ <name>`, it opens (or creates) a profile file in `$EDITOR`.

---

## Requirements

- Bash (script uses `#!/usr/bin/env bash`)
- Zed installed and available as `zed` on your `PATH`
- `tv`: [Television](https://github.com/alexpasmantier/television) for interactive profile selection
- `bat`: [alternative to `cat`](https://github.com/sharkdp/bat) for viewing profiles in Television
- `fd`: [alternative to `find`](https://github.com/sharkdp/fd) for file search in Television
- `ripgrep`: [alternative to `grep`](https://github.com/BurntSushi/ripgrep) for text search in Television
- Optional: set `$EDITOR` for profile editing (defaults to `vim`)

---

## Installation (recommended)

1. Put `zl` somewhere in your `PATH`:

   Example:
   - Copy it to `~/.local/bin/zl` (or another bin directory you use)
   - Make it executable `chmod +x ~/.local/bin/zl`

2. Install dependencies:
   - macOS: `brew install television bat fd ripgrep`
   - Ubuntu: `sudo apt-get install television bat fd ripgrep`
   
      > On Ubuntu you may need to add repositories for `television`, `bat`, `fd`, and `ripgrep` if they are not available in the default repositories, or download them from their respective websites.

2. Then run it from your terminal as:

- `zl` (interactive selection via `tv`)
- `zl <profile_name>` (open that profile directly)
- `zl + <profile_name>` (create/edit a profile)

---

## Usage

### 1) Pick a profile interactively

Run:

- `zl`

If `tv` is installed, it will show a picker of files in `~/.zl/`. Selecting one launches Zed using the profile’s first line as arguments.

### 2) Open a profile directly

Run:

- `zl my-profile`

This reads `~/.zl/my-profile`, takes the first line, and runs:

- `zed <first-line>`

### 3) Create or edit a profile

Run:

- `zl + my-profile`

This opens `~/.zl/my-profile` in `$EDITOR` (or `vim` if `$EDITOR` is not set).

If the profile doesn’t exist yet, it’s created with an initial `ssh://` line so you can quickly fill in a remote target.

---

## Profile format

A profile is a plain text file. Only the **first line** is used by the launcher.

Everything after the first line is ignored by the script, which is useful for notes.

Example profile: `~/.zl/work-dev`

First line is what matters:

- `ssh://me@dev.example.com/var/www/app`

You can also add notes:

- second line and beyond: anything you want (hostnames, reminders, commands, etc.)

### Tilde expansion

If the first line begins with `~` or `~/...`, it will be expanded to your home directory before running Zed.

---

## Examples

### Local project

Create a profile:

- `zl + my-local`

Put this on the first line:

- `~/code/my-local`

Then run:

- `zl my-local`

### Remote project over SSH

Create/edit:

- `zl + remote-dev`

First line:

- `ssh://user@server.example.com/home/user/project`

Run:

- `zl remote-dev`

### Keep notes per project

Profile file: `~/.zl/client-a`

First line:

- `~/work/client-a`

Notes below:

- `# staging: ssh://me@staging.example.com/...`
- `# jira: https://...`
- `# runbook: ...`

### Use a different launcher target (adapt for other use cases)

By default, the script runs `zed`. You can override it with `LAUNCH_TARGET`.

Open the same profiles with a different command:

- `LAUNCH_TARGET=code zl my-local` (VS Code, if `code` is installed)
- `LAUNCH_TARGET=zed-preview zl my-local` (if you have an alternate Zed binary)
- `LAUNCH_TARGET=echo zl my-local` (debug: prints what would be executed)

Because the launcher is “read first line → exec a command”, you can adapt it for many workflows beyond Zed.

---

## Tips

- Use short, memorable profile names (`work`, `dev`, `client-a`, `notes`, etc.).
- Store one profile per repo / environment.
- Add comments and reminders under the first line—only the first line is used to launch.

---

## Troubleshooting

- **`zl` says `tv` is not installed**  
  Install `tv` (Television) or run `zl <profile>` directly instead of using interactive mode.

- **It can’t read a profile file**  
  Ensure the file exists in `~/.zl/` and is readable:
  - `ls -l ~/.zl/<profile>`

- **Zed doesn’t open the target**  
  Try running the command manually:
  - `zed "<first-line-from-profile>"`

This helps confirm whether the issue is with the profile content or with Zed itself.
