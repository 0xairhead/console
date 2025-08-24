# `pass`

## Introduction
`pass` is a command-line password manager designed for simplicity, security, and integration with Unix tools. It stores passwords encrypted with GPG in a directory hierarchy, leveraging the Unix philosophy of small, composable tools. As of the latest version (1.7.4, released February 2021), `pass` supports features like Git integration, password generation, and clipboard copying, making it ideal for power users, developers, and sysadmins who prefer a lightweight, scriptable solution over GUI-based password managers.

`pass` excels in scenarios requiring secure password storage, easy scripting, and integration with tools like `gpg`, `git`, or `fzf`. It’s particularly valuable for those managing credentials across servers, CI/CD pipelines, or personal workflows, offering robust encryption and flexibility without relying on external services.

## Installation
`pass` is available on Linux, macOS, and Windows (via WSL or native ports). Below are common installation methods, updated for the latest practices, including containerized options for reproducibility:

### Linux
- **Using Package Managers**:
  - Ubuntu/Debian: `sudo apt install pass`
  - Fedora: `sudo dnf install pass`
  - Arch Linux: `sudo pacman -S pass`
  - Nix: `nix-env -i pass`
- **From Source** (requires `git`, `make`, and `gpg`):
  ```bash
  git clone https://git.zx2c4.com/password-store
  cd password-store
  sudo make install
  ```
- **Binary Download**: Pre-built binaries available from [passwordstore.org](https://www.passwordstore.org/) or [Git Source](https://git.zx2c4.com/password-store).
- **Docker**: For isolated environments:
  ```bash
  docker run --rm -v $HOME/.password-store:/root/.password-store -it tianon/pass pass
  ```

### macOS
- **Homebrew**:
  ```bash
  brew install pass
  ```
- **MacPorts**:
  ```bash
  sudo port install pass
  ```
- **Nix**: `nix-env -i pass`

### Windows
- **Scoop**:
  ```bash
  scoop install pass
  ```
- **WSL**: Install via Linux methods in Windows Subsystem for Linux.
- **Native (Manual)**: Use GPG4Win and download `pass` source, then run via Bash (e.g., Git Bash or Cygwin). Alternatively, use the [pass-winmenu](https://github.com/cortex/pass-winmenu) port.

### Dependencies
- **GPG**: Required for encryption (`gpg2` recommended).
- **Git**: Optional for version control.
- **Tree**: Optional for directory visualization (`pass show -t`).
- **xclip`/`wl-clipboard`**: For clipboard on Linux.
- **pbcopy`/`pbpaste`**: For clipboard on macOS.

### Verify Installation
Run `pass version` to confirm installation. Example output:
```
pass 1.7.4
```
Ensure GPG is set up: `gpg --list-keys`. If not, generate a key:
```bash
gpg --gen-key
```

## Basic Usage
`pass` manages passwords in `~/.password-store` (or `$PASSWORD_STORE_DIR`), storing each as a GPG-encrypted file. The basic syntax is:
```bash
pass [COMMAND] [OPTIONS] [PASS-NAME]
```

- **COMMAND**: Actions like `init`, `insert`, `show`, `edit`, `rm`, etc.
- **PASS-NAME**: Path to the password (e.g., `work/email`).
- **OPTIONS**: Flags to modify behavior (e.g., clipboard, multiline).

### Simple Example
Initialize the password store with your GPG key ID:
```bash
pass init "your-gpg-key-id"
```

Insert a password:
```bash
pass insert work/email
Enter password for work/email: [your-password]
```

Show a password:
```bash
pass show work/email
```
Output: The decrypted password (e.g., `your-password`).

Copy to clipboard (Linux/macOS):
```bash
pass -c work/email
```

## Key Features
`pass`’s strength lies in its simplicity and security:
- **GPG Encryption**: Passwords are encrypted with GPG, ensuring strong security.
- **Hierarchical Storage**: Organizes passwords in a directory structure (e.g., `work/email`, `personal/bank`).
- **Git Integration**: Tracks changes with `git` for versioning and syncing.
- **Password Generation**: Built-in random password generator.
- **Clipboard Support**: Copies passwords securely with a timeout.
- **Extensible**: Easy to script and integrate with tools like `fzf`, `dmenu`, or `rofi`.
- **Cross-Platform**: Works anywhere GPG and a shell are available.

New in 1.7.4: Improved Git handling, better error messages, and support for custom extensions.

## Common Commands
Frequently used commands (see `pass --help` for a full list):

| Command | Description | Example |
|---------|-------------|---------|
| `init` | Initialize password store | `pass init "key-id"` |
| `insert` | Add a new password | `pass insert work/email` |
| `show` | Display a password | `pass show work/email` |
| `-c`, `--clip` | Copy password to clipboard | `pass -c work/email` |
| `edit` | Edit a password | `pass edit work/email` |
| `rm` | Remove a password | `pass rm work/email` |
| `mv` | Move/rename a password | `pass mv work/email work/mail` |
| `cp` | Copy a password | `pass cp work/email work/email-backup` |
| `generate` | Generate random password | `pass generate work/new 20` |
| `ls` | List passwords | `pass ls` |
| `grep` | Search passwords | `pass grep "email"` |
| `git` | Run Git commands on store | `pass git status` |

### Options
- `-m`: Multiline input for `insert` or `edit`.
- `-f`: Force overwrite for `insert` or `generate`.
- `-i`: In-place generation for `generate`.
- `-t`: Show tree structure with `tree` (if installed).

## Advanced Usage
### Initialization
Initialize with multiple GPG keys (for team sharing):
```bash
pass init "key-id1 key-id2"
```

Custom store location:
```bash
export PASSWORD_STORE_DIR=~/my-secrets
pass init "key-id"
```

### Password Organization
Nested structure:
```bash
pass insert work/dev/server1
pass insert work/dev/server2
pass ls
```
Output:
```
Password Store
└── work
    └── dev
        ├── server1
        └── server2
```

### Multiline Passwords
Store metadata (e.g., username, URL):
```bash
pass insert -m work/email
```
Input:
```
password123
username: user@example.com
url: https://mail.example.com
```
Show specific line:
```bash
pass show work/email | grep username
```

### Password Generation
Generate a 20-character password:
```bash
pass generate work/new 20
```

Exclude special characters:
```bash
pass generate -n work/new 20  # Alphanumeric only
```

In-place update:
```bash
pass generate -i work/old 15
```

### Git Integration
Initialize Git:
```bash
pass git init
pass git remote add origin git@server:repo.git
```

Track changes:
```bash
pass insert work/new
pass git add . && pass git commit -m "Added work/new"
pass git push
```

Sync across devices:
```bash
pass git pull
```

### Clipboard Management
Copy with timeout (default 45s, controlled by `PASSWORD_STORE_CLIP_TIME`):
```bash
pass -c work/email
```

Copy specific line (niche):
```bash
pass show work/email | grep username | cut -d' ' -f2 | xclip -selection clipboard
```

### Searching and Filtering
Search passwords:
```bash
pass grep "email"
```

With `fzf` for interactive selection:
```bash
pass ls | fzf | xargs pass show
```

### Editing
Edit directly (uses `$EDITOR`):
```bash
pass edit work/email
```

### Extensions
Install extensions (e.g., `pass-otp` for TOTP):
```bash
sudo apt install pass-extension-otp
```

Use OTP:
```bash
pass otp insert work/2fa
pass otp work/2fa
```

Custom extensions in `$PASSWORD_STORE_EXTENSIONS_DIR` (default: `~/.password-store/.extensions`).

### Syncing and Sharing
Team sharing via GPG:
- Share store with multiple keys.
- Re-encrypt for new keys:
  ```bash
  pass init --force "key-id1 key-id2"
  ```

Sync with private Git repo or cloud storage (e.g., Dropbox with `rclone`):
```bash
rclone sync ~/.password-store remote:password-store
```

## Niche Usages
### Scripting
Generate and copy:
```bash
pass generate -c work/temp 20
```

Extract metadata:
```bash
pass show work/email | awk '/username/ {print $2}'
```

Batch import from CSV:
```bash
#!/bin/bash
while IFS=, read -r name pass; do
  echo "$pass" | pass insert -f "$name"
done < passwords.csv
```

### CI/CD Integration
Use in GitHub Actions (ensure GPG key import):
```yaml
- name: Retrieve secrets
  run: |
    echo "${{ secrets.GPG_PRIVATE_KEY }}" | gpg --import
    pass show ci/secret-key
```

### With Fzf
Interactive password selection:
```bash
pass ls | fzf --preview 'pass show {}' | xargs pass -c
```

### TOTP (Two-Factor Authentication)
Generate OTP code:
```bash
pass otp work/2fa
```

Copy OTP to clipboard:
```bash
pass otp -c work/2fa
```

### Browser Integration
Use `passff` (Firefox) or `browserpass` (Chrome):
```bash
pass insert web/example.com
```
Configure browser extension to read `~/.password-store`.

### Mobile Access
Sync to Android with [Password Store app](https://github.com/android-password-store/Android-Password-Store):
```bash
pass git push origin main
```

### GPG Key Management
Rotate keys:
```bash
pass init --force "new-key-id"
```

Export/import store:
```bash
tar -czf secrets.tar.gz ~/.password-store
```

### Backup and Recovery
Backup:
```bash
pass git archive --format tar.gz main > pass-backup.tar.gz
```

Restore:
```bash
tar -xzf pass-backup.tar.gz -C ~/.password-store
pass init "key-id"
```

### Edge Cases
- Custom GPG binary: `export PASSWORD_STORE_GPG_OPTS="--gpg-binary gpg2"`.
- Debug GPG: `pass show work/email --debug`.
- Non-standard chars in passwords: Use `-m` for multiline to avoid shell issues.
- Large stores: Use `tree -C ~/.password-store` for visualization.
- Temporary store: `PASSWORD_STORE_DIR=/tmp/pass pass init "key-id"`.

## Practical Examples
1. **Add New Password**:
   ```bash
   pass insert personal/bank
   ```

2. **Generate and Copy**:
   ```bash
   pass generate -c work/api 25
   ```

3. **List with Tree**:
   ```bash
   pass ls
   ```

4. **Search with Fzf**:
   ```bash
   pass ls | fzf | xargs pass show
   ```

5. **Edit Multiline**:
   ```bash
   pass insert -m work/vpn
   # Input: password\nusername: user\nurl: vpn.example.com
   ```

6. **Git Sync**:
   ```bash
   pass git pull && pass insert new/pass && pass git push
   ```

7. **TOTP Setup**:
   ```bash
   pass otp insert work/2fa
   pass otp -c work/2fa
   ```

8. **Extract Username**:
   ```bash
   pass show work/email | grep username | cut -d' ' -f2
   ```

9. **Batch Remove**:
   ```bash
   pass ls work | grep old | xargs -I {} pass rm work/{}
   ```

10. **CI/CD Secret**:
    ```bash
    pass show ci/secret | xargs -I {} echo "API_KEY={}" >> $GITHUB_ENV
    ```

11. **Backup Store**:
    ```bash
    tar -czf ~/pass-backup-$(date +%F).tar.gz ~/.password-store
    ```

12. **Team Sharing**:
    ```bash
    pass init --force "team-key1 team-key2"
    pass git push
    ```

## Performance Tips
- **Small Store**: Keep `~/.password-store` lean; avoid thousands of entries.
- **Git**: Use lightweight repos; avoid large binary files.
- **GPG**: Cache passphrase with `gpg-agent` for faster decryption.
- **Fzf**: Pre-filter with `pass ls | grep pattern` for large stores.
- **Scripting**: Use `--clip` or `show` in scripts to avoid UI.
- **Sync**: Use `git pull --rebase` for fast syncs.
- **Large Stores**: Index with `find ~/.password-store -type f` for custom scripts.

Niche: For massive stores, consider splitting into multiple stores (`work/`, `personal/`) with separate `PASSWORD_STORE_DIR`.

## Troubleshooting
- **GPG Errors**: Verify key with `gpg --list-keys`; re-import if needed.
- **No Passwords Found**: Check `PASSWORD_STORE_DIR`, ensure `pass init`.
- **Clipboard Issues**: Install `xclip`/`wl-clipboard` (Linux) or verify `pbcopy` (macOS).
- **Git Sync Fails**: Check `pass git remote -v` and SSH keys.
- **Slow Decryption**: Enable `gpg-agent` caching: `echo "default-cache-ttl 3600" >> ~/.gnupg/gpg-agent.conf`.
- **Permission Denied**: Ensure `~/.password-store` permissions: `chmod 700 ~/.password-store`.
- **Extension Errors**: Verify extension installation (e.g., `pass-otp`).

Niche: Debug with `PASS_DEBUG=1 pass show`.

## Integration with Other Tools
- **Shell**:
  - Bash/Zsh: Source completions: `source /usr/share/pass/pass.bash-completion`.
  - Fish: `pass fish | source`.

- **Fzf**:
  ```bash
  pass ls | fzf --preview 'pass show {}' | xargs pass -c
  ```

- **Ripgrep**:
  ```bash
  pass grep "email" | fzf | awk '{print $1}' | xargs pass show
  ```

- **Fd**:
  ```bash
  fd . ~/.password-store | sed 's|.*/.password-store/||;s|\.gpg$||' | fzf | xargs pass show
  ```

- **Git**:
  ```bash
  pass git log --oneline | fzf | awk '{print $1}' | xargs pass git show
  ```

- **Editors**:
  - Vim: Use `pass edit` with `$EDITOR=vim`.
  - Emacs: `password-store.el` for integration.
  - VS Code: Script with `pass show` in tasks.

- **Browser**:
  - Firefox: `passff` extension.
  - Chrome: `browserpass`.

- **Advanced Niche**:
  - Kubernetes: Store secrets in `pass` and inject: `pass show k8s/secret | kubectl apply -f -`.
  - Docker: Mount store: `docker run -v $HOME/.password-store:/root/.password-store pass show secret`.
  - CI/CD: Use `pass` for secrets in Jenkins/GitLab pipelines.
  - Mobile: Sync with Termux or Android Password Store app.

## Resources
- [Official Website](https://www.passwordstore.org/)
- [Git Repository](https://git.zx2c4.com/password-store)
- [Man Page](https://git.zx2c4.com/password-store/about/?id=HEAD)
- Run `pass --help` for quick reference.
- Community: GitHub Discussions, r/commandline, Stack Overflow.

## Conclusion
`pass` is a minimalist yet powerful password manager, leveraging GPG and Unix tools for secure, scriptable credential management. This comprehensive guide covers its full range, from basic usage to advanced integrations and niche scenarios, empowering you to manage passwords efficiently in any workflow. Experiment with the examples to integrate `pass` into your daily operations!