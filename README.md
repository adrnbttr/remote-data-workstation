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

> 🚧 Work in progress — see the roadmap below.

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
inventory/            hosts + group_vars (vault-encrypted secrets)
playbooks/
  site.yml            personal profile — the base workstation
  data-workstation.yml example profile — base + a generic data overlay
roles/                one role per concern (see roadmap)
```

## Roadmap

- [ ] `hardening` — SSH hardening, default-deny firewall, fail2ban, Tailscale
- [ ] `user` — non-root user with sudo and authorized keys
- [ ] `dotfiles` — clone & stow dotfiles, oh-my-zsh
- [ ] `dev_tools` — zsh, neovim, python/node toolchains, docker, modern CLIs
- [ ] `remote_shell` — mosh, kitty terminfo, remote session helpers
- [ ] `ai_tools` — AI CLI tooling
- [ ] `data_workstation_example` — generic data overlay (showcase)

## Security model

- **No public SSH** — the VM is reached over a private Tailscale network.
- **No private keys on the VM** — Git uses SSH agent forwarding from the laptop.
- **Secrets stay encrypted** — managed with Ansible Vault, never committed.

## Quickstart

```bash
ansible-galaxy collection install -r requirements.yml
cp inventory/hosts.example.ini inventory/hosts.ini   # then edit
ansible-playbook playbooks/site.yml
```

## Quality & CI

Every pull request is gated by automated checks: `yamllint`, `ansible-lint`,
playbook syntax checks, `gitleaks` secret scanning, and `commitlint`
(Conventional Commits). The same checks run locally via pre-commit — see
[CONTRIBUTING.md](./CONTRIBUTING.md).

## License

MIT — see [LICENSE](./LICENSE).
