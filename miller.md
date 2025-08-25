# `miller`

## Introduction
`miller` (or `mlr`) is a command-line tool for processing name-indexed data, such as CSV, TSV, JSON, and other structured formats. Written in Go, it combines the functionality of Unix tools like `awk`, `sed`, `cut`, `join`, and `sort`, but with a focus on structured data with headers. As of the latest version (6.14.0, released October 2024), `miller` includes features like survival curve estimation, enhanced regex support, and new statistical functions, making it ideal for developers, data analysts, sysadmins, and DevOps professionals handling data cleaning, transformation, and analysis.

`miller` excels in scenarios requiring data munging, format conversion, statistical reporting, or log processing, particularly when dealing with heterogeneous records or large datasets. It integrates seamlessly with tools like `jq`, `fzf`, `rg`, and `xargs`, offering a streaming, memory-efficient solution for data pipelines.[](https://github.com/johnkerl/miller)

## Installation
`miller` is available on Linux, macOS, Windows, and various Unix-like systems. Below are common installation methods, updated for the latest practices, including containerized options.

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian:
    ```bash
    sudo apt update
    sudo apt install miller
    ```
  - Fedora:
    ```bash
    sudo dnf install miller
    ```
  - Arch Linux:
    ```bash
    sudo pacman -S miller
    ```
    Or AUR: `yay -S miller`
  - Nix:
    ```bash
    nix-env -i miller
    ```
- **Binary Download**:
  ```bash
  VER="6.14.0"
  wget https://github.com/johnkerl/miller/releases/download/v${VER}/miller-${VER}-linux-amd64.tar.gz
  tar -xzf miller-${VER}-linux-amd64.tar.gz
  sudo mv mlr /usr/local/bin/
  ```
  For ARM: Use `miller-${VER}-linux-arm64.tar.gz`.
- **From Source** (requires Go 1.17+ and `gcc` for cgo):
  ```bash
  git clone https://github.com/johnkerl/miller
  cd miller
  make
  sudo make install
  ```
  Or via Go:
  ```bash
  go install github.com/johnkerl/miller/v6/cmd/mlr@latest
  ```
  Installs to `$GOPATH/bin/mlr`.[](https://github.com/johnkerl/miller)
- **Docker**:
  ```bash
  docker run --rm -i ghcr.io/johnkerl/miller mlr --version
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install miller
  ```
- **MacPorts**:
  ```bash
  sudo port install miller
  ```
- **Nix**:
  ```bash
  nix-env -i miller
  ```
- **Binary Download**:
  ```bash
  VER="6.14.0"
  curl -LO https://github.com/johnkerl/miller/releases/download/v${VER}/miller-${VER}-darwin-amd64.tar.gz
  tar -xzf miller-${VER}-darwin-amd64.tar.gz
  sudo mv mlr /usr/local/bin/
  ```

### Windows
- **Scoop**:
  ```bash
  scoop install miller
  ```
- **Winget**:
  ```bash
  winget install JohnKerl.Miller
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Manual**:
  ```bash
  VER="6.14.0"
  curl -LO https://github.com/johnkerl/miller/releases/download/v${VER}/miller-${VER}-windows-amd64.zip
  unzip miller-${VER}-windows-amd64.zip
  move mlr.exe %USERPROFILE%\bin\
  ```
  Add `%USERPROFILE%\bin` to PATH.

### Dependencies
- **Go**: Required for source builds (1.17+ for v6.14.0).
- **gcc**: For cgo dependencies during source builds.
- **bash`/`zsh`/`fish`: For scripting and piping.
- **jq**: Optional for JSON processing.

### Verify Installation
Run `mlr --version` to confirm installation. Example output:
```
mlr 6.14.0
```
Verify Go environment for source builds: `go version`.[](https://github.com/johnkerl/miller)

## Basic Usage
`miller` processes structured data from files or stdin, applying verbs (commands) to transform or analyze data. The basic syntax is:
```bash
mlr [OPTIONS] [VERB] [ARGUMENTS] [FILES]
```

- **VERB**: Commands like `cut`, `sort`, `stats1`, `join`, etc.
- **OPTIONS**: Flags to modify behavior (e.g., `--csv`, `--json`).
- **FILES**: Input files (or stdin if omitted).

### Simple Example
Filter columns from a CSV:
```bash
mlr --csv cut -f name,age data.csv
```
Output: CSV with only `name` and `age` columns.

Sort JSON by field:
```bash
cat data.json | mlr --json sort -f id
```

Count records:
```bash
mlr --tsv count data.tsv
```

## Key Features
`miller`’s strengths include:
- **Structured Data Support**: Handles CSV, TSV, JSON, DKVP, and more.
- **Streaming Processing**: Processes records one at a time, minimizing memory usage.
- **Record Heterogeneity**: Supports interleaved records with different schemas.
- **Rich Verb Set**: Over 50 verbs for filtering, sorting, joining, and statistics.
- **DSL (Domain-Specific Language)**: Custom expressions for advanced transformations.
- **Format Conversion**: Converts between CSV, JSON, TSV, etc., with header preservation.
- **Scriptable**: Ideal for pipelines with `jq`, `fzf`, or `xargs`.

New in 6.14.0: `surv` verb for survival curves, regex support in `cut -r`, and `strmatch`/`strmatchx` DSL functions.[](https://github.com/johnkerl/miller/releases)

## Common Verbs
Frequently used verbs (see `mlr --help` or [Miller Docs](https://miller.readthedocs.io) for a full list):

| Verb | Description | Example |
|------|-------------|---------|
| `cut` | Select/exclude fields | `mlr --csv cut -f name,age data.csv` |
| `sort` | Sort records by fields | `mlr --json sort -f id data.json` |
| `filter` | Filter records by condition | `mlr --csv filter '$age > 18' data.csv` |
| `stats1` | Compute statistics (mean, sum, etc.) | `mlr --csv stats1 -a sum -f sales data.csv` |
| `join` | Join two datasets | `mlr --csv join -f lookup.csv -j id data.csv` |
| `group-by` | Group records by fields | `mlr --csv group-by region data.csv` |
| `cat` | Concatenate records | `mlr --tsv cat file1.tsv file2.tsv` |
| `sub` | Substitute text in fields | `mlr --csv sub -f name "old" "new" data.csv` |
| `put` | Apply DSL expressions | `mlr --json put '$total = $price * $qty' data.json` |
| `count` | Count records | `mlr --csv count data.csv` |

### Common Options
- `--csv`, `--tsv`, `--json`: Set input/output format.
- `-I`: In-place processing.
- `--from`: Specify input file(s).
- `-o`: Set output format (e.g., `json`, `csv`).
- `--headerless-csv-output`: Skip header row in output.
- `-n`: Dry-run (show what would be done).

## Advanced Usage
### Format Conversion
Convert CSV to JSON:
```bash
mlr --icsv --ojson cat data.csv
```

TSV to CSV with headerless output:
```bash
mlr --itsv --ocsv --headerless-csv-output cat data.tsv
```

### Filtering
Complex filter with DSL:
```bash
mlr --csv filter '$age > 18 && $region == "West"' data.csv
```

Regex filter:
```bash
mlr --csv filter -x '$name =~ "^[A-Z]"' data.csv
```

### Sorting
Sort numerically:
```bash
mlr --json sort -n price data.json
```

Reverse sort:
```bash
mlr --csv sort -r name data.csv
```

### Statistics
Compute mean and sum:
```bash
mlr --csv stats1 -a mean,sum -f sales -g region data.csv
```

Survival curve (new in 6.14.0):
```bash
mlr --csv surv -f time,event data.csv
```

### Joins
Join with lookup table:
```bash
mlr --csv join -f lookup.csv -j id --lr data.csv
```

Left join:
```bash
mlr --csv join --ul -f lookup.csv -j id data.csv
```

### DSL Expressions
Add computed field:
```bash
mlr --json put '$tax = $price * 0.1; $total = $price + $tax' data.json
```

String manipulation:
```bash
mlr --csv put '$name = toupper($name)' data.csv
```

New `strmatch` function:
```bash
mlr --csv filter 'strmatch($name, "^[A-Z]")' data.csv
```

### Grouping
Group by multiple fields:
```bash
mlr --csv group-by region,department stats1 -a sum -f sales data.csv
```

### Substitutions
Replace text with `sub` (new in 6.9.0):
```bash
mlr --csv sub -f description "error" "warning" data.csv
```

Global replace with `gsub`:
```bash
mlr --csv gsub -f description "[aeiou]" "*" data.csv
```

### Streaming
Process large files:
```bash
mlr --csv filter '$size > 1000' huge.csv | mlr --csv sort -n size
```

Tail logs:
```bash
tail -f log.csv | mlr --csv cat
```

### Scripting
Batch rename fields:
```bash
mlr --csv rename old_name,new_name data.csv
```

Pipe to `jq`:
```bash
mlr --json cat data.json | jq '.[] | .name'
```

With `fzf`:
```bash
mlr --csv cut -f name data.csv | fzf | xargs -I {} mlr --csv filter '$name == "{}"' data.csv
```

## Niche Usages
### Log Processing
Filter log entries:
```bash
mlr --csv filter '$level == "ERROR"' logs.csv
```

### Data Cleaning
Remove empty records:
```bash
mlr --csv filter '!is_empty($name)' data.csv
```

Fix UTF-8 issues:
```bash
mlr --csv put '$name = strptoutf8($name)' data.csv
```

### Statistical Analysis
Compute percentiles:
```bash
mlr --csv stats1 -a p25,p50,p75 -f price data.csv
```

Array/map stats (new in 6.9.0):
```bash
mlr --json put '$stats = stats1($values, "mean")' data.json
```

### CI/CD Integration
GitHub Actions:
```yaml
- name: Process CSV
  run: mlr --csv stats1 -a sum -f sales data.csv > report.csv
```

### With Other Tools
With `fzf`:
```bash
mlr --csv cut -f name data.csv | fzf | xargs -I {} mlr --csv filter '$name == "{}"' data.csv
```

With `rg`:
```bash
mlr --csv cat data.csv | rg "error"
```

With `jq`:
```bash
mlr --ijson --ocsv cat data.json > data.csv
```

### Edge Cases
- Handle UTF-8 issues: Use `strptoutf8` function.[](https://github.com/johnkerl/miller/releases)
- Large files: Stream with `cat` or `filter` to avoid memory issues.
- Custom separator: `--ifs "," --ofs ";"`.
- Debug DSL: `mlr put -v '$x = 1'` to see expression evaluation.
- In-place edit: `mlr -I --csv put '$total = $price * $qty' data.csv`.

## Practical Examples
1. **Filter CSV**:
   ```bash
   mlr --csv filter '$age > 18' data.csv
   ```

2. **Sort JSON**:
   ```bash
   mlr --json sort -f id data.json
   ```

3. **Compute Stats**:
   ```bash
   mlr --csv stats1 -a mean,sum -f sales data.csv
   ```

4. **Join Files**:
   ```bash
   mlr --csv join -f lookup.csv -j id data.csv
   ```

5. **Convert Format**:
   ```bash
   mlr --icsv --ojson cat data.csv
   ```

6. **Interactive with Fzf**:
   ```bash
   mlr --csv cut -f name data.csv | fzf | xargs -I {} mlr --csv filter '$name == "{}"' data.csv
   ```

7. **Group By**:
   ```bash
   mlr --csv group-by region stats1 -a sum -f sales data.csv
   ```

8. **Substitutions**:
   ```bash
   mlr --csv sub -f description "error" "warning" data.csv
   ```

9. **DSL Expression**:
   ```bash
   mlr --json put '$total = $price * $qty' data.json
   ```

10. **Survival Curve**:
    ```bash
    mlr --csv surv -f time,event data.csv
    ```

11. **Tail Logs**:
    ```bash
    tail -f log.csv | mlr --csv cat
    ```

12. **In-Place Edit**:
    ```bash
    mlr -I --csv put '$tax = $price * 0.1' data.csv
    ```

## Performance Tips
- **Streaming**: Use `cat`, `filter`, or `cut` for large files.
- **Minimal Verbs**: Avoid `sort` or `stats1` for huge datasets unless necessary.
- **Field Selection**: Use `cut -f` to reduce processed data.
- **Piping**: Combine with `head` or `tail` for quick previews.
- **Parallel**: Use `parallel` for multi-file processing:
  ```bash
  find . -name "*.csv" | parallel mlr --csv cat {}
  ```
- **Format**: Use `--csvlite` for faster CSV processing.

## Troubleshooting
- **No Output**: Check input format (`--csv`, `--json`) and file existence.
- **Malformed CSV**: Use `--csvlite` or `clean-whitespace`.
- **Memory Issues**: Stream with `cat` or use `--mmap`.
- **UTF-8 Errors**: Apply `strptoutf8` in DSL.[](https://github.com/johnkerl/miller/releases)
- **Verb Errors**: Use `mlr --help-verb <verb>` for syntax.
- **Slow Performance**: Limit fields with `cut` or use `-n` for dry-run.

Niche: Debug with `mlr --debug` or `mlr put -v`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `echo "alias mlr='mlr --csv'" >> ~/.zshrc`.
  - Fish: `function mlr; command mlr --csv $argv; end`.

- **Fzf**:
  ```bash
  mlr --csv cut -f name data.csv | fzf | xargs -I {} mlr --csv filter '$name == "{}"' data.csv
  ```

- **Ripgrep**:
  ```bash
  mlr --csv cat data.csv | rg "error"
  ```

- **Fd**:
  ```bash
  fd -e csv | xargs mlr --csv cat
  ```

- **Jq**:
  ```bash
  mlr --ijson --ocsv cat data.json > data.csv
  ```

- **Git**:
  ```bash
  git clone <repo> && mlr --csv cat data.csv >> README.md
  ```

- **Editors**:
  - Vim: `mlr --csv cut -f name data.csv | vim -`.
  - VS Code: Task: `"command": "mlr --csv stats1 -a sum -f sales data.csv"`.
  - Emacs: Script with `mlr` in `eshell`.

- **Advanced Niche**:
  - CI/CD: `mlr --csv stats1 -a sum -f sales data.csv > report.csv` in GitHub Actions.
  - Docker: `docker run -i ghcr.io/johnkerl/miller mlr --csv cat < data.csv`.
  - Pass: `pass show data/token | mlr --csv put '$token = "{}"' data.csv`.

## Resources
- [Official Repository](https://github.com/johnkerl/miller)
- [Documentation](https://miller.readthedocs.io)
- [Releases](https://github.com/johnkerl/miller/releases)
- Run `mlr --help` or `man mlr` for quick reference.
- Community: GitHub Discussions, Stack Overflow, r/commandline.

## Conclusion
`miller` is a versatile, powerful tool for processing structured data, combining the strengths of Unix tools with modern data-handling capabilities. This guide covers its full range, from basic transformations to advanced statistical analysis and integrations, empowering you to streamline data workflows. Experiment with the examples to integrate `miller` into your data processing tasks![](https://github.com/johnkerl/miller)