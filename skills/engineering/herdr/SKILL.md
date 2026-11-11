---
name: herdr
description: Customize and drive the Herdr desktop app (herdr-app window and herdr-core `hc`) - layouts, keys.json shortcuts, settings, themes, mods, programs.json, and machines (ssh hosts, WSL distros). Use only when the user invokes /herdr.
disable-model-invocation: true
---

# Herdr

Herdr is a desktop app for terminals and coding agents. Two programs:

- **herdr-app**: the window (spaces, tabs, layouts, panels). Its whole interface is files in `~/.config/herdr-app`, where `~` is the user's home directory on any platform (`$HOME` on Linux and macOS, `%USERPROFILE%` on Windows, for example `C:\Users\<user>\.config\herdr-app`). The window redraws within a second when a file changes.
- **herdr-core (`hc`)**: runs every terminal and agent, on this machine and on each added machine. Terminals keep running when the window closes or updates.

## Step 1: read the live manual first

The authoritative reference is `AGENTS.md` in the user's `.config/herdr-app` folder: `~/.config/herdr-app/AGENTS.md` on Linux and macOS, `%USERPROFILE%\.config\herdr-app\AGENTS.md` on Windows (`$HOME\.config\herdr-app\AGENTS.md` in PowerShell). Resolve the home directory at run time and don't hardcode a user name. Herdr rewrites it on every start to match the installed version, so **read it at the start of every run** instead of relying on this skill's summary. Read only the sections the task needs:

| Task | AGENTS.md section | Also read |
|---|---|---|
| Shortcuts | "Keys: keys.json" | `herdr-app commands` for command names and args |
| Look, theme, fonts, zoom | "The look", "The person: settings.json" | |
| Window structure | "Layouts", "Pane frames", "Floating blocks" | |
| Panels / mods | "Mods", "UI", "The herdr module" | `gpui-kit.d.ts`, `herdr.d.ts` next to AGENTS.md |
| Program names and agent launchers | "Programs: programs.json" | |
| Spaces, tabs, panes, terminals | "The model", "Commands" | `herdr-app --skill` |
| Machines, running agents | "herdr-core (hc)" | `hc --skill`, `hc machine --help` |
| Installing shared layouts/mods | "Layouts and mods other people made" | |

## Rules

- **Never edit shipped layouts or mods.** Herdr replaces them on every start (an edited copy is moved aside to `<name>-edited`). Change look through `settings.json`, or clone first: `herdr-app do layout.clone '{"name":"main","as":"my-layout"}'`, `herdr-app do mod.clone '{"name":"files","as":"my-files"}'`.
- **Never edit** `AGENTS.md`, the `.d.ts` files, `frame/`, `layouts/.state/`, or Herdr's bookkeeping files (`view.json`, `shared.json`, `seen.json`, `installed.json`, `window.json`, `windows.json`, `data/`).
- **Back up before editing** a user file (`cp keys.json keys.json.bak`), then edit.
- **Check after every edit.** An invalid file is ignored (the last good version stays) and the reason goes to `<name>.error` (`keys.error`, `layout.error`, `settings.error`, `programs.error`). Run `herdr-app status` and look at `errors`.
- **Mods:** run `herdr-app check ~/.config/herdr-app/mods/<name>` and never call a mod done without a passing check. No `setTimeout`, no stored `cx`, animate natively (`.frames`, `.spin`, `.loop`), use `theme()` colors.
- **Don't launch bare `herdr-app`** (it opens or raises the window) and never stop or restart the user's app or core.
- **Tell the user** about the escape hatch when changing keys or layouts: `ctrl-alt-shift-escape` resets the shown layout and `keys.json`.

## Shortcuts (keys.json)

- Sections: `global`, `layout:<name>`, `terminal`, `mod:<name>`. More specific wins (`terminal` over `layout:*` over `global`).
- Actions: `command`, `command:value`, `{"command": ..., "args": {...}}`, `send:<text>`, `<mod>.<command>`, or `terminal` (give the key back to the program).
- Before rebinding, check whether the key is already used and say what moves where.
- Don't pick keys programs rely on (ctrl+c/d/l/r/w/a/e/u/k/z, ctrl+[) or ctrl+alt (AltGr on Windows) unless the user asks, and then say what it takes from typing.
- Any edit makes the file the user's own: future default shortcuts are no longer merged in. Mention this the first time.

## Machines

- An ssh host can be added from the window (Settings > Machines) or `hc machine add <target>`.
- **WSL distros are not offered in the window.** Add them with `--via`:
  ```powershell
  wsl.exe -l -v                                    # list distros
  hc machine add --via 'wsl.exe -d <Distro> -e sh -c' --name <name> --install
  hc machine ls                                    # expect "state": "connected"
  hc exec --on <name> uname -a                     # confirm it runs inside the distro
  ```
  `--name` is required with `--via`. `--install` copies `hc` into the distro without asking.
- Every machine needs its own herdr-core (not the app). Keep versions in step with the local one: `hc --version`, then `hc machine update <name>` (terminals keep running).
- Remove with `hc machine rm <name>` (its terminals keep running there). Ask before removing.

## Useful commands

```sh
herdr-app ls          # spaces, tabs, terminals, pane IDs
herdr-app status      # layout, focus, overlays, file and mod errors
herdr-app commands    # every app command and its arguments
herdr-app do <command> --arg value
herdr-app shot out.png   # screenshot of the window, for checking a UI change
hc ls --json          # every core terminal
hc read t3 --lines 50 # a terminal's screen text
hc machine ls         # added machines, their state and hc version
```

## Finish

Report what changed (before and after for keys), where the backup is, and that the change applied without errors. If the user keeps setup notes in a wiki, offer to update them.
