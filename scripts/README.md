# Local helper scripts

These scripts run on your **local machine**, not on the workstation. Kitty is a
local GUI terminal, so the session/tunnel helpers that drive it must live next to
it. They complete the remote workflow that the Ansible roles set up on the VM.

## Install

```bash
ln -s "$PWD/scripts/de-session-remote" ~/.local/bin/de-session-remote
ln -s "$PWD/scripts/de-desk" ~/.local/bin/de-desk
```

Make sure `~/.local/bin` is on your `PATH`.

## Configuration

All helpers read the workstation's SSH host from `$RDW_HOST` (or a positional
argument). Define it once, e.g. in your shell rc or as an SSH alias:

```bash
export RDW_HOST=workstation
```

## Scripts

| Script               | What it does                                                        |
| -------------------- | ------------------------------------------------------------------- |
| `de-session-remote`  | Opens one kitty tab per remote git worktree, each running nvim.     |
| `de-desk`            | Forwards a remote web app to your browser via a secure SSH tunnel.  |
