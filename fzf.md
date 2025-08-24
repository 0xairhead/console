# `fzf`

## Introduction
`fzf` is a command-line fuzzy finder written in Go, designed for interactive and rapid file, command, or text searching. It's a powerful, general-purpose tool that excels at filtering and selecting items from lists, integrating seamlessly with other command-line tools like `fd`, `rg`, or `find`. As of the latest version (0.55.0, released July 2024), `fzf` includes enhancements like improved scoring algorithms, customizable layouts, and better terminal integration with features like truecolor support and preview window improvements.

`fzf` is ideal for developers navigating codebases, sysadmins searching logs, and power users who need quick access to files, directories, or command history. Its fuzzy matching algorithm, combined with a responsive interface, makes it faster and more intuitive than traditional tools like `grep` or `selecta`. It’s particularly powerful when paired with tools like `fd` for file discovery or `rg` for content searching, enabling complex workflows with minimal effort.

## Installation
`fzf` is available on Linux, macOS, and Windows. Below are common installation methods, updated for the latest practices, including containerized and reproducible options:

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian: `sudo apt install fzf`
  - Fedora: `sudo dnf install fzf`
  - Arch Linux: `sudo pacman -S fzf`
  - Nix: `nix-env -i fzf`
- **From Source** (requires Go):
  ```bash
  go install github.com/junegunn/fzf@latest
  ```
- **Binary Download**: Grab pre-built binaries from [GitHub Releases](https://github.com/junegunn/fzf/releases).
- **Docker**: For isolated environments:
  ```bash
  docker run --rm -it -v $(pwd):/data junegunn/fzf fzf
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install fzf
  ```
  Post-install (optional): `$(brew --prefix)/opt/fzf/install` to set up shell completions and key bindings.
- **MacPorts**:
  ```bash
  sudo port install fzf
  ```
- **Nix**: `nix-env -i fzf`

### Windows
- **Chocolatey**:
  ```bash
  choco install fzf
  ```
- **Scoop**:
  ```bash
  scoop install fzf
  ```
- **Winget**:
  ```bash
  winget install junegunn.fzf
  ```
- **Manual**: Download binaries from [GitHub Releases](https://github.com/junegunn/fzf/releases). Supports x86_64 and ARM64.
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.

### Verify Installation
Run `fzf --version` to confirm installation. Example output:
```
0.55.0 (git)
```
Ensure shell integration by sourcing completions/key bindings (e.g., `source /usr/share/fzf/key-bindings.zsh` for Zsh).

## Basic Usage
`fzf` filters input from stdin or a command and presents an interactive interface for selecting items. The basic syntax is:
```bash
fzf [OPTIONS]
```
Or with a command:
```bash
COMMAND | fzf [OPTIONS]
```

- **Input**: Typically piped from commands like `ls`, `fd`, or `rg`.
- **Output**: Selected item(s) printed to stdout.
- **OPTIONS**: Flags to customize behavior (e.g., preview, multi-select, key bindings).

### Simple Example
Fuzzy-find files in the current directory:
```bash
ls | fzf
```
Output: Selected file path (e.g., `src/main.rs`) after navigating and pressing Enter.

Combine with `fd` for better file discovery:
```bash
fd . | fzf
```

Search command history:
```bash
history | fzf
```

## Key Features
`fzf`’s power comes from its interactivity and flexibility:
- **Fuzzy Matching**: Matches partial, out-of-order substrings (e.g., `fb` matches `foo/bar`).
- **Interactive Interface**: Real-time filtering with arrow keys, Enter to select, or Ctrl+C to exit.
- **Preview Window**: Shows file contents or details during selection (customizable).
- **Multi-Select**: Select multiple items with `--multi` or Tab/Shift+Tab.
- **Customizable Layout**: Adjust preview size, position, or hide with `--layout`.
- **Key Bindings**: Extensible bindings for actions like execute, toggle, or jump.
- **Shell Integration**: Built-in completions and bindings for Bash, Zsh, Fish.
- **Color Support**: Truecolor and customizable themes via `--color`.

New in 0.55.0: Improved fuzzy matching for diacritics, better preview scrolling, and `--sync` for non-interactive mode.

## Common Options
Frequently used flags (see `fzf --help` for a full list):

| Option | Description | Example |
|--------|-------------|---------|
| `-m`, `--multi` | Enable multi-select | `fd . | fzf -m` |
| `-q`, `--query` | Initial query | `fzf -q "src"` |
| `--preview` | Show preview window | `fzf --preview 'cat {}'` |
| `--height` | Set interface height | `fzf --height 50%` |
| `--layout` | Set layout (default, reverse, reverse-list) | `fzf --layout=reverse` |
| `-i`, `--case-insensitive` | Case-insensitive matching | `fzf -i` |
| `+i`, `--case-sensitive` | Case-sensitive matching | `fzf +i` |
| `--exact` | Exact matching (disable fuzzy) | `fzf --exact` |
| `--filter` | Non-interactive filter | `fd . | fzf --filter "pattern"` |
| `--bind` | Custom key bindings | `fzf --bind 'ctrl-t:toggle-all'` |
| `--color` | Customize colors | `fzf --color=fg+:red` |
| `--select-1` | Auto-select if one match | `fzf --select-1` |
| `--exit-0` | Exit if no matches | `fzf --exit-0` |
| `--preview-window` | Customize preview (e.g., right, up, hidden) | `fzf --preview-window=up:60%` |

### Shell Integration
Install completions and bindings:
```bash
# Bash
source /usr/share/fzf/key-bindings.bash
source /usr/share/fzf/completion.bash
# Zsh
source /usr/share/fzf/key-bindings.zsh
source /usr/share/fzf/completion.zsh
# Fish
source /usr/share/fish/vendor_completions.d/fzf.fish
```

Default bindings:
- **Ctrl+T**: Paste file/dir selection into command line.
- **Ctrl+R**: Search command history.
- **Alt+C**: Change to selected directory.

## Advanced Usage
### Fuzzy Matching
Basic fuzzy:
```bash
fd . | fzf -q "src rs"  # Matches src/*.rs
```

Exact match:
```bash
fzf --exact -q "main.rs"
```

Extended search syntax:
- `'exact`: Exact match (`'foo`).
- `^prefix`: Starts with (`^src`).
- `suffix$`: Ends with (`.rs$`).
- `!not`: Negate (`!test`).
Example:
```bash
fd . | fzf -q "^src !test .rs$"  # src/*.rs, excluding test
```

### Preview Window
Show file contents:
```bash
fd --type f | fzf --preview 'bat --color=always --style=numbers {}'
```

Dynamic preview (niche):
```bash
fd . | fzf --preview '[[ -f {} ]] && bat {} || tree -C {}'
```

Position/size:
```bash
fzf --preview 'cat {}' --preview-window right:50%:wrap
```

Hide preview dynamically:
```bash
fzf --bind 'ctrl-p:toggle-preview' --preview 'cat {}'
```

### Multi-Select and Actions
Select multiple:
```bash
fd . | fzf -m --bind 'ctrl-a:select-all,ctrl-d:deselect-all'
```

Execute command on selection:
```bash
fd -e py | fzf -m --bind 'ctrl-e:execute(pylint {})'
```

Batch execute:
```bash
fd -e jpg | fzf -m -x 'convert {} -resize 800x600 {}.thumb.jpg'
```

### Key Bindings
Custom bindings:
```bash
fzf --bind 'ctrl-y:execute-silent(echo {} | pbcopy),ctrl-o:execute(vim {})+abort'
```
- Ctrl+Y: Copy to clipboard.
- Ctrl+O: Open in Vim and exit.

Reload input dynamically:
```bash
fzf --bind 'ctrl-r:reload(fd . --type f)' --preview 'cat {}'
```

### Layout and Interface
Reverse layout for top-down:
```bash
fzf --layout=reverse --height 40%
```

Border styles:
```bash
fzf --border=rounded --margin 5%
```

Full-screen toggle:
```bash
fzf --bind 'ctrl-f:toggle-fullscreen'
```

### Non-Interactive Mode
Filter without UI:
```bash
fd . | fzf --filter "src" | head -n 1
```

Sync mode (niche for scripts):
```bash
fd . | fzf --sync -q "main" --select-1
```

### Color Customization
Custom theme:
```bash
fzf --color='bg+:234,fg+:15,pointer:cyan,hl:yellow,hl+:red'
```
Or environment:
```bash
export FZF_DEFAULT_OPTS='--color=fg:252,bg:234,hl:81 --height 50% --layout=reverse'
```

## Niche Usages
### File System Navigation
With `fd`:
```bash
cd $(fd -t d | fzf)
```

With `zoxide`:
```bash
z $(fd -t d | fzf)
```

### Command History
Advanced history search:
```bash
history | fzf --tac --no-sort --exact | cut -c 8-
```

### Git Integration
Select branches:
```bash
git checkout $(git branch --all | fzf | tr -d '[:space:]*')
```

Select files from diff:
```bash
git diff --name-only | fzf -m | xargs git add
```

Commits:
```bash
git log --oneline | fzf --preview 'git show {+1}' | cut -d' ' -f1
```

### Ripgrep Integration
Search file contents:
```bash
rg --files-with-matches "pattern" | fzf --preview 'rg --color=always "pattern" {}'
```

Advanced: Content + file filter:
```bash
fd -t f | fzf --preview 'rg --color=always -C 3 "$(fzf --query)" {}'
```

### Process Management
Kill processes:
```bash
ps aux | fzf -m --preview 'echo {} | awk "{print \$2}" | xargs ps -p' | awk '{print $2}' | xargs kill
```

### SSH and Remote
Select SSH hosts:
```bash
cat ~/.ssh/config | grep '^Host ' | awk '{print $2}' | fzf | xargs ssh
```

### Kubernetes
Select pods:
```bash
kubectl get pods | fzf | awk '{print $1}' | xargs kubectl logs
```

### Scripting and Automation
Dynamic script input:
```bash
#!/bin/bash
file=$(fd --type f | fzf --query "$1" --select-1)
[[ -n "$file" ]] && vim "$file"
```

CI/CD (GitHub Actions):
```yaml
- name: Select files for linting
  run: fd -e py . | fzf --filter "lint" | xargs pylint
```

### Custom Input Sources
Search environment variables:
```bash
printenv | fzf
```

Docker containers:
```bash
docker ps -a | fzf | awk '{print $1}' | xargs docker rm
```

### Edge Cases
- Multi-line input: `--read0` for NUL-terminated:
  ```bash
  fd -0 | fzf --read0
  ```
- Exact match priority: `--tiebreak=index` to prefer earlier lines.
- Handle large inputs: `--no-sort` for speed.
- Preview for non-files: `--preview 'curl -s {}'` for URLs.
- Dynamic query: `fzf --bind 'change:reload(fd . --type f -q {q})'`.

## Practical Examples
1. **Find and Open File in Vim**:
   ```bash
   vim $(fd --type f | fzf --preview 'bat --color=always {}')
   ```

2. **Search Directory and Cd**:
   ```bash
   cd $(fd -t d | fzf)
   ```

3. **Git Add Files**:
   ```bash
   git add $(git status --short | fzf -m | awk '{print $2}')
   ```

4. **Search Ripgrep Results**:
   ```bash
   rg --files-with-matches "error" | fzf --preview 'rg -C 3 "error" {}'
   ```

5. **Kill Processes**:
   ```bash
   ps aux | fzf -m --preview 'echo {} | awk "{print \$2}" | xargs ps -p' | awk '{print $2}' | xargs kill -9
   ```

6. **Dynamic File Preview**:
   ```bash
   fd . | fzf --preview '[[ -f {} ]] && bat {} || tree -C {}' --bind 'ctrl-p:toggle-preview'
   ```

7. **Search History with Execution**:
   ```bash
   eval $(history | fzf --tac --no-sort | cut -c 8-)
   ```

8. **Select SSH Host**:
   ```bash
   ssh $(cat ~/.ssh/config | grep '^Host ' | awk '{print $2}' | fzf)
   ```

9. **Kubernetes Logs**:
   ```bash
   kubectl logs $(kubectl get pods | fzf | awk '{print $1}')
   ```

10. **Batch Rename**:
    ```bash
    fd -e txt | fzf -m | xargs -I {} mv {} {}.bak
    ```

11. **Custom Query Reload**:
    ```bash
    fd --type f | fzf --bind 'change:reload(fd --type f -q {q})' --preview 'bat {}'
    ```

12. **Non-Interactive Filter**:
    ```bash
    fd . | fzf --filter "src" | xargs ls
    ```

## Performance Tips
- **Optimize Input**: Use `fd` or `rg` for faster input than `find`.
- **No Sort**: `--no-sort` for large inputs.
- **Limit Height**: `--height 30%` for faster rendering.
- **Threads**: `fd -j$(nproc) | fzf` for faster input.
- **Preview Cost**: Use lightweight preview (e.g., `head` vs `bat` for huge files).
- **Large Inputs**: `--read0` with `fd -0` for NUL-separated.
- **Scoring**: `--tiebreak=length` for shorter matches first.
- **Monorepo**: Limit scope: `fd src/ | fzf`.

Niche: For massive lists, pre-filter with `head -n 10000` or use `--sync`.

## Troubleshooting
- **No Matches**: Check query syntax, use `-i` for case-insensitive.
- **Slow Performance**: Reduce preview complexity, use `--no-sort`.
- **Preview Not Showing**: Ensure command (e.g., `bat`, `cat`) is installed; test with `--preview 'echo {}'`.
- **Key Bindings Fail**: Source shell completions (`source ~/.fzf.zsh`).
- **Terminal Issues**: Set `--color=16` for older terminals; ensure `TERM=xterm-256color`.
- **Windows Paths**: Use WSL or Git Bash for better compatibility.
- **Large Inputs Hang**: Pipe through `head` or use `--filter`.
- **Completions Missing**: Run `fzf/install` or source manually.

Niche: Debug with `FZF_LOG=debug fzf` to log interactions.

## Integration with Other Tools
- **Shell**:
  - Zsh: `source ~/.fzf.zsh` for bindings/completions.
  - Bash: `source ~/.fzf.bash`.
  - Fish: Built-in via `fzf_key_bindings`.

- **Editors**:
  - Vim/Neovim:
    ```vim
    Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
    Plug 'junegunn/fzf.vim'
    nnoremap <C-p> :Files<CR>
    ```
  - Emacs: Use `fzf.el` or `consult`.
  - VS Code: Extension "fzf-quick-open".

- **Fd**:
  ```bash
  export FZF_DEFAULT_COMMAND='fd --type f --hidden --exclude .git'
  fzf
  ```

- **Ripgrep**:
  ```bash
  rg --files | fzf --preview 'rg -C 3 --color=always "$(fzf --query)" {}'
  ```

- **Bat**:
  ```bash
  fd --type f | fzf --preview 'bat --style=numbers --color=always {}'
  ```

- **Zoxide**:
  ```bash
  z $(fd -t d | fzf)
  ```

- **Git**:
  ```bash
  alias gco='git checkout $(git branch | fzf)'
  ```

- **Advanced Niche**:
  - Kubernetes: `kubectl get pods | fzf | awk '{print $1}' | xargs kubectl exec -it -- bash`
  - Docker: `docker ps -a | fzf | awk '{print $1}' | xargs docker inspect`
  - WebAssembly: Experimental Go WASM for browser-based fzf (niche).
  - CI/CD: Use in pipelines: `fd -e yaml | fzf --filter "config" | xargs yamllint`

## Resources
- [Official Documentation](https://github.com/junegunn/fzf/blob/master/README.md)
- [GitHub Repository](https://github.com/junegunn/fzf)
- [Wiki](https://github.com/junegunn/fzf/wiki)
- Run `fzf --help` for quick reference.
- Community: r/commandline, Stack Overflow, GitHub Discussions.

## Conclusion
`fzf` is a transformative tool for interactive command-line workflows, combining fuzzy matching with powerful integrations to streamline file navigation, content searching, and scripting. This guide covers its full spectrum, from basic usage to advanced niche applications, ensuring you can leverage its capabilities in any scenario. Experiment with the examples to make `fzf` a cornerstone of your productivity!