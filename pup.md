# `pup`

## Introduction
`pup` is a command-line tool for parsing and processing HTML, designed to filter and extract data using CSS selectors. Written in Go and inspired by `jq`, it reads from stdin, outputs to stdout, and allows flexible exploration of HTML documents directly in the terminal. As of the latest version (0.4.0, released July 2016), `pup` supports a wide range of CSS selectors, pseudo-classes, and output formats like JSON, making it ideal for developers, sysadmins, and data enthusiasts who need to scrape or analyze web content in scripts or one-liners.[](https://github.com/ericchiang/pup)

`pup` excels in scenarios requiring quick HTML parsing, such as extracting links, titles, or structured data from web pages, and integrates seamlessly with tools like `curl`, `fzf`, `jq`, and `xargs`. Despite no updates since 2016, it remains a lightweight, effective solution for terminal-based HTML processing.[](https://github.com/ericchiang/pup/issues/150)

## Installation
`pup` is available on Linux, macOS, Windows, and various BSD systems. Below are common installation methods, updated for modern practices, addressing issues like outdated `go get` instructions.[](https://github.com/ericchiang/pup/issues/207)

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian (available in Debian testing/Bullseye):
    ```bash
    sudo apt update
    sudo apt install pup
    ```
    Note: Not in Ubuntu main repos; use manual install for older versions.[](https://github.com/ericchiang/pup/issues/129)[](https://github.com/ericchiang/pup/issues/150)
  - Fedora: Not natively available; use binary or source install.
  - Arch Linux: `yay -S pup-bin` (AUR).
  - Nix: `nix-env -i pup`.
- **Binary Download**:
  ```bash
  VER="0.4.0"
  wget https://github.com/ericchiang/pup/releases/download/v${VER}/pup_v0.4.0_linux_amd64.zip
  unzip pup_v0.4.0_linux_amd64.zip
  sudo mv pup /usr/local/bin/
  ```
  For ARM (e.g., Raspberry Pi): Use `pup_v0.4.0_linux_arm.zip` or `pup_v0.4.0_linux_arm64.zip`.[](https://github.com/ericchiang/pup/issues/131)
- **From Source** (requires Go 1.6+):
  ```bash
  go install github.com/ericchiang/pup@latest
  ```
  Note: `go get` is deprecated; use `go install` instead.[](https://github.com/ericchiang/pup/issues/207)
- **Docker**:
  ```bash
  docker run --rm -i ghcr.io/ericchiang/pup pup --version
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install pup
  ```
  Note: Homebrew support was deprecated but re-added in recent years.[](https://github.com/ericchiang/pup/issues/207)[](https://macappstore.org/pup/)
- **MacPorts**:
  ```bash
  sudo port install pup
  ```
- **Nix**: `nix-env -i pup`
- **Binary Download**:
  ```bash
  VER="0.4.0"
  wget https://github.com/ericchiang/pup/releases/download/v${VER}/pup_v0.4.0_darwin_amd64.zip
  unzip pup_v0.4.0_darwin_amd64.zip
  sudo mv pup /usr/local/bin/
  ```

### Windows
- **Scoop**:
  ```bash
  scoop install pup
  ```
- **Winget**: Not officially supported; use binary or WSL.
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Manual**:
  ```bash
  VER="0.4.0"
  curl -LO https://github.com/ericchiang/pup/releases/download/v${VER}/pup_v0.4.0_windows_amd64.zip
  unzip pup_v0.4.0_windows_amd64.zip
  move pup.exe %USERPROFILE%\bin\
  ```
  Add `%USERPROFILE%\bin` to PATH.

### Dependencies
- **Go**: Required for source builds (1.6+ for v0.4.0).
- **curl`/`wget`: For downloading binaries or HTML.
- **bash`/`zsh`/`fish`: For scripting and piping.
- **jq**: Optional for JSON processing.

### Verify Installation
Run `pup --version` to confirm installation. Example output:
```
pup version 0.4.0
```
Verify Go environment for source builds: `go version`.

## Basic Usage
`pup` processes HTML from stdin, applying CSS selectors to filter elements and output results to stdout. The basic syntax is:
```bash
pup [FLAGS] '[SELECTORS] [DISPLAY_FUNCTION]'
```

- **SELECTORS**: CSS selectors to filter HTML (e.g., `a`, `#id`, `.class`).
- **DISPLAY_FUNCTION**: Optional output format (e.g., `text{}`, `json{}`).
- **FLAGS**: Modify behavior (e.g., `--color`, `--indent`).

### Simple Example
Fetch and parse HTML:
```bash
curl -s https://example.com | pup 'title'
```
Output:
```
<title>Example Domain</title>
```

Extract links:
```bash
curl -s https://news.ycombinator.com | pup 'a attr{href}'
```

JSON output:
```bash
curl -s https://news.ycombinator.com | pup 'table table tr td.title a json{}'
```

## Key Features
`pup`’s strengths include:
- **CSS Selector Support**: Robust filtering with tag, class, ID, attribute, and pseudo-class selectors.
- **Output Formats**: Outputs raw HTML, text, attribute values, or JSON.
- **Piping**: Reads from stdin, ideal for chaining with `curl`, `wget`, or `cat`.
- **Clean HTML**: Automatically fills missing tags and indents output.
- **Lightweight**: Minimal dependencies, fast execution in Go.
- **Scriptable**: Perfect for shell scripts and automation pipelines.

Notable in v0.4.0: Added preformatted text preservation, comma-separated selectors, and pseudo-class support like `:contains()`.[](https://github.com/EricChiang/pup/releases)

## Common Flags
Frequently used flags (see `pup --help` for a full list):

| Flag | Description | Example |
|------|-------------|---------|
| `--color` | Colorize output | `pup --color 'title'` |
| `-i`, `--indent` | Set JSON indent level | `pup -i 2 'a json{}'` |
| `--charset` | Specify input charset | `pup --charset utf-8 'title'` |
| `--number` | Number output lines | `pup --number 'a'` |
| `-p`, `--pre` | Preserve preformatted text | `pup -p 'pre'` |
| `-n` | Print number of matches | `pup -n 'div'` |
| `-l` | Limit output lines | `pup -l 5 'a'` |

## Common Selectors
Supported CSS selectors (see [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_selectors) for details):[](https://github.com/ericchiang/pup)
- **Basic**: `.class`, `#id`, `element` (e.g., `div`, `a`).
- **Relational**: `selector + selector` (adjacent), `selector > selector` (child), `,` (multiple).
- **Attributes**: `[attribute]`, `[attribute="value"]`, `[attribute*="value"]`, etc.
- **Pseudo-Classes**: `:empty`, `:first-child`, `:last-child`, `:nth-child(n)`, `:contains("text")`, `:parent-of(selector)`, `:not(selector)`.

## Advanced Usage
### Selectors
Chain selectors:
```bash
curl -s https://example.com | pup 'div#container a'
```
Output: Links within `<div id="container">`.

Multiple selectors:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup 'title, h1 span[dir="auto"]'
```

Pseudo-classes:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup ':contains("History")'
```

Attribute filtering:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup 'th[scope="row"]'
```

### Display Functions
Text output:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup '.mw-headline text{}'
```
Output: Text of all `.mw-headline` elements.

Attribute values:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup '.catlinks div attr{id}'
```
Output: `mw-normal-catlinks mw-hidden-catlinks`.

JSON output:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup 'div#p-namespaces a json{}'
```
Output: JSON array of `<a>` elements with attributes and text.

Indented JSON:
```bash
curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup -i 4 'title json{}'
```

### Piping and Chaining
With `curl`:
```bash
curl -s https://news.ycombinator.com | pup 'table table tr td.title a' | grep "http"
```

With `jq`:
```bash
curl -s https://news.ycombinator.com | pup 'td.title a json{}' | jq '.[].text'
```

With `fzf`:
```bash
curl -s https://news.ycombinator.com | pup 'a attr{href}' | fzf
```

### Scripting
Extract titles to file:
```bash
curl -s https://news.ycombinator.com | pup 'td.title a text{}' > titles.txt
```

Batch process with `xargs`:
```bash
curl -s https://example.com | pup 'a attr{href}' | xargs -I {} curl -O {}
```

### Debugging
Verbose output:
```bash
curl -s https://example.com | pup 'div' --debug
```

Count matches:
```bash
curl -s https://example.com | pup 'div' -n
```

### Edge Cases
- Handle malformed HTML: `pup` auto-fixes missing tags.
- Charset issues: Use `--charset utf-8`.
- Large HTML: Use `-l` to limit output.
- Non-standard selectors: Combine `:not()` with `:contains()` for precision.

## Niche Usages
### Web Scraping
Extract structured data:
```bash
curl -s https://news.ycombinator.com | pup 'table table tr td.title a json{}' | jq '[.[] | {title: .text, url: .href}]'
```

### Automation
Cron job to monitor site changes:
```bash
curl -s https://example.com | pup 'title text{}' > title.txt
diff title.txt title-prev.txt || echo "Title changed"
```

### With Other Tools
With `fzf`:
```bash
curl -s https://news.ycombinator.com | pup 'a attr{href}' | fzf | xargs curl -s | pup 'title'
```

With `rg`:
```bash
curl -s https://example.com | pup 'div' | rg "error"
```

With `xargs`:
```bash
curl -s https://news.ycombinator.com | pup 'a attr{href}' | xargs -I {} wget {}
```

### CI/CD Integration
GitHub Actions:
```yaml
- name: Scrape site
  run: curl -s https://example.com | pup 'title text{}' > title.txt
```

### Raspberry Pi
Install on Raspbian:
```bash
VER="0.4.0"
wget https://github.com/ericchiang/pup/releases/download/v${VER}/pup_v0.4.0_linux_arm.zip
unzip pup_v0.4.0_linux_arm.zip
sudo mv pup /usr/local/bin/
```
Or via Go:
```bash
GOBIN=/usr/local/bin go install github.com/ericchiang/pup@latest
```
Ensure `/usr/local/bin` is in PATH.[](https://github.com/ericchiang/pup/issues/131)[](https://github.com/ericchiang/pup/issues/145)

## Practical Examples
1. **Extract Title**:
   ```bash
   curl -s https://example.com | pup 'title'
   ```

2. **List Links**:
   ```bash
   curl -s https://news.ycombinator.com | pup 'a attr{href}'
   ```

3. **JSON Output**:
   ```bash
   curl -s https://news.ycombinator.com | pup 'td.title a json{}'
   ```

4. **Interactive with Fzf**:
   ```bash
   curl -s https://news.ycombinator.com | pup 'a attr{href}' | fzf
   ```

5. **Clean HTML**:
   ```bash
   cat messy.html | pup --color
   ```

6. **Filter by Attribute**:
   ```bash
   curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup 'th[scope="row"]'
   ```

7. **Count Matches**:
   ```bash
   curl -s https://example.com | pup 'div' -n
   ```

8. **Pseudo-Class**:
   ```bash
   curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup ':contains("History")'
   ```

9. **Multiple Selectors**:
   ```bash
   curl -s https://en.wikipedia.org/wiki/Robots_exclusion_standard | pup 'title, h1 span[dir="auto"]'
   ```

10. **Batch Download**:
    ```bash
    curl -s https://example.com | pup 'a attr{href}' | xargs -I {} wget {}
    ```

11. **Preformatted Text**:
    ```bash
    curl -s https://example.com | pup -p 'pre'
    ```

12. **Debug Output**:
    ```bash
    curl -s https://example.com | pup 'div' --debug
    ```

## Performance Tips
- **Limit Output**: Use `-l` for large HTML documents.
- **Pipe Efficiently**: Pre-filter with `curl -s` to reduce input size.
- **JSON Parsing**: Use `jq` to process large JSON outputs.
- **Selectors**: Prefer simple selectors (e.g., `tag`) over complex regex for speed.
- **Caching**: Save HTML locally (`curl -o file.html`) for repeated parsing.
- **Scripting**: Batch commands with `xargs` to minimize `pup` invocations.

## Troubleshooting
- **Command Not Found**: Ensure `/usr/local/bin` is in PATH; check binary permissions.[](https://github.com/ericchiang/pup/issues/207)
- **No Matches**: Verify selector syntax; test with simple `tag` selector.
- **Malformed HTML**: `pup` auto-fixes, but use `--color` to inspect output.
- **Charset Issues**: Specify `--charset utf-8`.
- **Go Install Fails**: Use `go install` instead of `go get`; ensure `GOBIN` is set.[](https://github.com/ericchiang/pup/issues/207)
- **Raspberry Pi**: Use ARM binary or build with `go install`.[](https://github.com/ericchiang/pup/issues/131)
- **Large Output**: Use `-l` or pipe to `head`.

Niche: Debug with `--debug` or check logs in `/tmp/pup.log` if enabled.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `echo "alias pup='pup --color'" >> ~/.zshrc`.
  - Fish: `function pup; command pup --color $argv; end`.

- **Fzf**:
  ```bash
  curl -s https://news.ycombinator.com | pup 'a attr{href}' | fzf | xargs curl -s | pup 'title'
  ```

- **Ripgrep**:
  ```bash
  curl -s https://example.com | pup 'div' | rg "error"
  ```

- **Fd**:
  ```bash
  fd -e html | xargs cat | pup 'title'
  ```

- **Jq**:
  ```bash
  curl -s https://news.ycombinator.com | pup 'td.title a json{}' | jq '.[].text'
  ```

- **Git**:
  ```bash
  git clone <repo> && curl -s https://example.com | pup 'title' >> README.md
  ```

- **Editors**:
  - Vim: `curl -s https://example.com | pup 'title' | vim -`.
  - VS Code: Task: `"command": "curl -s https://example.com | pup 'title'"`.
  - Emacs: Script with `pup` in `eshell`.

- **Advanced Niche**:
  - CI/CD: `curl -s https://example.com | pup 'title' > title.txt` in GitHub Actions.
  - Docker: `docker run -i ghcr.io/ericchiang/pup pup 'title' < page.html`.
  - Pass: `pass show web/token | curl -s -H "Authorization: Bearer {}" https://api.example.com | pup 'data json{}'`.

## Resources
- [Official Repository](https://github.com/ericchiang/pup)[](https://github.com/ericchiang/pup)
- [Releases](https://github.com/ericchiang/pup/releases)[](https://github.com/EricChiang/pup/releases)
- Run `pup --help` for quick reference.
- Community: GitHub Issues, Stack Overflow, r/commandline.[](https://github.com/ericchiang/pup/issues/150)

## Conclusion
`pup` is a lightweight, powerful tool for parsing HTML in the terminal, offering flexible CSS selector-based filtering and multiple output formats. Despite its last update in 2016, it remains a robust choice for web scraping and automation. This guide covers its full range, from basic parsing to advanced scripting and integrations, empowering you to extract HTML data efficiently. Experiment with the examples to integrate `pup` into your workflows![](https://github.com/ericchiang/pup)