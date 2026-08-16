
#  Confy: The Dev's Dotfile Deployment System

`confy` is a blazingly fast, single-binary Rust TUI for managing dotfiles. It skips the bloat of heavy Git integrations or custom configuration languages. Instead, it uses standard symlinks, smart content-hashing for version control, and password-protected `.zip` archives for instant deployment to new machines.


## 🌟 Core Features

- **🌳 Inline Tree View:** Navigate and expand directories directly in the list.
- **⏳ Smart Version Control:** Content-hashed snapshots are taken *only* when a file actually changes. Includes a built-in `diff` viewer.
- **📦 Encrypted Deployments:** Archive selected files into a password-protected `.zip` with a `manifest.json`, and deploy them to a new machine with smart, conflict-aware backups.
- **🔒 Seamless Sudo:** Edit root-owned configs without leaving the TUI. If a file needs root, a password prompt appears right in the bottom bar.
- **📝 Persistent State:** Bookmarks (`★`), per-file notes, and an MRU (Recent Files) list are saved to `~/.confy_state.json`.
- **🎨 Adaptive UI:** Detects Truecolor  and falls back to ANSI 256. Features a dynamic kitty `ฅ^•ﻌ•^ฅ` that reacts to your actions.

## 🛠️ The v4.0.0 Bug Hunt: 9 Critical Fixes

This version underwent a rigorous code audit. Nine logical bugs, race conditions, and edge-case crashes were identified and resolved:

1. **Cross-Device `fs::rename` Panic**: Shelled out to `mv` for deployments/cut-paste to handle different filesystems (e.g., `/tmp` vs `~`).
2. **Directory Symlink Overwrite Crash**: Used `fs::remove_dir_all` for directories in `deploy_archive` instead of crashing on `remove_file`.
3. **State Leakage in Deploy Mode**: Explicitly cleared `deploy_target` and `deploy_pass` in `try_deploy` and `cancel_input`.
4. **Silent Failures on Wrong Passwords**: `ArchivePrompt` now catches `PASSWORD_REQUIRED`, displays "Wrong password", and allows retries.
5. **`VisualSelect` Wiping Manual Selections**: `Esc` now just exits the mode and preserves selections made with `Space`.
6. **Directory Selection Crash**: Restricted `toggle_selection` to files only to match the archiver's capability.
7. **Stale Input on Error**: Input bar now clears on error in `AddingPath` so you can type fresh.
8. **Leftover Temp Files**: `deploy_archive` now cleans up `temp_dir` unconditionally.
9. **Shell Injection / Escaping**: The `!` shell mode now escapes `"` to `\"` in file paths.

##  Dependencies 

To keep the Rust binary tiny and fast, `confy` shells out to standard Unix tools. Ensure you have these installed for the full experience:
- `fzf` (Fuzzy finder for path completion)
- `bat` (Syntax highlighting in previews)
- `chafa` (Image/video ASCII rendering)
- `ffmpeg` / `ffprobe` (Video metadata/thumbnails)
- `zip` / `unzip` (Archive creation/deployment)
- `diff` (Version comparison)

## 📦 Installation

1. Ensure you have the Rust toolchain installed (`rustup`).
2. Clone and build:
    ```bash
    git clone https://github.com/YourUsername/confy.git
    cd confy
    cargo build --release
    ```
3. Move the binary to your path:
    ```bash
    cp target/release/confy ~/.local/bin/confy
    ```

## ⌨️ Quick Keybindings

| Key | Action |
| --- | ------ |
| `j` / `k` / `↑` / `↓` | Navigate |
| `Enter` | Edit File / Expand Folder |
| `Right` / `Left` | Expand / Collapse Folder |
| `a` | Add Symlink (Use `Tab` for `fzf`) |
| `f` | Create Folder |
| `r` | Rename |
| `d` | Delete (Press twice to confirm) |
| `c` / `p` | Cut / Paste |
| `y` / `p` | Yank / Paste |
| `v` | View Version History (Press `d` for diff) |
| `Space` | Select File for Archiving |
| `V` | Visual Select Mode |
| `z` | Archive Selected to `.zip` |
| `D` | Deploy `.zip` (Dry Run, then `y` to apply) |
| `!` | Shell Mode (Run command on file) |
| `b` | Toggle Bookmark |
| `n` | Add/Edit Note |
| `R` | Jump to Recent File |
| `.` | Toggle Hidden Files |
| `?` | Help Menu |
| `q` / `Esc` | Quit / Cancel |

## ⌨️ CLI Usage

```bash
confy                 # Launch TUI
confy -l <path> -n <name> # Create a symlink
confy -o <file/folder> # Open in default system app
confy -e <file/folder> # Open in selected editor
confy -d <archive.zip> [--apply] [--pass <password>] # Deploy archive
confy doctor          # Check dependencies and symlinks
confy discover        # Auto-link famous dotfiles
confy git-export      # Init git repo and commit ~/.confy
confy -s "<password>" # Save sudo password
```
