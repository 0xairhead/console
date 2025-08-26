# `jq`

## Introduction
`jq` is a lightweight, command-line JSON processor for parsing, filtering, and transforming JSON data. Written in C, it’s designed for speed and flexibility, enabling complex JSON manipulations using a functional, query-like syntax. As of the latest version (1.7, released December 2022), `jq` includes features like improved regex support, enhanced error handling, and new built-in functions for advanced transformations. It’s ideal for developers, data analysts, sysadmins, and DevOps professionals who need to process JSON in scripts, pipelines, or interactive shells.

`jq` excels in scenarios requiring JSON data extraction, transformation, or validation, such as API response parsing, log analysis, or configuration manipulation. It integrates seamlessly with tools like `curl`, `fzf`, `rg`, and `xargs`, making it a cornerstone for JSON-centric workflows.

## Installation
`jq` is available on Linux, macOS, Windows, and various Unix-like systems. Below are common installation methods, updated for modern practices, including containerized options.

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian:
    ```bash
    sudo apt update
    sudo apt install jq
    ```
  - Fedora:
    ```bash
    sudo dnf install jq
    ```
  - Arch Linux:
    ```bash
    sudo pacman -S jq
    ```
  - Nix:
    ```bash
    nix-env -i jq
    ```
- **Binary Download**:
  ```bash
  VER="1.7"
  wget https://github.com/jqlang/jq/releases/download/jq-${VER}/jq-linux-amd64
  chmod +x jq-linux-amd64
  sudo mv jq-linux-amd64 /usr/local/bin/jq
  ```
  For ARM: Use `jq-linux-arm64`.
- **From Source** (requires `gcc`, `make`, `bison`, `flex`):
  ```bash
  git clone https://github.com/jqlang/jq.git
  cd jq
  autoreconf -i
  ./configure
  make
  sudo make install
  ```
- **Docker**:
  ```bash
  docker run --rm -i ghcr.io/jqlang/jq jq --version
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install jq
  ```
- **MacPorts**:
  ```bash
  sudo port install jq
  ```
- **Nix**:
  ```bash
  nix-env -i jq
  ```
- **Binary Download**:
  ```bash
  VER="1.7"
  curl -LO https://github.com/jqlang/jq/releases/download/jq-${VER}/jq-macos-amd64
  chmod +x jq-macos-amd64
  sudo mv jq-macos-amd64 /usr/local/bin/jq
  ```

### Windows
- **Scoop**:
  ```bash
  scoop install jq
  ```
- **Winget**:
  ```bash
  winget install jqlang.jq
  ```
- **Chocolatey**:
  ```bash
  choco install jq
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Manual**:
  ```bash
  VER="1.7"
  curl -LO https://github.com/jqlang/jq/releases/download/jq-${VER}/jq-windows-amd64.exe
  move jq-windows-amd64.exe %USERPROFILE%\bin\jq.exe
  ```
  Add `%USERPROFILE%\bin` to PATH.

### Dependencies
- **None**: Binary installations are standalone.
- **gcc`, `make`, `bison`, `flex`: For source builds.
- **bash`/`zsh`/`fish`: For scripting and piping.
- **curl`/`wget`: For fetching JSON data.

### Verify Installation
Run `jq --version` to confirm installation. Example output:
```
jq-1.7
```
Verify functionality:
```bash
echo '{"name": "test"}' | jq .
```

## Basic Usage
`jq` processes JSON from stdin or files, applying filters to transform or extract data. The basic syntax is:
```bash
jq [OPTIONS] FILTER [FILE...]
```

- **FILTER**: Expression to process JSON (e.g., `.name`, `.[0]`).
- **FILE**: Input JSON file(s) (or stdin if omitted).
- **OPTIONS**: Flags to modify behavior (e.g., `--raw-output`, `--slurp`).

### Simple Example
Extract a field from JSON:
```bash
echo '{"name": "Alice", "age": 30}' | jq '.name'
```
Output: `"Alice"`

Select first array element:
```bash
echo '[{"id": 1}, {"id": 2}]' | jq '.[0]'
```
Output: `{"id": 1}`

Pretty-print JSON:
```bash
curl -s https://api.example.com/data | jq .
```

## Key Features
`jq`’s strengths include:
- **Powerful Filter Language**: Supports dot notation, indexing, slicing, and functional transformations.
- **Streaming Processing**: Handles large JSON datasets efficiently.
- **Multiple Formats**: Outputs JSON, raw strings, or compact formats.
- **Built-in Functions**: Over 100 functions for math, strings, regex, and more.
- **Scriptable**: Ideal for pipelines with `curl`, `fzf`, or `xargs`.
- **Error Handling**: Detailed diagnostics for malformed JSON or filters.

New in 1.7: `strptime`/`strftime` for date parsing, `with_entries` enhancements, and improved regex support with `test` and `match`.

## Common Options
Frequently used options (see `jq --help` or [JQ Manual](https://jqlang.github.io/jq/manual/)):

| Option | Description | Example |
|--------|-------------|---------|
| `-r`, `--raw-output` | Output strings without quotes | `jq -r '.name'` |
| `-c`, `--compact-output` | One JSON object per line | `jq -c '.'` |
| `-s`, `--slurp` | Read all input into an array | `jq -s '.'` |
| `-n`, `--null-input` | Use null input for expressions | `jq -n '"hello"'` |
| `--arg NAME VALUE` | Set variable | `jq --arg x 10 '.value = $x'` |
| `-f`, `--from-file` | Read filter from file | `jq -f filter.jq` |
| `--indent N` | Set JSON indent level | `jq --indent 4 '.'` |
| `-e` | Exit with status based on output | `jq -e '.name'` |

## Common Filters
- **Field Access**: `.field`, `.["field"]`.
- **Array Indexing**: `.[0]`, `.[-1]`.
- **Slicing**: `.[1:3]`.
- **Recursion**: `..` (deep search).
- **Pipes**: `.field | .subfield`.
- **Functions**: `map`, `select`, `length`, `keys`, `sort_by`.

## Advanced Usage
### Field Extraction
Complex path:
```bash
echo '{"user": {"name": "Alice", "age": 30}}' | jq '.user.name'
```
Output: `"Alice"`

Dynamic key:
```bash
echo '{"data": {"key1": "value"}}' | jq '.data["key1"]'
```

### Array Operations
Filter array:
```bash
echo '[{"id": 1, "active": true}, {"id": 2, "active": false}]' | jq '.[] | select(.active)'
```
Output: `{"id": 1, "active": true}`

Map transformation:
```bash
echo '[{"name": "alice"}, {"name": "bob"}]' | jq 'map(.name |= ascii_upcase)'
```

### Conditionals
Select with condition:
```bash
echo '[{"age": 25}, {"age": 17}]' | jq '.[] | select(.age > 18)'
```

### Recursion
Find all values:
```bash
curl -s https://api.example.com | jq '.. | .name? // empty'
```

### String Manipulation
Concatenate:
```bash
echo '{"first": "Alice", "last": "Smith"}' | jq '.first + " " + .last'
```
Output: `"Alice Smith"`

Regex matching (new in 1.7):
```bash
echo '{"name": "Alice123"}' | jq '.name | test("[0-9]+$")'
```
Output: `true`

### Date Handling
Parse date (new in 1.7):
```bash
echo '{"date": "2025-08-25"}' | jq '.date | strptime("%Y-%m-%d") | strftime("%Y/%m")'
```
Output: `"2025/08"`

### Aggregations
Group by:
```bash
echo '[{"region": "West", "sales": 100}, {"region": "West", "sales": 200}]' | jq 'group_by(.region) | map({region: .[0].region, total: map(.sales) | add})'
```

Sum:
```bash
echo '[{"value": 10}, {"value": 20}]' | jq 'map(.value) | add'
```
Output: `30`

### Slurping
Combine inputs:
```bash
echo '{"a": 1}\n{"a": 2}' | jq -s 'map(.a)'
```
Output: `[1, 2]`

### Variables
Set and use:
```bash
echo '{"value": 5}' | jq --arg threshold 10 '.value < $threshold'
```
Output: `true`

### External Filters
Use filter file:
```bash
echo 'select(.age > 18) | .name' > filter.jq
cat data.json | jq -f filter.jq
```

### Streaming
Process large JSON:
```bash
curl -s https://api.example.com/large | jq --stream 'from_stream(1|truncate_stream(inputs))'
```

## Niche Usages
### API Parsing
Extract API fields:
```bash
curl -s https://api.github.com/repos/jqlang/jq | jq '{name: .name, stars: .stargazers_count}'
```

### Log Analysis
Filter logs:
```bash
cat logs.json | jq 'select(.level == "ERROR") | {time: .timestamp, msg: .message}'
```

### Data Transformation
Normalize nested JSON:
```bash
cat data.json | jq 'map({id: .user.id, name: .user.name})'
```

### CI/CD Integration
GitHub Actions:
```yaml
- name: Process API response
  run: curl -s https://api.example.com | jq '.data | length' > count.txt
```

### With Other Tools
With `fzf`:
```bash
curl -s https://api.github.com/repos/jqlang/jq/commits | jq -r '.[].commit.message' | fzf
```

With `rg`:
```bash
cat data.json | jq -r '.[] | .message' | rg "error"
```

With `fd`:
```bash
fd -e json | xargs jq '.name'
```

## Practical Examples
1. **Extract Field**:
   ```bash
   echo '{"name": "Alice"}' | jq '.name'
   ```

2. **Filter Array**:
   ```bash
   echo '[{"age": 25}, {"age": 17}]' | jq '.[] | select(.age > 18)'
   ```

3. **Pretty-Print JSON**:
   ```bash
   curl -s https://api.example.com | jq .
   ```

4. **Interactive with Fzf**:
   ```bash
   curl -s https://api.github.com/repos/jqlang/jq/commits | jq -r '.[].commit.message' | fzf
   ```

5. **Sum Values**:
   ```bash
   echo '[{"value": 10}, {"value": 20}]' | jq 'map(.value) | add'
   ```

6. **Group By**:
   ```bash
   echo '[{"region": "West", "sales": 100}, {"region": "West", "sales": 200}]' | jq 'group_by(.region) | map({region: .[0].region, total: map(.sales) | add})'
   ```

7. **Regex Match**:
   ```bash
   echo '{"name": "Alice123"}' | jq '.name | test("[0-9]+$")'
   ```

8. **Date Parsing**:
   ```bash
   echo '{"date": "2025-08-25"}' | jq '.date | strptime("%Y-%m-%d") | strftime("%Y/%m")'
   ```

9. **Slurp Input**:
   ```bash
   echo '{"a": 1}\n{"a": 2}' | jq -s 'map(.a)'
   ```

10. **External Filter**:
    ```bash
    echo 'select(.age > 18) | .name' > filter.jq
    cat data.json | jq -f filter.jq
    ```

11. **Streaming Large JSON**:
    ```bash
    curl -s https://api.example.com/large | jq --stream 'from_stream(1|truncate_stream(inputs))'
    ```

12. **API Call**:
    ```bash
    curl -s https://api.github.com/repos/jqlang/jq | jq '{name: .name, stars: .stargazers_count}'
    ```

## Performance Tips
- **Streaming**: Use `--stream` for large JSON files.
- **Raw Output**: Use `-r` for faster string output.
- **Compact Output**: Use `-c` to reduce verbosity.
- **Limit Filters**: Avoid deep recursion (`..`) for large datasets.
- **Piping**: Pre-filter with `curl -s` or `head`.
- **Parallel**: Use `parallel` for multiple files:
  ```bash
  find . -name "*.json" | parallel jq '.name' {}
  ```

## Troubleshooting
- **No Output**: Check JSON validity (`jq .`) or filter syntax.
- **Malformed JSON**: Use `jq -e` to catch errors.
- **Memory Issues**: Enable `--stream` for large files.
- **Filter Errors**: Test with simple `.` filter; use `jq -n` for debugging.
- **Slow Performance**: Avoid complex filters; use `select` early.
- **Windows Paths**: Use WSL or Git Bash for compatibility.

Niche: Debug with `jq -n 'debug | .'` to trace filter execution.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `echo "alias jq='jq -r'" >> ~/.zshrc`.
  - Fish: `function jq; command jq -r $argv; end`.

- **Fzf**:
  ```bash
  curl -s https://api.github.com/repos/jqlang/jq/commits | jq -r '.[].commit.message' | fzf
  ```

- **Ripgrep**:
  ```bash
  cat data.json | jq -r '.[] | .message' | rg "error"
  ```

- **Fd**:
  ```bash
  fd -e json | xargs jq '.name'
  ```

- **Git**:
  ```bash
  git log --format=%B | jq -R -s 'split("\n")'
  ```

- **Editors**:
  - Vim: `curl -s https://api.example.com | jq . | vim -`.
  - VS Code: Task: `"command": "curl -s https://api.example.com | jq ."``.
  - Emacs: Script with `jq` in `eshell`.

- **Advanced Niche**:
  - CI/CD: `curl -s https://api.example.com | jq '.data | length' > count.txt` in GitHub Actions.
  - Docker: `docker run -i ghcr.io/jqlang/jq jq '.name' < data.json`.
  - Pass: `pass show api/token | curl -s -H "Authorization: Bearer {}" https://api.example.com | jq '.data'`.

## Resources
- [Official Repository](https://github.com/jqlang/jq)
- [Manual](https://jqlang.github.io/jq/manual/)
- [Releases](https://github.com/jqlang/jq/releases)
- Run `jq --help` or `man jq` for quick reference.
- Community: GitHub Issues, Stack Overflow, r/commandline.

## Conclusion
`jq` is a powerful, flexible tool for processing JSON data, offering a rich query language for parsing, filtering, and transforming. This guide covers its full range, from basic field extraction to advanced scripting and integrations, empowering you to streamline JSON workflows. Experiment with the examples to integrate `jq` into your data processing tasks!