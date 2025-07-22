# Saint‑Claude

\*A role‑manager CLI/TUI wrapper for \****claude‑code***

---

## Why Saint‑Claude?

Working with large language models often devolves into juggling dozens of barely‑remembered prompts. **Saint‑Claude** turns these prompts into \*\*named \*\****roles*** with versioned ***presets*** and makes switching between them a one‑liner.

Key ideas:

* **Two files, two responsibilities**
  *`roles.json`* / *`roles.local.json`* – *what roles exist & what they can tweak*
  *`roles.settings.json`* / *`roles.settings.local.json`* – *which concrete values you picked yesterday*
* **Inheritance & composition** – declare an abstract `dev` once, extend it into `cpp_dev`, `rust_dev`, …
* **TUI with muscle‑memory‑friendly shortcuts** – arrow keys, `/` to search, `?` for context help.
* **Plain JSON on disk** – edit by hand, track in Git, generate with scripts.

---

## Quick start

```bash
# 1) Install (Rust >=1.78 is required)
cargo install saint-claude

# 2) First run – initialise global configs
st-claude init
#  – or simply run `st-claude` in an empty repo and the wizard appears automatically.

# 3) Pick a role and talk to Claude
st-claude --role cpp_dev -- "Write a CMakeLists.txt for…"
```

The \`\` wizard writes minimal *`~/.config/saint-claude/roles.json`* and *`roles.settings.json`* files, then asks you to create your first role or import community bundles.

---

## CLI layout

`st-claude <SUBCOMMAND|ROLE> [OPTIONS] [--] [claude-code args…]`

### Global options

| Flag                           | Default                                        | Meaning                                                               |
| ------------------------------ | ---------------------------------------------- | --------------------------------------------------------------------- |
| `--scope <global\|local\|any>` | *any*                                          | Where to look for roles & presets. Replaces the old `-g/-l` booleans. |
| `--lang <code>`                | read from config                               | Preferred reply language for Claude.                                  |
| `--skip-permissions`           | *false*                                        | Passes `--dangerously-skip-permissions` to **claude‑code**.           |
| `--set-default`                | –                                              | After this run, remember chosen role & preset as defaults.            |
| `--dry-run`                    | –                                              | Print the final prompt instead of launching *claude-code*.            |
| `-v / --verbose` (repeatable)  | info                                           | Increase log level (info ▶ debug ▶ trace).                            |
| `-q / --quiet`                 | –                                              | Only errors are shown.                                                |
| `--log-file <path>`            | `~/.local/state/saint‑claude/saint‑claude.log` | Custom log destination.                                               |
| `--config <dir>`               | platform default                               | Read/write configs from a different folder.                           |

### Sub‑commands

| Command                                        | Purpose                                                                                                                              |
| ---------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| \`\`                                           | Create *roles.json* & *roles.settings.json* if they don’t exist. Automatically invoked when both global & local configs are missing. |
| \`\` `[--json]`                                | Show available roles (honouring `--scope`).                                                                                          |
| \`\` `<NAME>` `[--abstract] [--based A,B]`     | Launch TUI to create a new role.                                                                                                     |
| \`\` `<NAME>`                                  | TUI editor for an existing role.                                                                                                     |
| \`\` `<NAME>`                                  | Delete role (with confirmation).                                                                                                     |
| \`\` `<NAME>`                                  | Make role the implicit one for plain `st‑claude`.                                                                                    |
| \`\` `[ROLE]`                                  | List presets of a role or of the current default role.                                                                               |
| `** / **`\*\* / \*\*\`\`                       | Manage presets analogously to roles.                                                                                                 |
| \`\` `[ROLE] [PRESET] [ -- claude‑code args…]` | Explicit verb for running Claude if you don’t like the implicit mode.                                                                |

*(All sub‑commands accept the global options above.)*

---

## TUI navigation cheatsheet

| Key                  | Action                                          |
| -------------------- | ----------------------------------------------- |
| **↑ / ↓**            | Move between items                              |
| **← / →**            | Collapse/expand sections                        |
| **Enter**            | Select / confirm                                |
| **Space**            | Toggle checkbox in multiselect fields           |
| **Esc**              | Go back / cancel edit                           |
| **Esc Esc** (double) | Quit Saint‑Claude from a top‑level screen       |
| **/**                | Fuzzy search within current list                |
| **?**                | Pop‑up context‑aware help with active shortcuts |

All screens share a **status bar** at the bottom that shows validation errors or the `description` of the field under cursor.

---

## Typical workflows

1. **Switch to an existing role quickly**
   `st-claude cpp_dev -- "Refactor this template …"`
2. **Create a one‑off preset**
   `st-claude preset add release_notes --role tech_writer && st-claude --preset release_notes`
   Later remove it with `preset remove`.
3. **Make a project‑specific override**
   Inside repo: `st-claude role add cpp_dev.local --based cpp_dev --scope local`
4. **Update default role after a while**
   `st-claude role set-default research_assistant`

---

## Configuration files

| File                        | Scope      | Purpose                                      |
| --------------------------- | ---------- | -------------------------------------------- |
| *roles.json*                | global     | Canonical role catalogue.                    |
| *roles.local.json*          | repo‑local | Overrides/additions for the current project. |
| *roles.settings.json*       | global     | Last‑used values + named presets.            |
| *roles.settings.local.json* | repo‑local | Project‑specific presets.                    |

Saint‑Claude never merges files silently. If both a global and local role share a name the **local** one wins – no exceptions.

---

## Logging

* **Destination** – `~/.local/state/saint‑claude/saint‑claude.log` (override with `--log-file`).
* **Format** – one JSON object per line (JSON Lines):

```jsonc
{"ts":"2025-07-22T11:42:30.412Z","lvl":"ERROR","scope":"preset.edit","msg":"Failed to parse value for 'C++ standard'","input":"C+17"}
```

Fields: `ts` (ISO‑8601 UTC), `lvl` (`TRACE`…`ERROR`), `scope` (module/function), `msg`, arbitrary extra context keys.

---

## License

MIT – see LICENSE for details.
