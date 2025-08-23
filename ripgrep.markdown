# `ripgrep`

## Introduction
Ripgrep (`rg`) is a command-line search tool designed for speed and usability, built in Rust. It’s a modern alternative to `grep`, offering features like respecting `.gitignore`, fast parallel searching, robust regex support, and advanced capabilities for handling large codebases, compressed files, and custom configurations. As of the latest version (14.1.1, released September 2023), ripgrep includes enhancements like hyperlink formatting for file paths, improved performance for complex regexes, and better integration with tools like Git and shells.

Ripgrep excels in scenarios where traditional `grep` falls short, such as multithreaded searches, automatic binary file skipping, and intelligent ignoring of version control directories. It's particularly useful for developers dealing with monorepos, sysadmins parsing logs, researchers sifting through large datasets, and even in CI/CD pipelines for automated checks.

## Installation
Ripgrep is available on Linux, macOS, and Windows. Below are common installation methods, updated for the latest practices, including containerized options for reproducibility:

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian: `sudo apt install ripgrep`
  - Fedora: `sudo dnf install ripgrep`
  - Arch Linux: `sudo pacman -S ripgrep`
  - Nix: `nix-env -i ripgrep`
- **From Source** (requires Rust and Cargo):
  ```bash
  cargo install ripgrep
  ```
- **Binary Download**: Grab pre-built binaries from [GitHub Releases](https://github.com/BurntSushi/ripgrep/releases).
- **Docker**: For isolated environments:
  ```bash
  docker run --rm -v $(pwd):/data burnt/ripgrep rg "pattern" /data
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install ripgrep
  ```
- **MacPorts**:
  ```bash
  sudo port install ripgrep
  ```
- **Nix**: `nix-env -i ripgrep`

### Windows
- **Chocolatey**:
  ```bash
  choco install ripgrep
  ```
- **Scoop**:
  ```bash
  scoop install ripgrep
  ```
- **Winget**:
  ```bash
  winget install BurntSushi.ripgrep.MSVC
  ```
- **Manual**: Download pre-built binaries from [GitHub Releases](https://github.com/BurntSushi/ripgrep/releases). For ARM support, use `armv7-unknown-linux-gnueabihf` binaries.
- **WSL**: Install via Linux methods above in Windows Subsystem for Linux.

### Verify Installation
Run `rg --version` to confirm ripgrep is installed. Example output (latest version):
```
ripgrep 14.1.1
-SIMD -AVX (compiled)
+SIMD +AVX (runtime)
+PCRE2 (compiled)
```
The output includes PCRE2 availability and SIMD details for performance insights. If missing features, recompile with `cargo install ripgrep --features pcre2`.

## Basic Usage
Ripgrep searches for patterns in files or directories. The basic syntax is:
```bash
rg [OPTIONS] PATTERN [PATH]
```

- **PATTERN**: The text or regex to search for (defaults to Rust's regex engine; use `-P` for PCRE2).
- **PATH**: Optional directory or file to search (defaults to current directory; use `-` for stdin).
- **OPTIONS**: Flags to modify behavior (e.g., case sensitivity, output format).

### Simple Example
Search for "hello" in all files in the current directory:
```bash
rg "hello"
```
Output shows file paths, line numbers, and matching lines with color highlighting:
```
src/main.rs:10:    println!("hello, world!");
```

Search in a specific directory, following symlinks:
```bash
rg "hello" ./src --follow
```

Stdin example (niche for pipelines):
```bash
echo "hello world" | rg "hello"
```

## Key Features
Ripgrep’s power comes from its speed and intelligent defaults:
- **Ignores Hidden Files and Directories**: Skips `.git`, `node_modules`, etc., unless specified with `--hidden`.
- **Respects `.gitignore`**: Automatically excludes files listed in `.gitignore`, `.ignore`, `.rgignore`, and other ignore files (disable with `--no-ignore`).
- **Multithreaded**: Searches files in parallel using multiple CPU cores (control with `-j/--threads`).
- **Regex Support**: Uses Rust’s regex engine by default; optional PCRE2 for advanced Unicode, lookarounds, and backreferences.
- **Color Output**: Highlights matches in terminal output (disable with `--no-color`; customize with `--colors` for specific styles like `match:fg:red`).
- **Binary Detection**: Skips binary files automatically; force search with `--text` or detect with `--binary`.
- **Hyperlink Support**: Outputs file paths as clickable hyperlinks in terminals (e.g., `--hyperlink-format=default`).
- **Performance Optimizations**: Uses SIMD/AVX for faster searching; tunable DFA size with `--dfa-size-limit`; literal optimizations for simple patterns.

New in 14.x: Faster inner literal optimizations, reduced memory usage, better handling of lookarounds, and improved error messages for regex compilation.

## Common Options
Here are frequently used flags (see `rg --help` for a full list, categorized in output for easier navigation):

| Option | Description | Example |
|--------|-------------|---------|
| `-i`, `--ignore-case` | Case-insensitive search | `rg -i "hello"` |
| `-s`, `--case-sensitive` | Force case-sensitive search (overrides smart case) | `rg -s "Hello"` |
| `-S`, `--smart-case` | Case-insensitive if pattern is lowercase, else sensitive (default) | `rg "hello"` |
| `-w`, `--word-regexp` | Match whole words only | `rg -w "hello"` |
| `-x`, `--line-regexp` | Match entire lines | `rg -x "hello.*world"` |
| `-l`, `--files-with-matches` | List files containing matches | `rg -l "hello"` |
| `-L`, `--files-without-match` | List files without matches | `rg -L "error"` |
| `-c`, `--count` | Count matches per file | `rg -c "hello"` |
| `-g`, `--glob` | Include/exclude files by pattern | `rg "hello" -g "*.rs"` |
| `--iglob` | Case-insensitive glob | `rg "hello" --iglob "*.rs"` |
| `--type` | Search specific file types | `rg "hello" --type py` |
| `-T`, `--type-not` | Exclude file types | `rg "hello" -T py` |
| `-r`, `--replace` | Replace matches (output only) | `rg "hello" -r "hi"` |
| `-A`, `-B`, `-C` | Show lines after/before/context | `rg "hello" -C 2` |
| `--no-ignore` | Ignore `.gitignore` rules | `rg "hello" --no-ignore` |
| `--hidden` | Search hidden files | `rg "hello" --hidden` |
| `-u`, `--unrestricted` | Reduce ignoring (combine for -uu, -uuu) | `rg -uuu "hello"` (searches everything) |
| `-v`, `--invert-match` | Invert search (show non-matching lines) | `rg -v "hello"` |
| `--text` | Search binary files as text | `rg --text "magic"` |
| `--binary` | Search binaries but note them | `rg --binary "pattern"` |

### File Type Filtering
List supported file types (expanded in 14.x with types like Ada, GraphQL, TypeScript):
```bash
rg --type-list
```
Add custom types globally in `~/.ripgreprc` or per-command:
```bash
rg --type-add 'web:*.{html,css,js}' "pattern" --type web
```
Clear and redefine:
```bash
rg --type-clear py --type-add 'py:*.py' "def"
```
Niche: Use `--type-add` for project-specific types in scripts.

## Advanced Usage
### Regex Patterns
Ripgrep supports regular expressions for complex searches. Enable PCRE2 for advanced features:
```bash
rg -P "\b[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}\b"  # Email with PCRE2
```

Multiline regex with `-U/--multiline` (searches across lines):
```bash
rg -U '(?s)start.*end'  # Dot matches newlines with PCRE2 (?s)
```

Niche lookarounds and backreferences:
```bash
rg -P '(?<!prefix)pattern(?!suffix)'  # Negative lookaround
rg -P '(\w+)\s+\1'  # Backreference to capture group
```

Byte/column offsets:
```bash
rg --byte-offset --column "pattern"
```

Fixed strings for literal search:
```bash
rg -F "literal*string"  # No regex interpretation
```

### Globbing and Ignoring
Advanced globbing:
- Include subdirs: `rg "hello" -g '**/src/*.rs'`
- Exclude: `rg "hello" -g '!{*.log,*.tmp}'`
- Case-insensitive: `--iglob '*.txt'`

Custom ignore files:
- Use `.rgignore` for ripgrep-specific ignores.

Pre-globbing with `--pre` for preprocessing (e.g., decompress):
```bash
rg --pre gunzip "pattern" -g '*.gz' --pre-glob '*.gz'
```

Search everything:
```bash
rg -uuu --hidden --no-ignore --follow --text "pattern"  # Ultimate unrestricted mode
```

### Context Lines
Partial override:
```bash
rg -C1 -A2 "pattern"  # 1 before, 2 after
```

Niche: `--only-matching` with context for focused output.

### Replacing Text
Advanced replacement with captures:
```bash
rg '(\w+)=(\d+)' -r '$1 is $2'
```

In-place edits via piping (niche script):
```bash
rg -l "hello" | xargs -d '\n' perl -i -pe 's/hello/hi/g'
```

### Searching Compressed Files
Search inside archives:
```bash
rg --search-zip "error" logs.tar.gz
```

Extend with preprocessing for other formats:
```bash
rg --pre 'xz -d' "pattern" -g '*.xz' --pre-glob '*.xz'
```

Niche: Chain with `ripgrep-all` (rga) for PDFs, Office docs:
```bash
rga "pattern" docs/  # Separate tool, install via cargo
```

### Recursive vs. Non-Recursive
Limit depth/filesize:
```bash
rg "hello" --max-depth 3 --max-filesize 10M
```

One filesystem:
```bash
rg --one-file-system "pattern"
```

### Encoding and Line Endings
Handle non-UTF8:
```bash
rg --encoding shift-jis "pattern"
```

CRLF:
```bash
rg --crlf "pattern"
```

Niche: `--encoding none` for raw byte searches.

### Output Formats
JSON (for scripting):
```bash
rg --json "pattern" | jq '.data[] | .submatches[].match.text'
```

Vimgrep:
```bash
rg --vimgrep "pattern"
```

Pretty/hyperlink:
```bash
rg --pretty --hyperlink-format 'vscode://{path}:{line}' "pattern"
```

Stats:
```bash
rg --stats "pattern"  # Elapsed time, matches, etc.
```

Other niche: `--null` for NUL-terminated output, `--sort none` for unsorted (faster), `--trim` to strip prefixes.

Passthru:
```bash
rg --passthru "pattern" > modified.txt
```

### Performance Tuning
Threads:
```bash
rg -j$(nproc) "pattern"
```

Memory:
```bash
rg --mmap "pattern"  # For local disks
rg --no-mmap "pattern"  # For NFS
```

DFA/regex limits:
```bash
rg --dfa-size-limit 100M --regex-size-limit 50M "complex"
```

Engine:
```bash
rg --engine pcre2 "pattern"
```

Buffering/quiet:
```bash
rg --block-buffered --quiet "pattern" && echo "Found"
```

Niche: `--no-require-git` to use .gitignore outside repos.

## Niche Usages
### Searching in Git Repos
Only tracked files:
```bash
rg "pattern" $(git ls-files)
```

Git-specific:
```bash
rg --no-ignore-vcs "pattern"  # Only VCS ignores
```

Pre-commit hook example:
```bash
#!/bin/sh
rg "TODO|FIXME" $(git diff --cached --name-only) && exit 1
```

### Large Codebases/Monorepos
Optimize:
```bash
rg --one-file-system --max-filesize 50M -j$(nproc) --no-messages "pattern"
```

Interactive with fzf:
```bash
rg --files | fzf --preview 'rg --context 5 --color=always {q} {} | bat --color=always'
```

### Scripting and Automation
Null-terminated:
```bash
rg -0 -l "pattern" | xargs -0 myscript.sh
```

Invert count:
```bash
rg -v -c "pattern"
```

CI/CD example (GitHub Actions):
```yaml
- name: Check for secrets
  run: rg "AKIA[0-9A-Z]{16}" . || true
```

### Binary and Text Mixing
Hex dump style:
```bash
rg --text -a "pattern" binary.file
```

### Multiline and Dotall
PCRE2 multiline:
```bash
rg -PU '(?s)multi.line.pattern'
```

Dot matches all without PCRE2:
```bash
rg -U 'start[\s\S]*end'
```

### Custom Configuration
Environment config:
```bash
export RIPGREP_CONFIG_PATH=~/.ripgreprc
```
Sample `~/.ripgreprc`:
```
--smart-case
--follow
--hidden
--type-add web:*.{html,css,js,ts}
--colors match:fg:yellow
--colors path:fg:blue
```

Per-project: Use local `.ripgreprc` or env vars in scripts.

Aliases (niche):
```bash
alias rgf='rg --fixed-strings --ignore-case'
alias rgg='rg --glob "**/*.go" --type-add go:*.go'
```

### Extensions and Companions
- **ripgrep-all (rga)**: Search non-text files like PDFs, SQLite:
  ```bash
  cargo install ripgrep_all
  rga "pattern" archive.zip
  ```
- **fd**: Pair with fd-find for faster file listing:
  ```bash
  fd . | rg --files-with-matches "pattern"
  ```
- **delta**: Syntax-highlighted diffs with replacements:
  ```bash
  rg -r "new" "old" --passthru | delta
  ```

### Edge Cases
- Search for NUL bytes: `--null-data`
- Stop early: `--stop-on-nonmatch`
- No headings: `--no-heading`
- Max count per file: `--max-count 5`

## Practical Examples
1. **Find Function Definitions in Python**:
   ```bash
   rg "^def\s+\w+" --type py --stats
   ```

2. **Count Occurrences**:
   ```bash
   rg "TODO" -c --json | jq 'sum(.stats.matches)'
   ```

3. **Search Logs Excluding Old**:
   ```bash
   rg "error" -g "*.log" -g "!old*.log" --hyperlink-format=default
   ```

4. **List JS Imports**:
   ```bash
   rg -l "import" --type js --sort path
   ```

5. **Warning with Context**:
   ```bash
   rg -i "warning" -C 3 logs/ --pretty
   ```

6. **Hex in Binaries**:
   ```bash
   rg --text "0x[0-9a-fA-F]+" binaries/ --byte-offset
   ```

7. **Multiline Config**:
   ```bash
   rg -U '(?P<header>\[section\])\nkey=value' -P config.ini
   ```

8. **Replace Captures**:
   ```bash
   rg 'version: (\d+)' -r 'version: $1.0' --vimgrep
   ```

9. **Interactive Preview**:
   ```bash
   rg --files-with-matches "pattern" | fzf --preview 'bat --color=always {}'
   ```

10. **Benchmark**:
    ```bash
    time rg --stats --dfa-size-limit 50M "complex_pattern" /large/dir
    ```

11. **Search Stdin with Replace**:
    ```bash
    cat file.txt | rg "old" -r "new" --passthru
    ```

12. **Exclude VCS but Include Hidden**:
    ```bash
    rg --no-ignore-vcs --hidden "pattern"
    ```

## Performance Tips
- **Narrow Scope**: Use `--type`, `-g`, `--max-depth`.
- **Scripting**: `--no-color`, `--block-buffered`.
- **Tune Hardware**: `-j` for cores, `--mmap` for SSDs.
- **Avoid Overkill**: Simple patterns use literals automatically.
- **Benchmark**: Use `--stats` and `time` to iterate.
- **Monorepo Hack**: Split searches: `rg "pattern" src/ tests/`.
- **ARM Optimizations**: Use native binaries; test SIMD flags.

Niche: For hyperscale, consider indexing tools like `codesearch` but rg is often sufficient.

## Troubleshooting
- **No Matches**: `--no-ignore`, verify regex with `rg -e 'pattern' --debug`.
- **Slow**: Limit scope, increase `--dfa-size-limit`, disable mmap.
- **Permissions**: `sudo rg` or fix ownership.
- **Binaries**: `--text` or `--binary`.
- **Regex Errors**: `-P` for PCRE2 compatibility.
- **Hyperlinks**: Match format to terminal (e.g., 'iterm' for iTerm).
- **Memory**: Reduce threads or DFA limits on low-RAM systems.
- **OS-Specific**: On Windows, use `--crlf`; on macOS, check Homebrew updates.

Niche: Debug with `--debug` or `--trace` for detailed logs.

## Integration with Other Tools
- **Editors**:
  - Vim/Neovim:
    ```vim
    set grepprg=rg\ --vimgrep\ --smart-case\ --follow
    set grepformat=%f:%l:%c:%m
    ```
  - Emacs: Use `rg.el` package for interactive searches.
  - VS Code: Built-in; customize with `"search.ripgrepArgs": ["--hidden"]`.

- **FZF**:
  ```bash
  rg --column --line-number --no-heading --color=always --smart-case "$1" | fzf -m --ansi --preview="echo {} | cut -d: -f1 | xargs bat --color=always"
  ```

- **Less/Pager**:
  ```bash
  rg "pattern" | less -R
  ```

- **Git**:
  Alias: `alias git-grep='rg --no-ignore-vcs'`

- **Shell Completions**: Generate: `rg --generate complete-zsh > ~/.zsh/completions/_rg`

- **Bat**:
  ```bash
  rg "pattern" --passthru | bat -l log
  ```

- **Delta for Diffs**:
  ```bash
  rg -r "replacement" "pattern" --passthru | delta
  ```

- **CI/CD**: In Jenkins/Docker: Mount volumes and run rg for linting.

- **Advanced Niche**:
  - WebAssembly: Compile rg to WASM for browser-based searches (experimental via `wasm-pack`).
  - Kubernetes: Run in pods for log searching: `kubectl exec pod -- rg "error" /var/log`.
  - Pyodide: Integrate in Python web envs for client-side.

## Resources
- [Official Guide](https://github.com/BurntSushi/ripgrep/blob/master/GUIDE.md)
- [GitHub](https://github.com/BurntSushi/ripgrep)
- [Blog](https://blog.burntsushi.net/ripgrep/)
- `rg --help`, `rg --generate man` for manpage.
- Community: Reddit r/commandline, Stack Overflow tags.
- Extensions: ripgrep-all, xsv for CSV searching.
