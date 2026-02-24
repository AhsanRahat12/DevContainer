# DevContainer

Project-specific dev environment using [DevPod](https://devpod.sh), [mise](https://mise.jdx.dev), and a custom Dockerfile.

> **Related repo:** [dotfiles](https://github.com/AhsanRahat12/dotfiles) — personal global tooling and dotfiles applied automatically inside every container.

---

## Overview

This repo defines the **project-level** dev environment — the tools and setup that every developer working on this project gets, regardless of their personal machine.

Personal tools (chezmoi, bat, neovim, etc.) are handled separately via the [dotfiles repo](https://github.com/AhsanRahat12/dotfiles) and are applied automatically when the container starts.

---

## Directory Structure

```
~/Projects/devcontainer/
├── .devcontainer/
│   ├── devcontainer.json       → points to Dockerfile, sets postCreateCommand
│   └── Dockerfile              → Ubuntu 24.04 + mise binary
├── scripts/
│   └── setup                  → postCreateCommand: trusts and installs project tools
├── mise.toml                  → project-scoped tools (kubectl, etc.)
└── hello.py
```

---

## Container Boot Flow

```
devpod up .
    ↓
Dockerfile builds
└── mise binary available at /usr/local/bin/mise
    ↓
postCreateCommand runs (scripts/setup)
└── mise trust → mise install → project tools installed (kubectl)
    ↓
DevPod clones dotfiles repo & runs setup script
└── chezmoi apply runs
    └── mise binary downloaded to ~/.local/bin/mise
    └── ~/.config/mise/config.toml placed
    └── chezmoi script runs → personal tools installed (chezmoi, bat)
    ↓
Shell starts
└── All tools available on PATH
```

---

## How to Use on a New Machine

Make sure you have [DevPod](https://devpod.sh) installed, then:

```bash
# Load YubiKey for SSH auth
ssh-add -K

# Spin up the container
cd ~/Projects/devcontainer
devpod up .

# SSH in
devpod ssh devcontainer
```

---

## How to Add a New Project Tool

Project tools are available to everyone working in this repo, but only inside the project directory.

1. Edit `mise.toml` in the project root:
```toml
[tools]
kubectl = "latest"
helm = "latest"   # ← add new tools here
```

2. Run:
```bash
mise install
```

To find available tool names, run `mise registry` or visit [mise.jdx.dev](https://mise.jdx.dev).

> For personal tools you want everywhere (not project-specific), add them to the [dotfiles repo](https://github.com/AhsanRahat12/dotfiles) instead.

---

## Two-Tier Tooling System

| Layer | Location | Scope | Examples |
|---|---|---|---|
| **Project tools** | `mise.toml` (this repo) | Whole team, project directory only | kubectl, helm, terraform |
| **Personal global** | `dot_config/mise/config.toml` (dotfiles repo) | You, everywhere | chezmoi, bat, neovim |
