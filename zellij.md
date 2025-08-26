# `zellij`

## Introduction
`zellij` is a modern terminal multiplexer written in Rust, designed to enhance terminal workflows by allowing multiple sessions, panes, and tabs within a single window. It aims to combine simplicity with powerful features, serving as a drop-in replacement for tools like `tmux` and `screen`. As of the latest version (0.43.1, released October 2025), `zellij` offers features like session persistence, customizable layouts, a plugin system, and multiplayer collaboration, making it ideal for developers, sysadmins, and terminal enthusiasts. Its user-friendly interface and intuitive keybindings cater to both beginners and power users.

`zellij` excels in scenarios requiring persistent terminal sessions, complex window management, or remote collaboration. It integrates seamlessly with tools like `fzf`, `rg`, `git`, and shells, providing a robust, customizable workspace for terminal-based tasks.[](https://github.com/zellij-org/zellij)[](https://www.tecmint.com/zellij-linux-terminal-multiplexer/)

## Installation
`zellij` is available on Linux, macOS, Windows, and FreeBSD. Below are common installation methods, updated for modern practices, including containerized options.

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian:
    ```bash
    sudo apt update
    sudo apt install zellij
    ```
  - Fedora/RHEL/CentOS/Rocky/AlmaLinux:
    ```bash
    sudo dnf install zellij
    ```
  - Arch Linux:
    ```bash
    sudo pacman -S zellij
    ```
  - Alpine Linux:
    ```bash
    sudo apk add zellij
    ```
  - OpenSUSE:
    ```bash
    sudo zypper install zellij
    ```
  - Nix:
    ```bash
    nix-env -i zellij
    ```
  - FreeBSD:
    ```bash
    sudo pkg install zellij
    ```
- **Binary Download**:
  ```bash
  VER="0.43.1"
  wget https://github.com/zellij-org/zellij/releases/download/v${VER}/zellij-x86_64-unknown-linux-musl.tar.gz
  tar -xzf zellij-x86_64-unknown-linux-musl.tar.gz
  chmod +x zellij
  sudo mv zellij /usr/local/bin/
  ```
  For ARM: Use `zellij-aarch64-unknown-linux-musl.tar.gz`.
- **From Source** (requires Rust and Cargo):
  ```bash
  cargo install --locked zellij
  ```
- **Docker**:
  ```bash
  docker run --rm -it -v $(pwd):/data ghcr.io/zellij-org/zellij zellij
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install zellij
  ```
- **MacPorts**:
  ```bash
  sudo port install zellij
  ```
- **Nix**:
  ```bash
  nix-env -i zellij
  ```
- **Binary Download**:
  ```bash
  VER="0.43.1"
  curl -LO https://github.com/zellij-org/zellij/releases/download/v${VER}/zellij-x86_64-apple-darwin.tar.gz
  tar -xzf zellij-x86_64-apple-darwin.tar.gz
  chmod +x zellij
  sudo mv zellij /usr/local/bin/
  ```

### Windows
- **Scoop**:
  ```bash
  scoop install zellij
  ```
- **Winget**:
  ```bash
  winget install Zellij.Zellij
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Manual**:
  ```bash
  VER="0.43.1"
  curl -LO https://github.com/zellij-org/zellij/releases/download/v${VER}/zellij-x86_64-pc-windows-msvc.zip
  unzip zellij-x86_64-pc-windows-msvc.zip
  move zellij.exe %USERPROFILE%\bin\
  ```
  Add `%USERPROFILE%\bin` to PATH.

### Try Without Installing
For Bash/Zsh:
```bash
bash <(curl -L https://zellij.dev/launch)
```
For Fish/Xonsh:
```bash
bash -c 'bash <(curl -L https://zellij.dev/launch)'
```

### Dependencies
- **Rust/Cargo**: For source builds.
- **bash`/`zsh`/`fish`: For scripting and try-without-install.
- **curl`/`wget`: For downloading binaries or launch scripts.
- **WebAssembly**: Optional for plugin development.

### Verify Installation
Run `zellij --version` to confirm installation. Example output:
```
zellij 0.43.1
```
Ensure Rust for source builds: `cargo --version`.

## Basic Usage
`zellij` launches a terminal multiplexer session with a TUI, allowing pane splitting, tab management, and session persistence. The basic syntax is:
```bash
zellij [COMMAND] [OPTIONS]
```

- **COMMAND**: Actions like `run`, `attach`, `list-sessions`, etc.
- **OPTIONS**: Flags to modify behavior (e.g., `--layout`, `--session`).

### Simple Example
Start a new session:
```bash
zellij
```
Output: TUI with a default session, status bar, and keybinding hints.

Start a named session:
```bash
zellij --session my-work
```

Attach to an existing session:
```bash
zellij attach my-work
```

List sessions:
```bash
zellij list-sessions
```

### Basic Keybindings (Default Mode)
- **Pane Management**:
  - `Ctrl+p n`: New pane.
  - `Ctrl+p -`: Split horizontally.
  - `Ctrl+p |`: Split vertically.
  - `Ctrl+p x`: Close pane.
  - `Ctrl+p arrows`: Switch panes.
  - `Ctrl+p Shift+arrows`: Resize panes.
- **Tab Management**:
  - `Ctrl+t n`: New tab.
  - `Ctrl+t Tab`: Switch tabs.
  - `Ctrl+t x`: Close tab.
- **Mode Switching**:
  - `Ctrl+p`: Pane mode.
  - `Ctrl+t`: Tab mode.
  - `Ctrl+s`: Session mode (for session manager).
- **Quit**: `Ctrl+q`.

Full keybindings: Press `Ctrl+g` for help or see [Zellij Keybindings](https://zellij.dev/documentation/keybindings.html).

## Key Features
`zellij`’s strengths include:
- **Session Persistence**: Automatically saves sessions, resuming after disconnects or crashes.[](https://www.msn.com/en-us/money/other/this-terminal-multiplexer-is-so-much-better-for-beginners-than-tmux/ar-AA1HCNSm)
- **Pane Management**: Split windows into multiple panes, stack, or float them.[](https://www.heise.de/en/news/Stack-and-pin-terminals-Zellij-0-42-0-brings-a-revised-interface-10318465.html)
- **Customizable Layouts**: Define layouts in KDL (Kiss Document Language) for automated setups.[](https://www.tecmint.com/zellij-linux-terminal-multiplexer/)
- **Plugin System**: Extend functionality with WebAssembly plugins (e.g., file picker, status bar).[](https://kalilinuxtutorials.com/zellij/)
- **Multiplayer Collaboration**: Multiple users can join a session with distinct cursors.[](https://zellij.dev/news/multiplayer-sessions/)
- **User-Friendly Interface**: Status bar with keybinding hints and mouse support.[](https://www.linuxtoday.com/blog/zellij-modern-drop-in-replacement-for-tmux-command/)
- **Cross-Platform**: Works on Linux, macOS, Windows, and FreeBSD.

New in 0.43.1: Improved plugin interfaces, pinned floating panes, and enhanced theme customization.[](https://www.heise.de/en/news/Stack-and-pin-terminals-Zellij-0-42-0-brings-a-revised-interface-10318465.html)

## Common Commands
Frequently used commands (see `zellij --help` or [Zellij Docs](https://zellij.dev/documentation/)):

| Command | Description | Example |
|---------|-------------|---------|
| `run` | Run command in new pane | `zellij run -- htop` |
| `attach` | Attach to existing session | `zellij attach my-work` |
| `list-sessions` | List active sessions | `zellij list-sessions` |
| `kill-session` | Kill a session | `zellij kill-session my-work` |
| `setup` | Generate config/layouts | `zellij setup --generate-config` |
| `options` | Set runtime options | `zellij options --simplified-ui true` |
| `plugin` | Load a plugin | `zellij plugin -- file://plugin.wasm` |

### Common Options
- `--session`: Specify session name.
- `--layout`: Load a layout file.
- `--config`: Use custom config file.
- `--force`: Override existing sessions.
- `--debug`: Enable debug logging.

## Advanced Usage
### Configuration
Customize via `~/.config/zellij/config.kdl`:
```kdl
keybinds {
  normal {
    bind "Ctrl b" { SwitchToMode "Pane" }
  }
}
theme "gruvbox"
simplified_ui true
```
Generate default config:
```bash
zellij setup --generate-config
```

### Layouts
Create a custom layout (`layout.kdl`):
```kdl
layout {
  pane split_direction="horizontal" {
    pane command="htop"
    pane
  }
  tab name="code" {
    pane split_direction="vertical" {
      pane command="vim"
      pane command="bash"
    }
  }
}
```
Load layout:
```bash
zellij --layout layout.kdl
```

### Plugins
Install a plugin:
```bash
mkdir -p ~/.config/zellij/plugins
wget https://github.com/zellij-org/zellij/raw/main/default-plugins/status-bar.wasm -O ~/.config/zellij/plugins/status-bar.wasm
```
Reference in `config.kdl`:
```kdl
plugins {
  status-bar location="file://~/.config/zellij/plugins/status-bar.wasm"
}
```

Write custom plugin (requires WebAssembly, e.g., Rust):
```bash
cargo new my-plugin --bin
# Add zellij-tile dependency
cargo build --target wasm32-wasi
```

### Session Management
Create named session:
```bash
zellij --session my-work --create
```

Attach or create:
```bash
zellij attach my-work --create
```

Session manager:
```bash
zellij action switch-mode session
```

### Multiplayer Collaboration
Start a session:
```bash
zellij --session collab --create
```
Share via SSH (requires VPN or `ngrok` for remote access):
```bash
ssh user@host 'zellij attach collab'
```
Each user gets a unique cursor color.[](https://zellij.dev/news/multiplayer-sessions/)

### Pane Management
Stack panes:
```bash
# In pane mode (Ctrl+p), press Shift+S to stack
```
Pin floating pane:
```bash
# In pane mode, press Shift+P to pin
```

### Scripting
Automate session setup:
```bash
zellij --session build --layout ci.kdl --force
zellij action new-pane --command "make test"
```

With `fzf`:
```bash
zellij list-sessions | fzf | xargs zellij attach
```

### Debugging
Run with logs:
```bash
zellij --debug > zellij.log
```

## Niche Usages
### Remote Work
Persist session on server:
```bash
ssh user@server
zellij --session prod
# Disconnect and reattach later
zellij attach prod
```

### CI/CD Integration
GitHub Actions:
```yaml
- name: Run tests in Zellij
  run: |
    zellij --session ci --layout test.kdl --force
    zellij action new-pane --command "make test"
```

### With Other Tools
With `fzf`:
```bash
zellij list-sessions | fzf | xargs zellij attach
```

With `rg`:
```bash
rg --files | fzf | xargs zellij action new-pane --command "vim {}"
```

With `fd`:
```bash
fd -t f | fzf | xargs zellij action new-pane --command "cat {}"
```

### Edge Cases
- Custom config: `zellij --config /custom/zellij.kdl`.
- Debug plugins: `zellij --debug plugin file://plugin.wasm`.
- Large sessions: Disable `simplified_ui` for better performance.
- Non-standard chars: Escape in `config.kdl`.

## Practical Examples
1. **Start Session**:
   ```bash
   zellij
   ```

2. **Named Session**:
   ```bash
   zellij --session my-work
   ```

3. **Load Layout**:
   ```bash
   zellij --layout layout.kdl
   ```

4. **Attach Session**:
   ```bash
   zellij attach my-work
   ```

5. **List Sessions**:
   ```bash
   zellij list-sessions
   ```

6. **Run Command in Pane**:
   ```bash
   zellij run -- htop
   ```

7. **Interactive Session with Fzf**:
   ```bash
   zellij list-sessions | fzf | xargs zellij attach
   ```

8. **Custom Config**:
   ```bash
   zellij --config custom.kdl
   ```

9. **Plugin Install**:
   ```bash
   zellij plugin -- file://~/.config/zellij/plugins/status-bar.wasm
   ```

10. **Multiplayer Setup**:
    ```bash
    zellij --session collab --create
    ```

11. **CI/CD Session**:
    ```bash
    zellij --session ci --layout ci.kdl --force
    ```

12. **Debug Logs**:
    ```bash
    zellij --debug > zellij.log
    ```

## Performance Tips
- **Simplify UI**: Set `simplified_ui true` in `config.kdl`.
- **Limit Plugins**: Load only necessary plugins.
- **Streaming**: Use `run` for lightweight commands.
- **Piping**: Pre-filter with `fzf` or `rg` for large inputs.
- **Large Sessions**: Avoid excessive pane splitting.

## Troubleshooting
- **No Sessions**: Check `zellij list-sessions` or create with `--create`.
- **Keybindings Fail**: Verify `config.kdl` or reset with `zellij setup --generate-config`.
- **Plugin Errors**: Ensure WebAssembly compatibility; test with `--debug`.
- **Slow Performance**: Disable plugins or `simplified_ui false`.
- **Connection Issues**: Use VPN or `ngrok` for multiplayer.[](https://zellij.dev/news/multiplayer-sessions/)
- **Windows TUI**: Use WSL for better terminal support.

Niche: Debug with `zellij --debug` and check `zellij.log`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `echo "alias zj='zellij'" >> ~/.zshrc`.
  - Fish: `function zj; zellij $argv; end`.

- **Fzf**:
  ```bash
  zellij list-sessions | fzf | xargs zellij attach
  ```

- **Ripgrep**:
  ```bash
  rg --files | fzf | xargs zellij action new-pane --command "vim {}"
  ```

- **Fd**:
  ```bash
  fd -t f | fzf | xargs zellij action new-pane --command "cat {}"
  ```

- **Git**:
  ```bash
  git pull && zellij --session dev --layout code.kdl
  ```

- **Editors**:
  - Vim: `zellij run -- vim file.txt`.
  - VS Code: Task: `"command": "zellij run -- code ."`
  - Emacs: Script with `zellij run`.

- **Advanced Niche**:
  - CI/CD: `zellij --session ci --layout test.kdl` in GitHub Actions.
  - Docker: `docker run -v $(pwd):/data ghcr.io/zellij-org/zellij zellij`.
  - Pass: `pass show git/token | zellij run --command "git clone {}"`.

## Resources
- [Official Website](https://zellij.dev/)
- [GitHub Repository](https://github.com/zellij-org/zellij)
- [Documentation](https://zellij.dev/documentation/)
- [Releases](https://github.com/zellij-org/zellij/releases)
- Run `zellij --help` or `man zellij` for quick reference.
- Community: GitHub Discussions, Discord, r/commandline.

## Conclusion
`zellij` is a modern, user-friendly terminal multiplexer that enhances productivity with its intuitive interface, session persistence, and powerful features like layouts and plugins. This guide covers its full range, from basic session management to advanced scripting and integrations, empowering you to streamline terminal workflows. Experiment with the examples to make `zellij` a cornerstone of your terminal environment![](https://kalilinuxtutorials.com/zellij/)