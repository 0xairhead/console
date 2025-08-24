# `doppler`

## Introduction
`doppler` is a command-line interface (CLI) tool for secure secret and configuration management, designed to streamline access to environment variables across development, CI/CD, staging, and production environments. Built by DopplerHQ, it emphasizes security, simplicity, and cross-platform compatibility, injecting secrets as environment variables or ephemeral files. As of the latest version (3.71.0, released July 2024), `doppler` includes features like automatic updates, multi-workplace support, and enhanced secret mounting, making it ideal for developers, DevOps engineers, and teams managing sensitive data.

`doppler` excels in scenarios requiring secure secret management, such as injecting API keys into applications, automating CI/CD pipelines, or syncing secrets across multiple projects. It integrates seamlessly with tools like `git`, `fzf`, and various shells, offering a lightweight alternative to heavyweight secret managers like HashiCorp Vault.

## Installation
`doppler` is available on Linux, macOS, and Windows. Below are common installation methods, updated for the latest practices, including containerized options:[](https://docs.doppler.com/docs/cli)[](https://docs.doppler.com/docs/install-cli)

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian:
    ```bash
    sudo apt update && sudo apt install -y gnupg
    curl -sLf --retry 3 --tlsv1.2 --proto "=https" 'https://packages.doppler.com/public/cli/gpg.DE2A7741A397C129.key' | sudo apt-key add -
    echo "deb https://packages.doppler.com/public/cli/deb/debian any-version main" | sudo tee /etc/apt/sources.list.d/doppler-cli.list
    sudo apt update && sudo apt install doppler
    ```
  - Fedora: `sudo dnf install doppler` (via third-party repos; check Doppler’s [Install page](https://docs.doppler.com/docs/install-cli)).
  - Arch Linux: `yay -S doppler-bin` (AUR package).
  - Nix: `nix-env -i doppler`.
- **Shell Script**:
  ```bash
  (curl -Ls --tlsv1.2 --proto "=https" --retry 3 https://cli.doppler.com/install.sh || wget -t 3 -qO- https://cli.doppler.com/install.sh) | sh
  ```
- **Binary Download**: Pre-built binaries from [Doppler Releases](https://github.com/DopplerHQ/cli/releases).
- **Docker**:
  ```bash
  docker run --rm -v $HOME/.doppler:/root/.doppler dopplerhq/cli doppler
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install gnupg  # Prerequisite for signature verification
  brew install dopplerhq/cli/doppler
  ```
- **MacPorts**:
  ```bash
  sudo port install doppler
  ```
- **Nix**: `nix-env -i doppler`

### Windows
- **Winget** (recommended):
  ```bash
  winget install doppler.doppler
  ```
- **Scoop**:
  ```bash
  scoop bucket add doppler https://github.com/DopplerHQ/scoop-doppler.git
  scoop install doppler
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Manual**: Download binaries from [Doppler Releases](https://github.com/DopplerHQ/cli/releases) and add to PATH.

### Dependencies
- **gnupg**: Required for binary signature verification (Linux/macOS).
- **curl`/`wget**: For script-based installation.
- **Git**: Optional for project integration.
- **bash`/`zsh`/`fish**: For shell completions.

### Verify Installation
Run `doppler --version` to confirm installation. Example output:
```
doppler 3.71.0
```
Verify GPG (if used): `gpg --list-keys`.

## Basic Usage
`doppler` manages secrets by scoping them to projects and configurations, injecting them as environment variables or files. The basic syntax is:
```bash
doppler [COMMAND] [OPTIONS]
```

- **COMMAND**: Actions like `login`, `setup`, `run`, `secrets`, etc.
- **OPTIONS**: Flags to modify behavior (e.g., token, mount, scope).
- **Default Behavior**: Reads config from `~/.doppler` or `$DOPPLER_CONFIG_DIR`.

### Simple Example
Authenticate to Doppler:
```bash
doppler login
```
Follow the browser-based OAuth flow to authenticate.

Set up a project and config:
```bash
cd ~/my-project
doppler setup
```
Select a project and config interactively.

Run a command with secrets injected:
```bash
doppler run -- printenv | grep API_KEY
```
Output: `API_KEY=your-secret-value`.

## Key Features
`doppler`’s strengths include:
- **Secure Secret Management**: Secrets are encrypted and accessed via API tokens or OAuth.
- **Environment Variable Injection**: Injects secrets seamlessly into any process.
- **Ephemeral File Mounting**: Mounts secrets as `.env`, JSON, or custom formats via named pipes.[](https://docs.doppler.com/docs/accessing-secrets)
- **Multi-Workplace Support**: Scope logins to directories for managing multiple projects.[](https://docs.doppler.com/docs/cli)
- **Git Integration**: Sync secrets with version-controlled projects.
- **Automatic Updates**: Self-updates via `doppler update`.
- **Shell Completions**: Auto-installed for Bash, Zsh, Fish, etc.
- **Cross-Platform**: Works with any language, framework, or cloud provider.

New in 3.71.0: Improved `--watch` for auto-reloading, enhanced JSON output, and better error handling for network issues.

## Common Commands
Frequently used commands (see `doppler --help` for a full list):[](https://docs.doppler.com/docs/cli)

| Command | Description | Example |
|---------|-------------|---------|
| `login` | Authenticate to Doppler | `doppler login --scope ./` |
| `setup` | Configure project and config | `doppler setup` |
| `run` | Run command with secrets injected | `doppler run -- python app.py` |
| `secrets` | Manage secrets (get, set, delete) | `doppler secrets get API_KEY` |
| `configs` | Manage configs | `doppler configs list` |
| `projects` | Manage projects | `doppler projects create myproj` |
| `environments` | Manage environments | `doppler environments list` |
| `update` | Update CLI to latest version | `doppler update` |
| `completion` | Generate shell completions | `doppler completion bash` |
| `open` | Open Doppler dashboard | `doppler open dashboard` |

### Options
- `--token`: Specify Service Token for CI/CD.
- `--mount`: Mount secrets as a file (e.g., `.env`).
- `--scope`: Set directory scope for config.
- `--json`: Output in JSON format.
- `--watch`: Auto-reload on secret changes (Team plan).

## Advanced Usage
### Authentication
Scoped login for multiple workplaces:
```bash
doppler login --scope ~/projects/backend
```

Service Token for production:
```bash
echo 'dp.st.prd.xxxx' | doppler configure set token --scope /project
```

OIDC for service accounts (niche):
```bash
doppler configure set identity --scope ./ --identity-token <oidc-token>
```

### Project Setup
Pre-configure with `doppler.yaml`:
```yaml
project: myproj
config: dev
```
```bash
doppler setup --config-dir ./ --no-interactive
```

Monorepo setup:
```bash
# Root doppler.yaml
project: myproj
config: dev
# Subdir doppler.yaml
project: myproj
config: api-dev
```

### Secret Management
Get a single secret:
```bash
doppler secrets get API_KEY --plain
```

Set a secret:
```bash
doppler secrets set API_KEY=abc123
```

Bulk import from `.env`:
```bash
doppler secrets upload .env
```

Download secrets as file:
```bash
doppler secrets download --format env > .env.local
```

Mount as ephemeral file:
```bash
doppler run --mount secrets.env -- python app.py
```

Custom format:
```bash
doppler run --mount config.json --mount-format json -- node app.js
```

### Running Commands
Inject secrets:
```bash
doppler run --command 'curl -u $USER:$PASS https://api.example.com'
```

Watch for changes (Team plan):
```bash
doppler run --watch -- npm start
```

### Git Integration
Sync secrets with Git:
```bash
git clone git@server:project.git
cd project
doppler setup
doppler run -- make build
```

### Shell Completions
Install completions:
```bash
doppler completion bash > /usr/local/etc/bash_completion.d/doppler
```

For Fish:
```bash
doppler completion fish > ~/.config/fish/completions/doppler.fish
```

### CI/CD Integration
GitHub Actions with Service Token:
```yaml
- name: Run with Doppler secrets
  env:
    DOPPLER_TOKEN: ${{ secrets.DOPPLER_TOKEN }}
  run: doppler run -- npm test
```

Docker:
```bash
docker run -e DOPPLER_TOKEN=dp.st.prd.xxxx dopplerhq/cli doppler run -- printenv
```

## Niche Usages
### Multi-Workplace Management
Switch workplaces:
```bash
doppler login --scope ~/workplace1
doppler login --scope ~/workplace2
```

List configs:
```bash
doppler configure --all
```

### Scripting
Dynamic secret retrieval:
```bash
API_KEY=$(doppler secrets get API_KEY --plain)
echo "Using API_KEY=$API_KEY"
```

Batch set secrets:
```bash
while IFS='=' read -r key value; do doppler secrets set "$key=$value"; done < secrets.env
```

### With Fzf
Interactive secret selection:
```bash
doppler secrets | jq -r '.[] | .name' | fzf | xargs doppler secrets get --plain
```

### Ephemeral Files
Mount as YAML (niche):
```bash
doppler run --mount config.yaml --mount-format yaml -- python app.py
```

### CI/CD Advanced
GitLab CI:
```yaml
test:
  script:
    - echo "$DOPPLER_TOKEN" | doppler configure set token
    - doppler run -- pytest
```

Kubernetes secrets:
```bash
doppler secrets download --format json | kubectl create secret generic app-secrets --from-file=secrets.json
```

### Debugging
Verbose output:
```bash
doppler --debug run -- printenv
```

Print config:
```bash
doppler --print-config
```

### Edge Cases
- Custom API host: `--api-host https://custom.doppler.com`.
- Disable timeouts: `--no-timeout` for slow networks.
- Skip TLS: `--no-verify-tls` (not recommended).
- Custom DNS: `--dns-resolver-address 8.8.8.8:53`.
- Fallback shell: `--command 'bash -c "command"'` if `$SHELL` fails.

## Practical Examples
1. **Authenticate and Setup**:
   ```bash
   doppler login
   doppler setup
   ```

2. **Run with Secrets**:
   ```bash
   doppler run -- python app.py
   ```

3. **Get Single Secret**:
   ```bash
   doppler secrets get API_KEY --plain
   ```

4. **Set Secret**:
   ```bash
   doppler secrets set DB_PASS=secure123
   ```

5. **Mount Secrets as File**:
   ```bash
   doppler run --mount .env -- node app.js
   ```

6. **Interactive Selection with Fzf**:
   ```bash
   doppler secrets | jq -r '.[] | .name' | fzf | xargs doppler secrets get --plain
   ```

7. **Git Sync and Run**:
   ```bash
   git pull origin main
   doppler setup
   doppler run -- make build
   ```

8. **Watch for Changes**:
   ```bash
   doppler run --watch -- npm start
   ```

9. **CI/CD with Docker**:
   ```bash
   docker run -e DOPPLER_TOKEN=dp.st.prd.xxxx dopplerhq/cli doppler run -- printenv
   ```

10. **Bulk Import**:
    ```bash
    doppler secrets upload .env
    ```

11. **Custom Format Mount**:
    ```bash
    doppler run --mount config.yaml --mount-format yaml -- python app.py
    ```

12. **Debug Config**:
    ```bash
    doppler --print-config
    ```

## Performance Tips
- **Narrow Scope**: Use `--scope` to limit config access.
- **Cache Tokens**: Store Service Tokens securely in CI/CD.
- **Ephemeral Files**: Prefer `--mount` over environment variables for file-based apps.[](https://docs.doppler.com/docs/accessing-secrets)
- **Watch Sparingly**: Use `--watch` only for long-running processes.
- **Minimize Secrets**: Reduce secret count for faster injection.
- **Network**: Use `--dns-resolver-address` for reliable DNS.
- **Monorepo**: Use multiple `doppler.yaml` files for subprojects.

## Troubleshooting
- **Auth Errors**: Run `doppler login` again; check token scope with `doppler configure --all`.
- **No Secrets**: Verify `doppler setup` and project/config selection.
- **Mount Fails**: Avoid iCloud-synced dirs; use `--mount-format`.[](https://docs.doppler.com/docs/accessing-secrets)
- **Network Issues**: Increase `--attempts` or use `--no-timeout`.
- **Shell Issues**: Specify shell: `--command 'bash -c "command"'`.
- **Completions Missing**: Re-run `doppler completion <shell>`.
- **Windows Scoop**: Use `winget` for updates.[](https://docs.doppler.com/docs/cli)

Niche: Debug with `--debug` or log API calls: `doppler --debug run`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: `source <(doppler completion bash)`.
  - Fish: `doppler completion fish > ~/.config/fish/completions/doppler.fish`.

- **Fzf**:
  ```bash
  doppler secrets | jq -r '.[] | .name' | fzf | xargs doppler secrets get --plain
  ```

- **Ripgrep**:
  ```bash
  rg --files | fzf | xargs doppler run --command 'cat {}'
  ```

- **Fd**:
  ```bash
  fd -t f | fzf | xargs doppler run --command 'cat {}'
  ```

- **Git**:
  ```bash
  git pull
  doppler setup
  doppler run -- make test
  ```

- **Editors**:
  - Vim: `doppler secrets get API_KEY --plain > .env.local`.
  - VS Code: Task: `"command": "doppler run -- npm start"`.
  - Emacs: Script with `doppler run`.

- **Advanced Niche**:
  - Kubernetes: `doppler secrets download --format json | kubectl create secret generic ...`.
  - Docker: Mount config: `docker run -v $HOME/.doppler:/root/.doppler ...`.
  - CI/CD: Jenkins/GitLab with Service Tokens.
  - Webapp.io: `doppler -t $DOPPLER_TOKEN run -- printenv`.[](https://docs.webapp.io/integrations/doppler)

## Resources
- [Official Documentation](https://docs.doppler.com/)[](https://docs.doppler.com/docs/cli)
- [GitHub Repository](https://github.com/DopplerHQ/cli)[](https://github.com/DopplerHQ/cli)
- [Install Guide](https://docs.doppler.com/docs/install-cli)[](https://docs.doppler.com/docs/install-cli)
- Run `doppler --help` or `doppler [command] -h` for quick reference.
- Community: GitHub Discussions, Doppler Community Forum.

## Conclusion
`doppler` is a modern, secure CLI for managing secrets across environments, offering flexibility and ease of use for developers and DevOps teams. This guide covers its full range, from basic setup to advanced integrations and niche use cases, empowering you to streamline secret management. Experiment with the examples to integrate `doppler` into your workflows![](https://docs.doppler.com/docs/cli)[](https://docs.doppler.com/docs/install-cli)[](https://docs.doppler.com/docs/accessing-secrets)