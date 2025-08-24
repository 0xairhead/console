# `stow`

## Introduction
`stow` is a command-line tool for managing symlinks to install and organize software or configuration files (dotfiles) in a clean, non-intrusive way. Written in Perl, it’s designed to manage multiple versions of software or configurations by creating symlinks from a central "stow" directory to target locations, typically `$HOME` for dotfiles. As of the latest version (2.4.0, released May 2024), `stow` includes improved conflict detection, better support for directory folding, and enhanced scripting capabilities.

`stow` is ideal for developers managing dotfiles across systems, sysadmins deploying software in shared environments, and power users who want a lightweight, scriptable way to organize configurations. It integrates well with tools like `git`, `fzf`, and shell scripts, making it a cornerstone for reproducible setups without modifying original files.

## Installation
`stow` is available on Linux, macOS, and Windows (via WSL or native ports). Below are common installation methods, updated for the latest practices, including containerized options:

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian: `sudo apt install stow`
  - Fedora: `sudo dnf install stow`
  - Arch Linux: `sudo pacman -S stow`
  - Nix: `nix-env -i stow`
- **From Source** (requires Perl and `make`):
  ```bash
  git clone https://git.savannah.gnu.org/git/stow.git
  cd stow
  ./configure
  make
  sudo make install
  ```
- **Binary Download**: Pre-built binaries from [GNU Stow Releases](https://ftp.gnu.org/gnu/stow/) or [GitHub](https://github.com/aspiers/stow).
- **Docker**: For isolated environments:
  ```bash
  docker run --rm -v $(pwd):/data -it alpine sh -c 'apk add stow && stow --dir=/data'
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install stow
  ```
- **MacPorts**:
  ```bash
  sudo port install stow
  ```
- **Nix**: `nix-env -i stow`

### Windows
- **Scoop**:
  ```bash
  scoop install stow
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Native (Manual)**: Install Perl (e.g., Strawberry Perl), then build from source or use [MSYS2](https://www.msys2.org/):
  ```bash
  pacman -S stow
  ```

### Dependencies
- **Perl**: Required (usually pre-installed on Unix-like systems).
- **Git**: Optional for version-controlled dotfiles.
- **make**: Optional for source builds.

### Verify Installation
Run `stow --version` to confirm installation. Example output:
```
stow 2.4.0
```
Ensure Perl is available: `perl --version`.

## Basic Usage
`stow` creates symlinks from a "stow" directory (containing package directories) to a target directory (e.g., `$HOME`). The basic syntax is:
```bash
stow [OPTIONS] PACKAGE...
```

- **PACKAGE**: Directory in the stow directory containing files to symlink.
- **OPTIONS**: Flags to modify behavior (e.g., delete, restow, simulate).
- **Default Behavior**: Symlinks files from `stow-dir/PACKAGE/` to `target-dir`.

### Simple Example
Assume a stow directory `~/dotfiles` with a package `vim`:
```
~/dotfiles/vim/.vimrc
~/dotfiles/vim/.vim/
```

Stow the `vim` package to `$HOME`:
```bash
cd ~/dotfiles
stow vim
```
Output: Creates `~/.vimrc` and `~/.vim/` as symlinks to `~/dotfiles/vim/.vimrc` and `~/dotfiles/vim/.vim/`.

Unstow (remove symlinks):
```bash
stow -D vim
```

Restow (refresh symlinks):
```bash
stow -R vim
```

## Key Features
`stow`’s power lies in its simplicity and flexibility:
- **Symlink Management**: Non-destructively links files to target locations.
- **Directory Folding**: Automatically handles nested directories (e.g., `~/.config/`).
- **Conflict Detection**: Prevents overwriting existing files unless forced.
- **Git Integration**: Pairs with Git for version-controlled dotfiles.
- **Scriptable**: Easy to automate with shell scripts or tools like `fzf`.
- **Cross-Platform**: Works on any system with Perl and a filesystem supporting symlinks.
- **No Dependencies**: Minimal runtime requirements beyond Perl.

New in 2.4.0: Enhanced conflict reporting, support for `--no-folding` to disable directory folding, and improved performance for large setups.

## Common Options
Frequently used options (see `stow --help` for a full list):

| Option | Description | Example |
|--------|-------------|---------|
| `-d`, `--dir` | Set stow directory (default: current dir) | `stow -d ~/dotfiles vim` |
| `-t`, `--target` | Set target directory (default: parent of stow dir) | `stow -t ~ vim` |
| `-S`, `--stow` | Stow package (default action) | `stow -S vim` |
| `-D`, `--delete` | Unstow package (remove symlinks) | `stow -D vim` |
| `-R`, `--restow` | Unstow then restow | `stow -R vim` |
| `--no` | Simulate (dry-run) | `stow --no vim` |
| `-v`, `--verbose` | Increase verbosity (0-5 levels) | `stow -v2 vim` |
| `--no-folding` | Disable directory folding | `stow --no-folding vim` |
| `--ignore` | Ignore files matching regex | `stow --ignore '\.bak$' vim` |
| `--defer` | Skip files if target exists | `stow --defer '.zshrc' vim` |
| `--override` | Overwrite existing files | `stow --override '.zshrc' vim` |

## Advanced Usage
### Directory Setup
Typical dotfiles structure:
```
~/dotfiles/
├── vim/
│   ├── .vimrc
│   └── .vim/
├── zsh/
│   ├── .zshrc
│   └── .zsh/
└── git/
    └── .gitconfig
```

Stow multiple packages:
```bash
stow vim zsh git
```

Custom stow/target dirs:
```bash
stow -d ~/configs -t /etc vim
```

### Conflict Management
Check conflicts (dry-run):
```bash
stow --no vim
```

Override specific conflicts:
```bash
stow --override '.zshrc' zsh
```

Defer if exists:
```bash
stow --defer '.bashrc' bash
```

### Git Integration
Initialize Git for dotfiles:
```bash
cd ~/dotfiles
git init
git add .
git commit -m "Initial dotfiles"
git remote add origin git@server:dotfiles.git
```

Sync across systems:
```bash
git pull origin main
stow -R vim zsh
```

### Ignoring Files
Ignore backup files:
```bash
stow --ignore '\.bak$' vim
```

Use `.stow-local-ignore` in package dir:
```bash
echo "*.bak" > ~/dotfiles/vim/.stow-local-ignore
stow vim
```

Global ignore (`~/.stowrc`):
```bash
--ignore='\.bak$'
--ignore='\.swp$'
```

### Directory Folding
Disable folding for explicit symlinks:
```bash
stow --no-folding vim
```
Example: Creates `~/.vim` as a symlink instead of linking individual files.

### Simulation and Debugging
Verbose dry-run:
```bash
stow -nv2 vim
```

Debug conflicts:
```bash
stow -v3 vim 2> stow.log
```

### Scripting
Automate stow for all packages:
```bash
for pkg in ~/dotfiles/*; do stow -d ~/dotfiles -t ~ "$(basename "$pkg")"; done
```

With `fzf` for interactive selection:
```bash
ls ~/dotfiles | fzf -m | xargs stow -d ~/dotfiles -t ~
```

### System-Wide Installs
Install software to `/usr/local`:
```bash
stow -d ~/software -t /usr/local myapp
```
Requires `sudo` if target is restricted.

## Niche Usages
### Dotfiles Management
Bootstrap new system:
```bash
git clone git@server:dotfiles.git ~/dotfiles
cd ~/dotfiles
stow -R vim zsh git
```

### Multi-User Setup
Shared dotfiles with user-specific targets:
```bash
stow -d /shared/dotfiles -t ~user1 vim
stow -d /shared/dotfiles -t ~user2 vim
```

### Versioned Software
Manage multiple versions:
```
~/software/
├── myapp-1.0/
│   ├── bin/myapp
│   └── lib/
├── myapp-2.0/
│   ├── bin/myapp
│   └── lib/
```

Stow specific version:
```bash
stow -d ~/software -t /usr/local myapp-1.0
```

Switch versions:
```bash
stow -D -d ~/software -t /usr/local myapp-1.0
stow -d ~/software -t /usr/local myapp-2.0
```

### CI/CD Integration
Automate dotfile setup in GitHub Actions:
```yaml
- name: Install dotfiles
  run: |
    git clone https://github.com/user/dotfiles.git
    cd dotfiles
    stow -t ~ vim zsh
```

### With Fzf
Interactive package selection:
```bash
ls ~/dotfiles | fzf --preview 'tree ~/dotfiles/{}' | xargs stow -d ~/dotfiles -t ~
```

### Backup and Restore
Backup dotfiles:
```bash
tar -czf ~/dotfiles-backup-$(date +%F).tar.gz ~/dotfiles
```

Restore:
```bash
tar -xzf dotfiles-backup.tar.gz -C ~
stow -R vim zsh
```

### Cross-Platform Dotfiles
Platform-specific packages:
```
~/dotfiles/
├── linux/
│   └── .zshrc
├── macos/
│   └── .zshrc
```

Stow based on OS:
```bash
if [[ "$OSTYPE" == "darwin"* ]]; then
  stow -d ~/dotfiles -t ~ macos
else
  stow -d ~/dotfiles -t ~ linux
fi
```

### Edge Cases
- Ignore Git metadata: `--ignore '^\.git$'`.
- Handle spaces in filenames: Use `--ignore` or escape paths.
- Nested stow dirs: `stow -d ~/dotfiles/subdir -t ~ package`.
- Debug symlink loops: `stow -nv3`.
- Temporary stow: `stow -t /tmp/test vim`.

## Practical Examples
1. **Stow Vim Config**:
   ```bash
   stow -d ~/dotfiles -t ~ vim
   ```

2. **Unstow Zsh**:
   ```bash
   stow -D -d ~/dotfiles -t ~ zsh
   ```

3. **Restow All Packages**:
   ```bash
   stow -R -d ~/dotfiles -t ~ vim zsh git
   ```

4. **Interactive Stow with Fzf**:
   ```bash
   ls ~/dotfiles | fzf -m | xargs stow -d ~/dotfiles -t ~
   ```

5. **Ignore Backups**:
   ```bash
   stow --ignore '\.bak$' vim
   ```

6. **System-Wide Install**:
   ```bash
   sudo stow -d ~/software -t /usr/local myapp
   ```

7. **Git Sync and Stow**:
   ```bash
   cd ~/dotfiles
   git pull origin main
   stow -R vim zsh
   ```

8. **Dry-Run Check**:
   ```bash
   stow --no -v2 vim
   ```

9. **Platform-Specific Stow**:
   ```bash
   stow -d ~/dotfiles -t ~ $(uname | grep -qi darwin && echo macos || echo linux)
   ```

10. **Batch Unstow**:
    ```bash
    ls ~/dotfiles | grep old | xargs stow -D -d ~/dotfiles -t ~
    ```

11. **Backup Dotfiles**:
    ```bash
    tar -czf ~/dotfiles-backup-$(date +%F).tar.gz ~/dotfiles
    ```

12. **Override Conflict**:
    ```bash
    stow --override '.bashrc' bash
    ```

## Performance Tips
- **Small Stow Dir**: Keep `~/dotfiles` lean; avoid unnecessary files.
- **Git**: Use sparse checkouts for large repos.
- **Dry-Run**: Always test with `--no` for large setups.
- **Ignore Patterns**: Use `.stow-local-ignore` to reduce processing.
- **Scripting**: Batch stow commands to minimize overhead.
- **Large Setups**: Split packages into subdirs (e.g., `~/dotfiles/core/`, `~/dotfiles/extra/`).
- **Verbose**: Use `-v1` or `-v2` to avoid excessive output.

Niche: For massive setups, pre-check with `find ~/dotfiles -type f` to index.

## Troubleshooting
- **Conflicts**: Use `--no` to diagnose; resolve with `--override` or `--defer`.
- **Broken Symlinks**: Check with `find ~ -type l -xtype l`; restow with `-R`.
- **Permissions**: Use `sudo` for system-wide targets; ensure `chmod 700 ~/dotfiles`.
- **Git Issues**: Verify `git status` in stow dir; fix conflicts with `git pull --rebase`.
- **Missing Files**: Check `.stow-local-ignore` or `--ignore` patterns.
- **Symlink Loops**: Use `--no-folding` or debug with `-v3`.

Niche: Log to file: `stow -v3 vim 2> stow.log`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: No built-in completions; script aliases:
    ```bash
    alias dot='stow -d ~/dotfiles -t ~'
    ```
  - Fish: `function dot; stow -d ~/dotfiles -t ~ $argv; end`.

- **Fzf**:
  ```bash
  ls ~/dotfiles | fzf --preview 'tree ~/dotfiles/{}' | xargs stow -d ~/dotfiles -t ~
  ```

- **Ripgrep**:
  ```bash
  rg --files ~/dotfiles | fzf | xargs -I {} dirname {} | xargs stow -d ~/dotfiles -t ~
  ```

- **Fd**:
  ```bash
  fd -t d . ~/dotfiles | sed 's|.*/dotfiles/||' | fzf | xargs stow -d ~/dotfiles -t ~
  ```

- **Git**:
  ```bash
  cd ~/dotfiles
  git pull
  stow -R $(ls)
  ```

- **Editors**:
  - Vim: Edit dotfiles and stow: `vim ~/dotfiles/vim/.vimrc && stow -R vim`.
  - Emacs: Use `dired` to manage `~/dotfiles`, then `stow`.
  - VS Code: Script stow in tasks: `"task": "stow -d ~/dotfiles -t ~ vim"`.

- **Advanced Niche**:
  - CI/CD: Stow configs in Docker: `docker run -v ~/dotfiles:/dotfiles alpine sh -c 'stow -d /dotfiles -t /root vim'`.
  - Kubernetes: Mount dotfiles volume and stow in pod.
  - Ansible: Automate with `command: stow -d /dotfiles -t ~ vim`.

## Resources
- [Official Website](https://www.gnu.org/software/stow/)
- [Git Repository](https://git.savannah.gnu.org/git/stow.git)
- [Manual](https://www.gnu.org/software/stow/manual/)
- Run `stow --help` or `man stow` for quick reference.
- Community: GitHub Issues, r/commandline, Stack Overflow.

## Conclusion
`stow` is a lightweight, powerful tool for managing symlinks, perfect for organizing dotfiles or software installations. This comprehensive guide covers its full range, from basic usage to advanced scripting and niche integrations, empowering you to create reproducible, version-controlled setups. Experiment with the examples to streamline your configuration management!