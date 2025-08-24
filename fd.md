# `fd`

## Introduction
Fd (`fd-find` or simply `fd`) is a simple, fast, and user-friendly command-line tool for finding files and directories in the filesystem. Written in Rust, it's designed as a modern replacement for the traditional `find` command, emphasizing speed, intuitive defaults, and ease of use. As of the latest version (9.0.0, released October 2023), fd includes enhancements like improved glob support, better handling of file system boundaries, and expanded color output options.

Fd excels in scenarios where `find` is verbose or slow, such as searching large codebases, navigating project directories, or integrating with tools like `fzf` for fuzzy finding. It automatically respects `.gitignore` files, skips hidden files by default, and provides parallel processing for blazing-fast results. It's particularly valuable for developers, sysadmins, and power users who need reliable file discovery without the complexity of `find`'s syntax.

## Installation
Fd is available on Linux, macOS, and Windows. Below are common installation methods, updated for the latest practices, including containerized and reproducible options:

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian: `sudo apt install fd-find` (binary is `fdfind`; alias to `fd` in shell config)
  - Fedora: `sudo dnf install fd-find`
  - Arch Linux: `sudo pacman -S fd`
  - Nix: `nix-env -i fd`
- **From Source** (requires Rust and Cargo):
  ```bash
  cargo install fd-find
  ```
- **Binary Download**: Grab pre-built binaries from [GitHub Releases](https://github.com/sharkdp/fd/releases).
- **Docker**: For isolated environments:
  ```bash
  docker run --rm -v $(pwd):/data sharkdp/fd fd "pattern" /data
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install fd
  ```
- **MacPorts**:
  ```bash
  sudo port install fd
  ```
- **Nix**: `nix-env -i fd`

### Windows
- **Chocolatey**:
  ```bash
  choco install fd
  ```
- **Scoop**:
  ```bash
  scoop install fd
  ```
- **Winget**:
  ```bash
  winget install Sharkdp.Fd
  ```
- **Manual**: Download pre-built binaries from [GitHub Releases](https://github.com/sharkdp/fd/releases). Supports both x86_64 and ARM64.
- **WSL**: Install via Linux methods above in Windows Subsystem for Linux.

### Verify Installation
Run `fd --version` to confirm fd is installed. Example output (latest version):
```
fd (fd-find) 9.0.0
```
If on Debian/Ubuntu, ensure alias: Add `alias fd=fdfind` to `~/.bashrc` or `~/.zshrc`.

## Basic Usage
Fd searches for files and directories matching a pattern in the filesystem. The basic syntax is:
```bash
fd [OPTIONS] [PATTERN] [PATH]
```

- **PATTERN**: The glob or regex pattern to match (defaults to filename; use `--regex` for full regex).
- **PATH**: Optional starting directory (defaults to current directory).
- **OPTIONS**: Flags to modify behavior (e.g., case sensitivity, file types).

### Simple Example
Search for files named "hello" in the current directory:
```bash
fd "hello"
```
Output lists matching files with color highlighting:
```
src/hello.rs
tests/hello_test.py
```

Search in a specific directory:
```bash
fd "hello" ./src
```

Stdin example (niche for pipelines):
```bash
echo "src" | fd "hello"
```

## Key Features
Fd’s strength lies in its simplicity and performance:
- **Ignores Hidden Files and Directories**: Skips `.git`, `.DS_Store`, etc., unless specified with `-H` or `--hidden`.
- **Respects `.gitignore`**: Automatically excludes files listed in `.gitignore` and other ignore files (disable with `--no-ignore`).
- **Parallel Processing**: Uses multiple threads for fast traversal (control with `--threads`).
- **Glob Support**: Intuitive glob patterns by default; optional regex with `--regex`.
- **Color Output**: Highlights paths and matches (customize with `--color` or environment vars like `FD_COLORS`).
- **Smart Case**: Case-insensitive by default if pattern is lowercase; sensitive otherwise (control with `-i` or `-s`).
- **File Type Filtering**: Built-in support for common types like `--type file`, `--type directory`.
- **Symlink Handling**: Follows symlinks by default; control with `--follow-symlinks`.

New in 9.x: Improved glob expansion for brace patterns (e.g., `{a,b}*.txt`), better Windows path handling, and expanded `--list-types` output.

## Common Options
Here are frequently used flags (see `fd --help` for a full list, now with improved formatting):

| Option | Description | Example |
|--------|-------------|---------|
| `-i`, `--ignore-case` | Case-insensitive search | `fd -i "Hello"` |
| `-s`, `--case-sensitive` | Force case-sensitive search | `fd -s "hello"` |
| `-g`, `--glob` | Use glob pattern (default) | `fd -g "*.rs"` |
| `-r`, `--regex` | Use regular expression | `fd -r "^src/.*\.py$"` |
| `-t`, `--type` | Filter by type (file, dir, symlink, empty, socket, executable) | `fd -t file "*.txt"` |
| `-e`, `--extension` | Match by file extension | `fd -e rs` |
| `-H`, `--hidden` | Include hidden files | `fd -H ".config"` |
| `--no-ignore` | Ignore `.gitignore` rules | `fd --no-ignore "node_modules"` |
| `-d`, `--max-depth` | Limit search depth | `fd -d 2 "pattern"` |
| `-p`, `--absolute-path` | Show absolute paths | `fd -p "hello"` |
| `-0`, `--null` | NUL-terminated output | `fd -0 "hello"` |
| `--color` | Control color output (never, auto, always) | `fd --color always "pattern"` |
| `--one-file-system` | Don't cross file system boundaries | `fd --one-file-system "pattern"` |
| `-x`, `--exec` | Execute command on matches | `fd -x echo "Found: {}"` |
| `-L`, `--follow-symlinks` | Follow symlinks (default) | `fd -L "pattern"` |
| `-E`, `--exclude` | Exclude paths | `fd -E "*.log" "pattern"` |

### File Type Filtering
List supported types:
```bash
fd --list-types
```
Example: Search only Python files:
```bash
fd -e py "def"
```
Or by type:
```bash
fd -t directory "src"
fd -t executable "script"
```

## Advanced Usage
### Pattern Matching
Fd uses globs by default for simplicity:
```bash
fd "src/*.rs"  # Matches src/*.rs
fd "{src,tests}/*.py"  # Brace expansion
fd "**/node_modules" -E "node_modules"  # Recursive with exclude
```

Switch to regex for complex patterns:
```bash
fd -r "^(src|tests)/.*\.rs$"  # Regex for src or tests Rust files
fd -r "\.(jpg|png|gif)$" -e image  # But extension is simpler
```

Niche: Full path regex:
```bash
fd -P -r "^/home/user/project/.*\.txt$"  # Anchor to root
```

Fixed strings (niche for literals with special chars):
```bash
fd -tf "hello*"  # Type file, glob
```

### Ignoring and Excluding
Advanced excluding:
- Multiple: `fd -E "*.log" -E "build/" "pattern"`
- Glob exclude: `fd -E "!important.log" "logs/"` (but `!` for include in globs)
- Vcs ignore: `fd --no-ignore-vcs` (only VCS ignores)

Custom ignore file:
```bash
fd --ignore-file ~/.customignore "pattern"
```

Search everything:
```bash
fd -H --no-ignore -d 999 "pattern"  # Ultimate unrestricted mode
```

### Depth and Scope Limits
Limit recursion:
```bash
fd -d 1 "pattern"  # Only current dir
fd --min-depth 2 "pattern"  # Start from depth 2
```

File size limits (niche):
```bash
fd --size +10M "largefile"
fd --size <1K "small"
```

### Output Customization
Absolute paths:
```bash
fd -p -d 3 "src"
```

Colors and themes:
```bash
export FD_COLORS="file:fg:32;bold;underline"
fd "pattern"
```
Or `--color rgb` for truecolor support.

Strip prefixes:
```bash
fd --strip-cwd-prefix "pattern"
```

Quiet mode:
```bash
fd -q "pattern" && echo "Found matches"
```

### Executing Commands
Run on matches:
```bash
fd -t file -e py -x python {} -c "print('Running {}')"
```
Batch rename (niche):
```bash
fd -e jpg -x mv {} {}.bak
```

With xargs:
```bash
fd "*.log" -0 | xargs -0 rm
```

### Symlinks and Filesystems
Follow symlinks explicitly:
```bash
fd --follow-symlinks "pattern"
```
Don't follow:
```bash
fd -x "pattern"  # No follow for loops
```

One filesystem:
```bash
fd --one-file-system /mnt/external "pattern"
```

### Encoding and Windows Paths
Handle Unicode:
```bash
fd --encoding utf-8 "pattern"  # Default, but specify for issues
```

Windows-specific:
```bash
fd --search-path-separator ';' "C:\path;with;semicolons"
```

## Niche Usages
### Searching in Git Repos
Only tracked files:
```bash
fd "pattern" $(git ls-files)
```

Ignore VCS partially:
```bash
fd --no-ignore-vcs -H "config"
```

Pre-commit: Check for large files:
```bash
fd --size +100M $(git ls-files) && echo "Large files detected"
```

### Large Directories/Monorepos
Optimize:
```bash
fd -j8 --one-file-system --max-depth 5 "pattern"  # 8 threads
```

Interactive with fzf:
```bash
fd --type file | fzf --preview 'bat --color=always {}'
```

### Scripting and Automation
Null-terminated for xargs:
```bash
fd -0 -t file "*.txt" | xargs -0 grep "pattern"
```

Invert search (files without pattern, niche via pipe):
```bash
fd -t file | while read f; do [[ ! -f "$f" ]] || grep -q "pattern" "$f" || echo "$f"; done
```

CI/CD example (GitHub Actions):
```yaml
- name: Find and lint Python files
  run: fd -e py . -x pylint {}
```

### File Type Combinations
Executables in PATH-like:
```bash
fd -t executable -d 1 /usr/bin "ls"
```

Empty files:
```bash
fd -t empty "temp"
```

Symlinks only:
```bash
fd -t symlink "link"
```

### Custom Configurations
Environment vars:
```bash
export FD_DEFAULT_COMMAND=fd
export FD_MAX_USER_WATCHES=524288  # For inotify limits
export FD_COLORS="dir:fg:cyan;file:fg:white"
```

Config file (`~/.config/fd/fd.toml`, new in 8.x+):
```toml
[default]
search-path = ["."]
max-user-watches = 524288
color = "auto"
ignore-vcs = true
```
Per-project: Local config in `.fdignore`.

Aliases (niche):
```bash
alias f='fd -t file'
alias fdg='fd -g --glob'
alias fdr='fd -r --regex'
```

### Extensions and Companions
- **ripgrep (rg)**: Pair for content search:
  ```bash
  fd -0 . | xargs -0 rg "pattern"
  ```
- **bat**: Preview with syntax:
  ```bash
  fd --type file | fzf --preview 'bat --color=always {}'
  ```
- **ripgrep-all (rga)**: For non-text, but fd for finding first.
- **exa/ls**: List details: `fd -x ls -l {}`
- **zoxide**: Smart directory jumping based on fd results.

### Edge Cases
- Search for dotfiles: `fd -H "\..*"`
- Max results: No built-in, but pipe: `fd | head -n 10`
- Debug: `fd --log debug "pattern" > fd.log`
- No colors in scripts: `fd --color never`
- Brace expansion limits: `fd "{1..100}.txt"` (expands to 100 patterns)

## Practical Examples
1. **Find Python Files**:
   ```bash
   fd -e py "class"
   ```

2. **Directories Named Src**:
   ```bash
   fd -t directory src --max-depth 2
   ```

3. **Hidden Configs**:
   ```bash
   fd -H ".env" --absolute-path
   ```

4. **Executables in Project**:
   ```bash
   fd -t executable -x chmod +x {}
   ```

5. **Regex for Logs**:
   ```bash
   fd -r "error.*\d{4}-\d{2}-\d{2}" -e log
   ```

6. **Large Files**:
   ```bash
   fd --size +50M --one-file-system .
   ```

7. **Exclude Build and Include Hidden**:
   ```bash
   fd -H -E "build/" -E "*.tmp" "source"
   ```

8. **Batch Process Images**:
   ```bash
   fd -e jpg -x convert {} -resize 800x600 {}.thumb.jpg
   ```

9. **Interactive Fuzzy Find**:
   ```bash
   fd --type file --hidden --exclude .git | fzf
   ```

10. **Count Matches**:
    ```bash
    fd "pattern" | wc -l
    ```

11. **Symlinks to Binaries**:
    ```bash
    fd -t symlink /usr/local/bin
    ```

12. **Min Depth Search**:
    ```bash
    fd --min-depth 3 -t directory "tests"
    ```

## Performance Tips
- **Narrow Scope**: Use `-d`, `--type`, `-e` to limit traversal.
- **Threads**: `-j $(nproc)` for multi-core speedup.
- **Ignore Files**: Leverage `.gitignore` for large repos.
- **Absolute Paths**: Avoid with `-p` unless needed (slower printing).
- **Globs vs Regex**: Use globs for simple patterns (faster).
- **Benchmark**: Time with `time fd "pattern"`.
- **Monorepo**: Split: `fd "pattern" src/ vendor/`.
- **Inotify Limits**: Increase `FD_MAX_USER_WATCHES` for watchers.
- **Windows**: Use `--search-path-separator ';'` for paths.

Niche: For massive FS, combine with `locate` for initial filter, then fd.

## Troubleshooting
- **No Matches**: Check `--hidden`, `--no-ignore`; verify pattern case with `-i`.
- **Slow Searches**: Reduce depth, increase threads, check FS (NFS slower).
- **Permissions**: `sudo fd` or fix ownership; note sudo may break ignores.
- **Symlink Loops**: Use `--no-symlink` if detected.
- **Colors Not Showing**: Set `--color always` or check `TERM`.
- **Windows Paths**: Use forward slashes or escape.
- **Alias Issues (Debian)**: Ensure `alias fd=fdfind` in shell.
- **Memory**: Rare, but limit threads on low-RAM.

Niche: Debug with `--log trace`; check `fd --print-diagnostics`.

## Integration with Other Tools
- **Editors**:
  - Vim/Neovim:
    ```vim
    :set grepprg=fd\ --color=never\ --hidden\ --type=file
    :grep "pattern"
    ```
  - Emacs: Use `fd` in `counsel-find-file` or `consult`.
  - VS Code: Extension "fd Finder" or tasks: `"find": "fd -e ts src/"`.

- **FZF** (Highly Recommended):
  ```bash
  export FZF_DEFAULT_COMMAND='fd --type f --hidden --follow --exclude .git .'
  export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"
  fzf
  ```
  Advanced preview:
  ```bash
  fd --type f . | fzf --preview 'bat --style=numbers --color=always --line-range :500 {}'
  ```

- **Ripgrep (rg)**:
  ```bash
  fd -0 --type f | xargs -0 rg --color=always "pattern" | less -R
  ```

- **Shell**:
  - Zsh: Completions via `fd --generate fish-completions > ~/.config/fish/completions/fd.fish`
  - Bash: `fd --generate bash-completions > /usr/local/etc/bash_completion.d/fd.bash`

- **Bat**:
  ```bash
  fd --type f | fzf --preview 'bat --color=always {}'
  ```

- **Zoxide**: Jump to frequent dirs:
  ```bash
  z foo  # Uses fd under the hood for indexing
  ```

- **CI/CD**: In Docker: `fd -e yaml . -x yamllint {}`

- **Advanced Niche**:
  - Kubernetes: `kubectl exec pod -- fd "config" /app`
  - WebAssembly: Compile to WASM for browser FS search (experimental).
  - Pair with `parallel`: `fd -0 . | parallel -0 process_file {}`

## Resources
- [Official Documentation](https://github.com/sharkdp/fd/blob/master/doc/fd.md)
- [GitHub Repository](https://github.com/sharkdp/fd)
- [Blog Post](https://github.com/sharkdp/fd#fd-a-simple-fast-and-user-friendly-alternative-to-find)
- Run `fd --help` for quick reference; `fd --generate man` for manpage.
- Community: Discussions on GitHub, r/rust, Stack Overflow.

## Conclusion
Fd is an essential tool for efficient file discovery, combining the power of `find` with modern usability and speed. This comprehensive guide covers everything from basics to advanced integrations and niche scenarios, empowering you to streamline your workflow. Whether in scripts, editors, or daily navigation, fd will become indispensable—experiment with the examples to see its full potential!
