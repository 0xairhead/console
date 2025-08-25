# `find`

## Introduction
`find` is a powerful command-line utility in Unix-like systems for searching files and directories based on various criteria such as name, type, size, permissions, and modification time. Part of the GNU coreutils (or native on BSD systems), it’s a foundational tool for file system navigation and manipulation. As of the latest GNU coreutils version (9.5, released March 2024), `find` includes improved performance for large directories, enhanced regex support, and better error handling. It’s ideal for sysadmins, developers, and power users who need precise control over file searches, especially in scripts or automation workflows.

`find` excels in scenarios requiring complex file searches, such as locating files by metadata, batch processing, or integrating with other tools like `grep`, `fzf`, or `xargs`. While modern alternatives like `fd` offer simpler syntax, `find` remains unmatched for its flexibility and depth.

## Installation
`find` is typically pre-installed on Unix-like systems (Linux, macOS, BSD). For specific cases or updates, here are installation methods:

### Linux
- **Using Package Managers** (updates GNU coreutils):
  - Ubuntu/Debian: `sudo apt install coreutils`
  - Fedora: `sudo dnf install coreutils`
  - Arch Linux: `sudo pacman -S coreutils`
  - Nix: `nix-env -i coreutils`
- **From Source** (requires `gcc`, `make`):
  ```bash
  git clone https://git.savannah.gnu.org/git/coreutils.git
  cd coreutils
  ./configure
  make
  sudo make install
  ```
- **Docker**:
  ```bash
  docker run --rm -v $(pwd):/data -it alpine sh -c 'apk add findutils && find /data'
  ```

### macOS
- **Pre-installed**: macOS includes BSD `find` (slightly different syntax).
- **Homebrew** (for GNU `find`):
  ```bash
  brew install coreutils
  ```
  Note: GNU `find` is prefixed as `gfind`; add to PATH or alias: `alias find=gfind`.
- **MacPorts**:
  ```bash
  sudo port install coreutils
  ```
- **Nix**: `nix-env -i coreutils`

### Windows
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **MSYS2**:
  ```bash
  pacman -S findutils
  ```
- **Cygwin**: Install `findutils` package.
- **Scoop** (for GNU coreutils):
  ```bash
  scoop install coreutils
  ```

### Dependencies
- None for pre-installed versions.
- **Git**, `gcc`, `make` for source builds.
- **bash`/`zsh`/`fish` for scripting.

### Verify Installation
Run `find --version` to confirm installation. Example output (GNU):
```
find (GNU findutils) 4.9.0
```
On macOS (BSD), use `man find` to verify features.

## Basic Usage
`find` searches the file system starting from a specified directory, applying tests and actions to matching files. The basic syntax is:
```bash
find [PATH...] [EXPRESSION]
```

- **PATH**: Starting directory (default: current directory, `.`).
- **EXPRESSION**: Tests (e.g., `-name`, `-type`) and actions (e.g., `-exec`, `-delete`).
- **Default Behavior**: Lists all files recursively in PATH.

### Simple Example
Find files named `*.txt` in the current directory:
```bash
find . -name "*.txt"
```
Output: Paths like `./docs/note.txt`.

Find directories in `/home`:
```bash
find /home -type d
```

## Key Features
`find`’s power lies in its flexibility:
- **Flexible Criteria**: Search by name, type, size, permissions, time, and more.
- **Recursive Search**: Traverses directories deeply by default.
- **Actions**: Execute commands, delete files, or print custom formats.
- **Regex Support**: Match patterns with `-regex` or `-name`.
- **Permission Handling**: Search regardless of access (with warnings).
- **Cross-Platform**: GNU `find` (Linux) and BSD `find` (macOS) share core functionality.

New in GNU findutils 4.9.0: Faster directory scanning, `-ipath` for case-insensitive paths, and improved `-execdir` security.

## Common Expressions
Frequently used tests and actions (see `man find` for a full list):

| Expression | Description | Example |
|------------|-------------|---------|
| `-name` | Match filename (case-sensitive) | `find . -name "*.py"` |
| `-iname` | Match filename (case-insensitive) | `find . -iname "*.py"` |
| `-type` | Match file type (f, d, l, etc.) | `find . -type f` |
| `-size` | Match file size | `find . -size +10M` |
| `-mtime` | Match modification time (days) | `find . -mtime -7` |
| `-perm` | Match permissions | `find . -perm 755` |
| `-user` | Match owner | `find . -user alice` |
| `-group` | Match group | `find . -group dev` |
| `-regex` | Match path with regex | `find . -regex ".*\.py$"` |
| `-exec` | Execute command on matches | `find . -exec chmod 644 {} \;` |
| `-delete` | Delete matching files | `find . -name "*.tmp" -delete` |
| `-print` | Print matching paths (default) | `find . -print` |
| `-ls` | List in `ls -l` format | `find . -ls` |

### Operators
- `-and` (default): Combine conditions (`find . -name "*.py" -type f`).
- `-or`: Match either condition (`find . -name "*.py" -or -name "*.sh"`).
- `-not`: Negate condition (`find . -not -name "*.bak"`).
- `()`: Group conditions (`find . \( -name "*.py" -or -name "*.sh" \) -type f`).

## Advanced Usage
### Pattern Matching
Case-insensitive names:
```bash
find . -iname "*.txt"
```

Regex for paths:
```bash
find . -regex ".*\/src\/.*\.py$"
```

Path-based (GNU-specific):
```bash
find . -ipath "*src*.py"
```

### File Types
Search for specific types:
```bash
find . -type f  # Files
find . -type d  # Directories
find . -type l  # Symlinks
```

Empty files or dirs:
```bash
find . -empty
```

### Size and Time
Size-based:
```bash
find . -size +100M  # Larger than 100MB
find . -size -1k    # Smaller than 1KB
```

Time-based:
```bash
find . -mtime -7    # Modified in last 7 days
find . -atime +30   # Accessed over 30 days ago
find . -mmin -60    # Modified in last 60 minutes (GNU)
```

### Permissions and Ownership
Find executable files:
```bash
find /usr/bin -perm /111
```

Find files owned by user:
```bash
find /home -user alice
```

### Actions
Execute command:
```bash
find . -name "*.bak" -exec rm {} \;
```

Safer execution in dir (GNU):
```bash
find . -name "*.bak" -execdir rm {} \;
```

Batch execution with `xargs`:
```bash
find . -name "*.txt" | xargs grep "error"
```

Custom print format:
```bash
find . -printf "%p %s\n"  # Path and size
```

### Pruning and Excluding
Skip directories:
```bash
find . -path "./node_modules" -prune -or -print
```

Multiple excludes:
```bash
find . -not \( -path "./node_modules" -or -path "./.git" \) -type f
```

### Depth Control
Limit depth:
```bash
find . -maxdepth 2 -name "*.py"
```

Minimum depth:
```bash
find . -mindepth 2 -type d
```

### Regex and Globs
Extended regex (GNU):
```bash
find . -regextype posix-extended -regex ".*\.(py|sh)$"
```

Glob with wildcards:
```bash
find . -name "[a-z]*.txt"
```

### Scripting
Batch delete old files:
```bash
find /tmp -mtime +7 -type f -delete
```

Move files:
```bash
find . -name "*.log" -exec mv {} /archive/ \;
```

With `fzf`:
```bash
find . -type f | fzf | xargs -I {} cat {}
```

## Niche Usages
### File System Analysis
Find largest files:
```bash
find / -type f -size +1G -printf "%s %p\n" | sort -nr | head
```

Broken symlinks:
```bash
find . -type l -xtype l
```

### Security Auditing
SUID/SGID files:
```bash
find / -perm /6000 -type f
```

World-writable files:
```bash
find / -perm -o+w
```

### Automation
Cron job to clean temp files:
```bash
find /tmp -type f -mtime +30 -delete
```

Backup modified files:
```bash
find . -mtime -1 -type f -exec tar -rf backup.tar {} \;
```

### With Other Tools
With `fzf`:
```bash
find . -type f | fzf --preview 'bat --color=always {}' | xargs vim
```

With `rg`:
```bash
find . -name "*.py" | xargs rg "def"
```

With `xargs`:
```bash
find . -name "*.bak" -print0 | xargs -0 rm
```

### CI/CD Integration
GitHub Actions:
```yaml
- name: Find modified files
  run: find . -mtime -1 -type f | xargs git add
```

### Edge Cases
- Null-terminated output: `-print0` for filenames with spaces.
- Handle spaces: `find . -name "* *" -exec mv {} {}.renamed \;`.
- Debug: `find . -name "*.py" -ls 2> find.log`.
- Cross-filesystem: `-xdev` to stay on one filesystem.
- Custom regex type: `-regextype egrep` (GNU).

## Practical Examples
1. **Find Python Files**:
   ```bash
   find . -name "*.py"
   ```

2. **Delete Old Logs**:
   ```bash
   find /logs -name "*.log" -mtime +30 -delete
   ```

3. **List Large Files**:
   ```bash
   find . -type f -size +100M
   ```

4. **Find Recent Files**:
   ```bash
   find . -mtime -1 -ls
   ```

5. **Executable Files**:
   ```bash
   find /usr/bin -perm /111
   ```

6. **Empty Directories**:
   ```bash
   find . -type d -empty
   ```

7. **With Fzf**:
   ```bash
   find . -type f | fzf | xargs cat
   ```

8. **Batch Rename**:
   ```bash
   find . -name "*.txt" -exec mv {} {}.bak \;
   ```

9. **Exclude Git**:
   ```bash
   find . -path "./.git" -prune -or -type f
   ```

10. **Find by User**:
    ```bash
    find /home -user alice -type f
    ```

11. **Custom Format**:
    ```bash
    find . -printf "%p %TY-%Tm-%Td\n"
    ```

12. **Security Audit**:
    ```bash
    find / -perm -o+w -type f
    ```

## Performance Tips
- **Limit Depth**: Use `-maxdepth` to reduce traversal.
- **Prune Dirs**: Use `-prune` for `node_modules`, `.git`.
- **Null Output**: Use `-print0` with `xargs -0` for speed.
- **Type Filter**: Use `-type f` to skip directories.
- **Filesystem**: Use `-xdev` for single filesystem.
- **Avoid Regex**: Prefer `-name` over `-regex` for speed.
- **Parallel**: Combine with `parallel` for large tasks:
  ```bash
  find . -type f | parallel grep "pattern"
  ```

## Troubleshooting
- **No Matches**: Check `-name` case sensitivity; use `-iname`.
- **Permission Denied**: Use `sudo` or redirect errors: `find / 2>/dev/null`.
- **Slow Searches**: Limit with `-maxdepth`, `-type`, or `-prune`.
- **Symlink Loops**: Use `-follow` or `-noleaf` (BSD).
- **Spaces in Names**: Use `-print0` or quote paths.
- **BSD vs GNU**: Check `man find` for syntax differences.

Niche: Debug with `strace find . -name "*.txt"`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: No completions needed; alias: `alias f='find . -type f'`.
  - Fish: `function f; find . -type f $argv; end`.

- **Fzf**:
  ```bash
  find . -type f | fzf --preview 'bat --color=always {}'
  ```

- **Ripgrep**:
  ```bash
  find . -name "*.py" | xargs rg "def"
  ```

- **Fd**:
  ```bash
  fd . | xargs find {} -type f
  ```

- **Git**:
  ```bash
  find . -type f -not -path "./.git/*" | xargs git add
  ```

- **Editors**:
  - Vim: `find . -name "*.py" | xargs vim`.
  - VS Code: Task: `"command": "find . -name '*.js' | xargs code"`.
  - Emacs: `find-dired` for interactive `find`.

- **Advanced Niche**:
  - Kubernetes: `find /var/log -name "*.log" | xargs kubectl cp`.
  - Docker: `docker run -v $(pwd):/data alpine find /data -name "*.txt"`.
  - CI/CD: `find . -mtime -1 | xargs git diff`.

## Resources
- [GNU Findutils](https://www.gnu.org/software/findutils/)
- [Man Page](https://man7.org/linux/man-pages/man1/find.1.html)
- [BSD Find](https://www.freebsd.org/cgi/man.cgi?query=find)
- Run `man find` or `find --help` (GNU) for reference.
- Community: Stack Overflow, r/commandline.

## Conclusion
`find` is a versatile, powerful tool for file system searches, offering unmatched flexibility for complex queries and automation. This guide covers its full range, from basic searches to advanced scripting and integrations, empowering you to master file discovery. Experiment with the examples to integrate `find` into your workflows!