# `ranger`

## Introduction
`ranger` is a terminal-based file manager with Vim-inspired keybindings, designed for efficient navigation and management of files and directories. Written in Python, it leverages `ncurses` for a text-based user interface (TUI) that integrates with command-line tools to provide powerful file browsing, previewing, and manipulation capabilities. As of the latest stable version (1.9.3, released February 2020), `ranger` supports features like image previews, tabs, bookmarks, and plugin extensibility. Despite no recent updates, it remains a robust choice for developers, sysadmins, and power users who prefer a lightweight, customizable file manager in the terminal.

`ranger` excels in scenarios requiring fast file navigation, batch operations, or integration with tools like `fzf`, `rg`, and `git`. Its ability to preview files (text, images, videos) without opening them makes it ideal for exploring large directories or managing projects without leaving the terminal.[](https://github.com/ranger/ranger)[](https://luxagraf.net/src/how-use-ranger-command-line-file-browser)

## Installation
`ranger` is available on Linux, macOS, Windows (via WSL), and BSD systems. Below are common installation methods, updated for modern practices.

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian:
    ```bash
    sudo apt update
    sudo apt install ranger
    ```
  - Fedora:
    ```bash
    sudo dnf install ranger
    ```
  - Arch Linux:
    ```bash
    sudo pacman -S ranger
    ```
  - Nix:
    ```bash
    nix-env -i ranger
    ```
- **From Source** (requires Python 3.1+ and `git`):
  ```bash
  git clone https://github.com/ranger/ranger.git
  cd ranger
  sudo make install
  ```
- **Docker**:
  ```bash
  docker run --rm -it -v $(pwd):/data ghcr.io/ranger/ranger ranger
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install ranger
  ```
- **MacPorts**:
  ```bash
  sudo port install ranger
  ```
- **Nix**:
  ```bash
  nix-env -i ranger
  ```

### Windows
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Scoop**:
  ```bash
  scoop install ranger
  ```
- **Manual**: Install Python 3, then use source install via WSL or Git Bash.

### Dependencies
- **Python**: Required (3.1+; 2.6+ for older versions).
- **ncurses**: For TUI rendering.
- **Optional for Previews**:
  - `w3mimgdisplay`, `ueberzug`, `mpv`, `iterm2`, `kitty`, `terminology`, or `urxvt` for image previews.
  - `highlight`, `bat`, or `pygmentize` for syntax-highlighted text previews.
  - `atool`, `unzip`, `tar` for archive previews.
  - `mediainfo`, `exiftool` for media metadata.
- **Git**: Optional for version-controlled configs.
- **rifle**: Included file opener (`rifle.conf`) for launching files.

### Verify Installation
Run `ranger --version` to confirm installation. Example output:
```
ranger version: 1.9.3
Python version: 3.8.10
```
Ensure dependencies: `python3 --version`, `w3mimgdisplay --version` (if used).[](https://github.com/ranger/ranger)

## Basic Usage
`ranger` launches a TUI for navigating files and directories, using Vim-like keybindings. The basic syntax is:
```bash
ranger [OPTIONS] [PATH]
```

- **PATH**: Starting directory (default: current directory).
- **OPTIONS**: Flags to customize behavior (e.g., `--copy-config`, `--choosedir`).

### Simple Example
Launch `ranger` in the current directory:
```bash
ranger
```
Output: TUI with three columns (parent, current, and preview).

Navigate to `~/Downloads`:
```bash
ranger ~/Downloads
```

Copy default configuration:
```bash
ranger --copy-config=all
```
Output: Creates `~/.config/ranger/{rc.conf,rifle.conf,commands.py,scope.sh}`.

### Basic Keybindings
- **Navigation**: `h` (parent dir), `j` (down), `k` (up), `l` or `Enter` (enter dir).
- **Go To**: `gg` (top), `G` (bottom), `/` (search).
- **File Operations**: `yy` (yank/copy), `dd` (cut), `pp` (paste), `cw` (rename).
- **Preview**: `i` (toggle preview), `zh` (show hidden files).
- **Tabs**: `Ctrl+N` (new tab), `Ctrl+W` (close tab).
- **Quit**: `q`.

Full keybindings: Press `?` in `ranger` or see [Keybindings](https://github.com/ranger/ranger/blob/master/doc/ranger.1).[](https://github.com/ranger/ranger)[](https://luxagraf.net/src/how-use-ranger-command-line-file-browser)

## Key Features
`ranger`’s strengths include:
- **Vim-Inspired Keybindings**: Intuitive for Vim users, highly customizable.
- **Three-Pane Layout**: Displays parent, current, and preview panes.
- **File Previews**: Supports text, images, videos, and archives with external tools.
- **Tabs and Bookmarks**: Multiple tabs and quick navigation with bookmarks.
- **Rifle File Opener**: Configurable file associations via `rifle.conf`.
- **Plugin System**: Extensible via Python scripts in `~/.config/ranger/plugins/`.
- **Lightweight**: Minimal resource usage, Python-based.

Notable in 1.9.3: Improved `scope.sh` for previews, better trash handling with `rifle`, and enhanced plugin support.[](https://github.com/ranger/ranger)[](https://ranger.github.io/ranger.1.html)

## Common Options
Frequently used options (see `ranger --help` or `man ranger`):

| Option | Description | Example |
|--------|-------------|---------|
| `--version` | Show version | `ranger --version` |
| `--copy-config` | Copy default config (rc, rifle, etc.) | `ranger --copy-config=all` |
| `--choosedir` | Write last directory to file | `ranger --choosedir=/tmp/dir` |
| `--choosefile` | Write selected file to file | `ranger --choosefile=/tmp/file` |
| `--clean` | Run without config files | `ranger --clean` |
| `--debug` | Enable debug mode with traceback | `ranger --debug` |
| `--cachedir` | Set cache directory | `ranger --cachedir=/tmp/ranger-cache` |

### Common Keybindings
- **File Selection**: `Space` (toggle select), `v` (select all).
- **Tags**: `t` (toggle tag), `"*` (default tag), `ut` (untag).
- **Sort**: `o` (cycle sort options, e.g., `oc` for creation time).
- **Search**: `/pattern` (search), `n` (next match).
- **Execute**: `:shell <command>` (run shell command), `r` (open with).
- **Trash**: `dD` (trash files, requires `trash-cli` or fallback).[](https://ranger.github.io/ranger.1.html)

## Advanced Usage
### Configuration
Customize via `~/.config/ranger/rc.conf`:
```bash
set show_hidden true
set preview_files true
set line_numbers relative
setlocal path=~/notes sort mtime
```
Copy defaults:
```bash
ranger --copy-config=rc
```

File opener (`~/.config/ranger/rifle.conf`):
```bash
ext py, has python3 = python3 "$1"
mime ^image, has feh = feh "$1"
```

Preview script (`~/.config/ranger/scope.sh`):
```bash
# Enable image previews with ueberzug
case "$extension" in
    jpg|png|jpeg) ueberzug "$path" ;;
esac
```

### Plugins
Add plugins to `~/.config/ranger/plugins/`:
```bash
mkdir -p ~/.config/ranger/plugins
wget https://github.com/ranger/ranger/raw/master/examples/plugin_file_filter.py -O ~/.config/ranger/plugins/file_filter.py
```
Example plugin: Filter specific file types dynamically.

### Bookmarks
Save bookmark: Press `m` then a key (e.g., `a` for bookmark `a`).
Jump to bookmark: Press `'a` or `:bookmark a`.

### Tabs
Open new tab: `Ctrl+N`.
Switch tabs: `Tab` or `Ctrl+Tab`.
Close tab: `Ctrl+W`.

### File Operations
Batch rename:
```bash
# Select files with Space, then:
:bulkrename
```
Edit filenames in `$EDITOR`.

Trash with confirmation:
```bash
set confirm_on_delete always
dD  # Trash selected files
```

### Searching
Regex search:
```bash
:filter ^file.*\.txt$
```

Mark matching files:
```bash
:mark pattern
```

### Shell Commands
Run shell command on selected files:
```bash
:shell mv %s /destination/
```
`%s` represents selected files.

### Previews
Enable image previews (requires `ueberzug` or `w3mimgdisplay`):
```bash
set preview_images true
set preview_images_method ueberzug
```

Custom preview in `scope.sh`:
```bash
# Add video preview with mpv
case "$extension" in
    mp4|avi) mpv --no-audio --vo=tct "$path" ;;
esac
```

### Scripting
Automate file selection:
```bash
ranger --choosefile=/tmp/selected.txt
cat /tmp/selected.txt  # Outputs selected file
```

Batch process with `xargs`:
```bash
ranger --choosefiles=/tmp/files.txt
cat /tmp/files.txt | xargs -I {} mv {} /archive/
```

## Niche Usages
### Directory Navigation
Choose directory:
```bash
ranger --choosedir=/tmp/dir
cd $(cat /tmp/dir)
```

### Automation
Cron job to archive old files:
```bash
ranger --choosefiles=/tmp/files.txt --cmd "mark mtime -7"
cat /tmp/files.txt | xargs tar -czf archive.tar.gz
```

### With Other Tools
With `fzf`:
```bash
ranger --choosefile=/tmp/file.txt
cat /tmp/file.txt | fzf --preview 'bat --color=always {}'
```

With `rg`:
```bash
ranger --choosefiles=/tmp/files.txt
cat /tmp/files.txt | xargs rg "error"
```

With `fd`:
```bash
fd -t f | ranger --cmd "select_file {}"
```

### CI/CD Integration
GitHub Actions:
```yaml
- name: Select files
  run: ranger --choosefiles=/tmp/files.txt --cmd "mark *.py"
```

### Edge Cases
- Custom config dir: `ranger --confdir=/custom/ranger`.
- Debug crashes: `ranger --debug > ranger.log`.
- Non-standard chars: Escape in `rc.conf` or use `:shell`.
- Large dirs: Set `set collapse_preview false` for performance.

## Practical Examples
1. **Launch Ranger**:
   ```bash
   ranger
   ```

2. **Copy Config**:
   ```bash
   ranger --copy-config=all
   ```

3. **Show Hidden Files**:
   ```bash
   ranger --cmd "set show_hidden true"
   ```

4. **Select File with Fzf**:
   ```bash
   ranger --choosefile=/tmp/file.txt
   cat /tmp/file.txt | fzf
   ```

5. **Batch Trash**:
   ```bash
   ranger --cmd "mark *.bak" --cmd "trash"
   ```

6. **Preview Images**:
   ```bash
   ranger --cmd "set preview_images true"
   ```

7. **Sort by Time**:
   ```bash
   ranger --cmd "set sort mtime"
   ```

8. **Bulk Rename**:
   ```bash
   ranger --cmd "mark *.txt" --cmd "bulkrename"
   ```

9. **Search Files**:
   ```bash
   ranger --cmd "filter *.py"
   ```

10. **Run Shell Command**:
    ```bash
    ranger --cmd "shell mv %s /backup/"
    ```

11. **Bookmark Dir**:
    ```bash
    ranger --cmd "bookmark a"
    ```

12. **CI/CD File Selection**:
    ```bash
    ranger --choosefiles=/tmp/files.txt --cmd "mark *.log"
    cat /tmp/files.txt | xargs tar -czf logs.tar.gz
    ```

## Performance Tips
- **Limit Previews**: Disable `preview_files` for large dirs.
- **Prune Dirs**: Add to `.gitignore` or use `:filter -x`.
- **Lightweight Preview**: Use `bat` instead of `highlight` for text.
- **Cache**: Set `cachedir` to fast storage (`--cachedir=/tmp/ranger`).
- **Scripting**: Use `--choosefile` for non-interactive tasks.
- **Large Repos**: Set `collapse_preview true` in `rc.conf`.

## Troubleshooting
- **No Previews**: Install `ueberzug`, `w3mimgdisplay`, or check `scope.sh`.
- **Keybindings Fail**: Check `rc.conf` or press `?` for defaults.
- **Permission Errors**: Run `sudo ranger` or fix perms: `chmod -R u+rw ~/.config/ranger`.
- **Slow Performance**: Disable previews or limit depth: `:set max_preview_size 100000`.
- **Crashes**: Run `ranger --debug` and check logs.
- **Windows TUI**: Use WSL for better `ncurses` support.

Niche: Debug with `PYTHONDEBUG=1 ranger`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `echo "alias r='ranger'" >> ~/.zshrc`.
  - Fish: `function r; ranger $argv; end`.

- **Fzf**:
  ```bash
  ranger --choosefile=/tmp/file.txt
  cat /tmp/file.txt | fzf --preview 'bat --color=always {}'
  ```

- **Ripgrep**:
  ```bash
  ranger --choosefiles=/tmp/files.txt
  cat /tmp/files.txt | xargs rg "error"
  ```

- **Fd**:
  ```bash
  fd -t f | ranger --cmd "select_file {}"
  ```

- **Git**:
  ```bash
  git status --porcelain | cut -c 4- | ranger --cmd "select_file {}"
  ```

- **Editors**:
  - Vim: `ranger --choosefile=/tmp/file.txt && vim $(cat /tmp/file.txt)`.
  - VS Code: Task: `"command": "ranger --choosefile=/tmp/file.txt && code $(cat /tmp/file.txt)"`.
  - Emacs: Script with `ranger --choosefile`.

- **Advanced Niche**:
  - CI/CD: `ranger --choosefiles=/tmp/files.txt --cmd "mark *.py" && cat /tmp/files.txt | xargs pylint`.
  - Docker: `docker run -v $(pwd):/data ghcr.io/ranger/ranger ranger`.
  - Pass: `pass show file/token | ranger --cmd "shell echo {} > token.txt"`.

## Resources
- [Official Repository](https://github.com/ranger/ranger)
- [Documentation](https://ranger.github.io/)
- [Man Page](https://github.com/ranger/ranger/blob/master/doc/ranger.1)
- [Wiki](https://github.com/ranger/ranger/wiki)
- Run `man ranger` or `ranger --help` for quick reference.
- Community: #ranger on Libera.Chat, GitHub Issues, Stack Overflow.

## Conclusion
`ranger` is a powerful, customizable terminal file manager that blends Vim-like efficiency with robust file handling and preview capabilities. This guide covers its full range, from basic navigation to advanced scripting and integrations, empowering you to streamline file management. Experiment with the examples to make `ranger` a key part of your terminal workflow![](https://github.com/ranger/ranger)[](https://luxagraf.net/src/how-use-ranger-command-line-file-browser)[](https://linuxconfig.org/introduction-to-ranger-file-manager)