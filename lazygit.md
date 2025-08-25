# `lazygit`

## Introduction
`lazygit` is a terminal-based user interface (TUI) for Git, designed to simplify and accelerate Git workflows through an intuitive, keyboard-driven interface. Written in Go using the `gocui` library, it provides a visual alternative to traditional Git CLI commands, rivaling GUI tools like Sourcetree or GitKraken. As of the latest version (0.44.1, released September 2025), `lazygit` includes features like enhanced custom command support, improved merge conflict resolution, and better integration with Git worktrees. It’s ideal for developers, DevOps engineers, and power users who prefer a terminal-centric workflow but want the visual clarity of a GUI.

`lazygit` excels in scenarios requiring rapid Git operations, such as staging hunks, interactive rebasing, or managing branches, without the complexity of raw Git commands. It integrates seamlessly with tools like `fzf`, `rg`, and shells, offering a lightweight, customizable solution for Git management.[](https://github.com/jesseduffield/lazygit)[](https://medium.com/%40rasmusfangel/level-up-git-with-lazygit-b5e6c923c5d7)

## Installation
`lazygit` is available on Linux, macOS, Windows, and FreeBSD. Below are common installation methods, updated for the latest practices, including containerized options:

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian (via Launchpad PPA):
    ```bash
    sudo add-apt-repository ppa:lazygit-team/daily
    sudo apt update
    sudo apt install lazygit
    ```
  - Fedora: `sudo dnf install lazygit`
  - Arch Linux: `sudo pacman -S lazygit` or `yay -S lazygit-bin` (AUR)
  - Nix: `nix-env -i lazygit`
- **Binary Download**:
  ```bash
  VER="0.44.1"
  wget -O lazygit.tgz https://github.com/jesseduffield/lazygit/releases/download/v${VER}/lazygit_${VER}_Linux_x86_64.tar.gz
  tar xvf lazygit.tgz
  sudo mv lazygit /usr/local/bin/
  ```
- **From Source** (requires Go):
  ```bash
  go install github.com/jesseduffield/lazygit@latest
  ```
- **Docker**:
  ```bash
  docker run --rm -v $HOME/.gitconfig:/root/.gitconfig -v $(pwd):/data -it ghcr.io/jesseduffield/lazygit lazygit
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install jesseduffield/lazygit/lazygit
  ```
- **MacPorts**:
  ```bash
  sudo port install lazygit
  ```
- **Nix**: `nix-env -i lazygit`

### Windows
- **Winget** (recommended):
  ```bash
  winget install JesseDuffield.lazygit
  ```
- **Scoop**:
  ```bash
  scoop install lazygit
  ```
- **Chocolatey**:
  ```bash
  choco install lazygit
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Manual**: Download binaries from [Lazygit Releases](https://github.com/jesseduffield/lazygit/releases) and add to PATH.

### Dependencies
- **Git**: Required for all operations.
- **bash`/`zsh`/`fish`: For shell integration and scripting.
- **Go**: For source builds.
- **delta`/`diff-so-fancy`: Optional for enhanced diff rendering.

### Verify Installation
Run `lazygit --version` to confirm installation. Example output:
```
lazygit version 0.44.1
```
Ensure Git is configured: `git --version`.[](https://github.com/jesseduffield/lazygit)[](https://computingforgeeks.com/how-to-install-and-use-lazygit-a-simple-terminal-ui-for-git-commands/)

## Basic Usage
`lazygit` launches a TUI inside a Git repository, providing a visual interface for Git operations. The basic syntax is:
```bash
lazygit [OPTIONS]
```

- **OPTIONS**: Flags to customize behavior (e.g., `--debug`, `--config`).
- **Default Behavior**: Opens TUI in the current Git repository.

### Simple Example
Navigate to a Git repository and run:
```bash
cd my-repo
lazygit
```
Output: A TUI with panels for files, commits, branches, stashes, and status.

Stage a file: Press `Space` on a file in the "Files" panel.
Commit: Press `c`, enter a message, and press `Enter`.
Push: Press `Shift+P`.

Add alias for convenience:
```bash
echo "alias lg='lazygit'" >> ~/.zshrc
source ~/.zshrc
lg
```

## Key Features
`lazygit`’s strengths include:
- **Keyboard-Driven**: Fully navigable without a mouse, with customizable keybindings.
- **Visual Interface**: Panels for files, commits, branches, stashes, and diffs.
- **Interactive Rebasing**: Simplifies rebase operations without editing TODO files.
- **Patch Building**: Stage specific hunks or lines with fine-grained control.
- **Worktree Support**: Create and manage Git worktrees for parallel branch work.
- **Gitflow Integration**: Supports Gitflow workflows with `i` in branches view.
- **Custom Commands**: Extend functionality with user-defined commands.
- **Cross-Platform**: Works on Linux, macOS, Windows, and FreeBSD.

New in 0.44.1: Improved diff panel scrolling, enhanced custom patch options, and better support for large repositories.[](https://github.com/jesseduffield/lazygit)[](https://medium.com/%40rasmusfangel/level-up-git-with-lazygit-b5e6c923c5d7)[](https://linuxcommandlibrary.com/man/lazygit)

## Common Options
Frequently used options (see `lazygit --help` for a full list):

| Option | Description | Example |
|--------|-------------|---------|
| `--help`, `-h` | Display help | `lazygit --help` |
| `--version` | Show version | `lazygit --version` |
| `--config` | Specify config file | `lazygit --config ~/.config/lazygit/custom.yml` |
| `--debug` | Enable debug logging | `lazygit --debug` |
| `--logs` | View logs | `lazygit --logs` |
| `--git-dir` | Set Git repository directory | `lazygit --git-dir .git` |
| `--work-tree` | Set working tree directory | `lazygit --work-tree .` |

### Keybindings (Default)
- **Navigation**: `←`, `→`, `↑`, `↓` or `h`, `j`, `k`, `l`.
- **Panels**: `1` (status), `2` (files), `3` (branches), etc., or `Tab` to cycle.
- **Stage File**: `Space` (toggle staged).
- **Commit**: `c` (commit staged changes).
- **Push/Pull**: `Shift+P` (push), `p` (pull).
- **Quit**: `q`.
- **Help**: `?` (show keybindings).
- **Patch Building**: `t` (add patched hunks), `Ctrl+P` (custom patch options).
- **Worktree**: `w` (create/switch worktree).
- **Refresh**: `Shift+R` (refresh files).

Full keybindings: [Lazygit Keybindings](https://github.com/jesseduffield/lazygit/blob/master/docs/keybindings/Keybindings_en.md).[](https://github.com/jesseduffield/lazygit)[](https://medium.com/%40rasmusfangel/level-up-git-with-lazygit-b5e6c923c5d7)

## Advanced Usage
### Configuration
Customize via `~/.config/lazygit/config.yml`:
```yaml
gui:
  theme:
    activeBorderColor:
      - green
      - bold
    inactiveBorderColor:
      - white
  scrollHeight: 5
customCommands:
  - key: 'Ctrl+C'
    command: 'git cherry-pick {{.SelectedCommit.Sha}}'
    context: 'commits'
```
Load custom config:
```bash
lazygit --config ~/custom.yml
```

### Patch Building
Build custom patch from a commit:
- Press `Enter` on a commit, then on a file.
- Press `Space` to select lines for a patch.
- Press `Ctrl+P` to apply/remove patch or create a new commit.[](https://github.com/jesseduffield/lazygit)

### Worktrees
Create worktree:
- In branches panel, select a branch and press `w`.
Switch or delete worktrees:
- Press `Shift+W` in branches panel.

### Gitflow
Start Gitflow feature:
```bash
# In branches view, press `i`, select "Start feature"
```
Finish feature:
```bash
# Press `i`, select "Finish feature"
```
Requires `gitflow` installed.[](https://github.com/jesseduffield/lazygit)

### Custom Commands
Define in `config.yml`:
```yaml
customCommands:
  - key: 'Ctrl+B'
    command: 'git branch --set-upstream-to=origin/{{.SelectedBranch.Name}}'
    context: 'branches'
```
Execute: Press `Ctrl+B` in branches panel.[](https://github.com/jesseduffield/lazygit)

### Merge Conflicts
Resolve conflicts:
- In files panel, select conflicted file.
- Use diff panel (`PgUp`/`PgDn` or `Ctrl+U`/`Ctrl+D`) to stage resolutions.
- Commit when resolved.[](https://computingforgeeks.com/how-to-install-and-use-lazygit-a-simple-terminal-ui-for-git-commands/)

### Scripting
Automate commit:
```bash
lazygit --command "git add . && git commit -m 'Auto commit'"
```

Batch stage and commit:
```bash
find . -name "*.py" | xargs git add && lazygit --command "git commit -m 'Update Python files'"
```

### With Fzf
Interactive branch selection:
```bash
git branch | fzf | xargs lazygit --command "git checkout {}"
```

### Debugging
Run with logs:
```bash
lazygit --debug --logs
```
Logs saved to `~/.config/lazygit/logs/lazygit.log`.

### Edge Cases
- Custom Git dir: `lazygit --git-dir /custom/.git --work-tree /custom`.
- Large repos: Increase `gui.scrollHeight` in config.
- Non-standard chars: Escape in `config.yml`.
- Custom diff: Set `git.paging.externalDiffCommand: delta`.

## Practical Examples
1. **Launch Lazygit**:
   ```bash
   lazygit
   ```

2. **Stage and Commit**:
   ```bash
   lazygit  # Press Space on files, c to commit
   ```

3. **Push Changes**:
   ```bash
   lazygit  # Press Shift+P
   ```

4. **Interactive Branch with Fzf**:
   ```bash
   git branch | fzf | xargs lazygit --command "git checkout {}"
   ```

5. **Create Worktree**:
   ```bash
   lazygit  # In branches, select branch, press w
   ```

6. **Custom Patch**:
   ```bash
   lazygit  # Enter commit, select file, Space on lines, Ctrl+P
   ```

7. **Gitflow Feature**:
   ```bash
   lazygit  # In branches, press i, select "Start feature"
   ```

8. **Merge Branch**:
   ```bash
   lazygit  # In branches, select branch, press m
   ```

9. **Resolve Conflicts**:
   ```bash
   lazygit  # Select conflicted file, resolve in diff panel
   ```

10. **Custom Command**:
    ```bash
    lazygit --config custom.yml  # With Ctrl+B for upstream
    ```

11. **Debug Logs**:
    ```bash
    lazygit --debug --logs
    ```

12. **Scripted Commit**:
    ```bash
    lazygit --command "git commit -m 'Auto commit'"
    ```

## Performance Tips
- **Small Repos**: Limit files with `.gitignore`.
- **Large Repos**: Increase `gui.scrollHeight` or use `--no-git-status`.
- **Diffs**: Use `delta` for faster rendering.
- **Scripting**: Avoid TUI for batch operations.
- **Monorepo**: Filter with `git status --porcelain | rg ...`.
- **Refresh**: Minimize `Shift+R` in large repos.

## Troubleshooting
- **No Git Repo**: Run in a Git directory or use `--git-dir`.
- **Keybindings Fail**: Check `config.yml` or press `?`.
- **Slow Performance**: Increase `scrollHeight` or disable animations in config.
- **Conflicts**: Use diff panel or drop to `git mergetool`.
- **Log Issues**: Check `~/.config/lazygit/logs/lazygit.log`.
- **Windows**: Use WSL or Git Bash for better TUI support.

Niche: Debug with `lazygit --debug --logs`.[](https://linuxcommandlibrary.com/man/lazygit)

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `echo "alias lg='lazygit'" >> ~/.zshrc`.
  - Fish: `function lg; lazygit $argv; end`.

- **Fzf**:
  ```bash
  git branch | fzf | xargs lazygit --command "git checkout {}"
  ```

- **Ripgrep**:
  ```bash
  rg --files | fzf | xargs lazygit --command "git add {}"
  ```

- **Fd**:
  ```bash
  fd -t f | fzf | xargs lazygit --command "git add {}"
  ```

- **Git**:
  ```bash
  git add . && lazygit
  ```

- **Editors**:
  - Vim: `lazygit --command "git add . && git commit"`.
  - VS Code: Task: `"command": "lazygit"`.
  - Emacs: Script with `lazygit --command`.

- **Advanced Niche**:
  - CI/CD: `lazygit --command "git push"`.
  - Docker: `docker run -v $(pwd):/data ghcr.io/jesseduffield/lazygit`.
  - Pass: `pass show git/token | gh auth login --with-token && lazygit`.

## Resources
- [Official Repository](https://github.com/jesseduffield/lazygit)
- [Documentation](https://github.com/jesseduffield/lazygit/blob/master/docs)
- [Keybindings](https://github.com/jesseduffield/lazygit/blob/master/docs/keybindings/Keybindings_en.md)
- Run `lazygit --help` for quick reference.
- Community: GitHub Discussions, Discord, r/git.

## Conclusion
`lazygit` is a game-changer for Git workflows, blending the speed of CLI with the clarity of a GUI. This guide covers its full range, from basic operations to advanced customizations and integrations, empowering you to streamline Git tasks. Experiment with the examples to make `lazygit` a cornerstone of your development workflow![](https://github.com/jesseduffield/lazygit)[](https://medium.com/%40rasmusfangel/level-up-git-with-lazygit-b5e6c923c5d7)[](https://linuxcommandlibrary.com/man/lazygit)