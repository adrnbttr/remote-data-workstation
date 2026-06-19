<div align="center">

# 🛰️ Remote Data Workstation

**A secure, reproducible data-engineering workstation —**<br>
**provisioned with Ansible, co-located with your database.**

[![CI](https://github.com/adrnbttr/remote-data-workstation/actions/workflows/ci.yml/badge.svg)](https://github.com/adrnbttr/remote-data-workstation/actions/workflows/ci.yml)
[![Security](https://github.com/adrnbttr/remote-data-workstation/actions/workflows/security.yml/badge.svg)](https://github.com/adrnbttr/remote-data-workstation/actions/workflows/security.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

</div>

The idea is simple: instead of pulling large datasets down to a laptop over the
internet, run the development environment on a VM that sits **next to the
database** (same region / private network). Heavy work stays close to the data,
the laptop becomes a thin client you SSH into — and iteration gets dramatically
faster.

See [docs/architecture.md](./docs/architecture.md) for the reasoning behind the
design.

## How it works

The local machine becomes a thin client: terminal emulator plus an SSH key.
Everything else — code, editor, AI tooling, containers, jobs — lives on the VM,
which sits in the same region as the database.

```
  laptop (kitty + ssh)  ──ssh──▶  remote workstation (VM)  ──private net──▶  database
        thin client                code · nvim · docker · jobs              (co-located)
```

## Layout

```
ansible.cfg
inventory/             hosts + group_vars (vault-encrypted secrets)
playbooks/
  site.yml             personal profile — the base workstation
  data-workstation.yml example profile — base + a generic data overlay
roles/                 one role per concern (see below)
scripts/               local helper scripts (run on your laptop)
docs/                  design notes
```

## What it sets up

The base workstation (`site.yml`) is built from focused roles:

| Role | Responsibility |
| --- | --- |
| `user` | Non-root user with sudo and authorized SSH keys |
| `hardening` | SSH hardening, default-deny firewall, fail2ban, auto security updates, Tailscale |
| `dotfiles` | Clone & stow dotfiles, oh-my-zsh and zsh plugins |
| `dev_tools` | Python (pyenv/uv/poetry), Node (nvm), Docker, modern CLIs, gh, gcloud |
| `remote_shell` | mosh and kitty terminfo for a comfortable SSH experience |
| `ai_tools` | AI CLI tooling (authentication stays manual) |

The example profile (`data-workstation.yml`) adds `data_workstation_example`, a
generic overlay showing how a project layer sits on top of the base.

The `scripts/` helpers (`de-session-remote`, `de-desk`) run on your **laptop** to
drive the remote workflow over SSH — see [scripts/README.md](./scripts/README.md).

## Security model

- **No public SSH** — the VM is reached over a private Tailscale network.
- **No private keys on the VM** — Git uses SSH agent forwarding from the laptop.
- **Secrets stay encrypted** — managed with Ansible Vault, never committed.

## Quickstart

```bash
# 1. Install dependencies
ansible-galaxy collection install -r requirements.yml

# 2. Point the inventory at your VM (reached over Tailscale)
cp inventory/hosts.example.ini inventory/hosts.ini   # then edit

# 3. Provision the base workstation...
ansible-playbook playbooks/site.yml

# ...or the example profile with the data overlay
ansible-playbook playbooks/data-workstation.yml
```

## Quality & CI

Every pull request is gated by automated checks: `yamllint`, `ansible-lint`,
playbook syntax checks, `gitleaks` secret scanning, and `commitlint`
(Conventional Commits). The same checks run locally via pre-commit — see
[CONTRIBUTING.md](./CONTRIBUTING.md).

## License

MIT — see [LICENSE](./LICENSE).
