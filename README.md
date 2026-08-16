```
                                                          ﷽
```
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
<img width="100%" height="75" alt="gray0_ctp_on_line" src="https://github.com/user-attachments/assets/d5ca8f7f-aca9-4948-b73e-f3adb2b29076" />

<svg width="600" height="75" viewBox="0 0 600 75" version="1.1" xmlns="http://www.w3.org/2000/svg" style="stroke-linecap: round; stroke-linejoin: round; stroke-miterlimit: 1.5;">
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M105.809,48.397C105.809,44.506 102.473,43.931 102.473,33.503" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M109.397,38.324L109.397,48.321" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M112.883,48.152C112.883,44.717 115.053,40.554 115.053,35.084C115.053,29.613 114.393,24.795 114.216,21.81" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M112.951,22.241C112.951,22.241 116.335,21.976 117.504,16.695" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M107.788,11.843C107.788,11.843 106.369,7.434 105.169,7.434C103.969,7.434 101.87,13.187 101.87,21.862C101.87,24.103 90.181,29.985 92.659,43.571C93.057,45.751 94.053,49.908 94.053,49.924C94.053,49.94 96.571,59.453 91.184,59.453C90.063,59.453 89.526,58.833 88.405,58.833C87.285,58.833 86.381,59.598 86.381,60.591C86.381,61.584 87.491,64.025 91.446,64.025C98.593,64.025 98.865,58.038 98.865,54.158C98.865,50.278 98.829,51.479 98.829,50.844C98.829,48.717 100.601,48.284 101.259,48.043" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <ellipse transform="matrix(1.00474,-0.404483,0.370766,0.920982,85.4108,49.8267)" cx="111.892" cy="15.766" rx="1.032" ry="1.449" style="fill: rgb(47, 44, 62);"/>
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M110.074,10.347C113.617,10.347 114.448,14.635 117.14,14.635" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <path transform="matrix(1,0,0,1,92.3579,4.11772)" d="M112.568,9.074C112.568,9.074 111.553,6.74 110.677,6.74C109.801,6.74 108.537,9.169 108.537,9.169" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 1.5px;"/>
    <path transform="matrix(3.96613,0,0,5.89452,-177.012,-336.835)" d="M93.717,66.428L195.647,66.428" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 0.3px;"/>
    <path transform="matrix(1.78906,0,0,2.78204,-166.7,-130.078)" d="M93.717,66.428L195.647,66.428" style="fill: none; stroke: rgb(110, 108, 126); stroke-width: 0.64px;"/>
</svg>
