# nix-setup

Unified system bootstrap for Linux (Debian/Ubuntu) and macOS. A single `ansible-pull` command installs packages, deploys shell config (ZSH, aliases, vim), sets your default shell, and on Linux sets up a self-updating cron job to keep things in sync.

## What it does

| Task | Linux | macOS |
|---|---|---|
| Install packages | apt | Homebrew |
| Deploy dotfiles (.zshrc, .aliases, .vimrc) | ✅ | ✅ |
| Set ZSH as default shell | ✅ | ✅ |
| Create `ansible` system user + sudoers | ✅ | — |
| Self-updating cron job (every 10 min) | ✅ | — |

## Prerequisites

### Linux (Debian/Ubuntu)
Install Ansible:
```bash
sudo apt update && sudo apt install -y ansible
```

### macOS
Install Homebrew, then Ansible:
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew install ansible
```

## Bootstrap

### Linux
Run as root (or with sudo):
```bash
sudo ansible-pull -U https://github.com/rscameron42/nix-setup.git
```

After first run, a cron job is installed under the `ansible` user to re-run every 10 minutes and pick up any repo changes automatically.

### macOS
```bash
ansible-pull -U https://github.com/rscameron42/nix-setup.git
```

On macOS there is no automatic cron job — re-run the command above manually to pull updates.

## Configuration

The target username is set in `local.yml`:
```yaml
vars:
  target_user: ron
```

Change `ron` to your username before running if needed.

## Repo structure

```
nix-setup/
├── local.yml               # main playbook
├── files/
│   ├── .zshrc              # ZSH config + custom prompt
│   ├── .aliases            # cross-platform aliases
│   ├── .aliases.linux      # Linux-specific aliases
│   ├── .aliases.darwin     # macOS-specific aliases
│   ├── .vimrc              # vim config
│   └── sudoers_ansible     # sudoers for ansible system user
├── templates/
│   └── sudoers_user.j2     # sudoers template for target user
├── tasks/
│   ├── packages.yml        # package installation
│   ├── users.yml           # user + sudoers setup (Linux only)
│   ├── dotfiles.yml        # dotfile deployment
│   ├── shell.yml           # set default shell
│   └── cron.yml            # self-update cron job (Linux only)
└── README.md
```

## Prompt

The ZSH prompt shows `user@host` with current path, exit status (✓ or error code), and SSH source IP when connected remotely. The right prompt shows date and time.
