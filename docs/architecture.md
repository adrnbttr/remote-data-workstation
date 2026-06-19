# Architecture

## The problem: locality, not horsepower

Data-engineering pipelines often feel slow for a reason that has nothing to do
with the tools or the data volume. The real bottleneck is **locality**: the code
runs on a laptop while the database lives in the cloud. Every step downloads
data over the internet, transforms it locally, and writes it back.

The same transformation that takes tens of minutes when streamed over the
internet can take a handful of seconds when it runs **inside** the database, or
on a machine sitting right next to it. The network round-trips dominate
everything else.

## The idea: bring the compute to the data

Put the development environment on a VM in the **same region / private network**
as the database. Heavy work then happens over a millisecond-latency private link
instead of the public internet.

```
  laptop (kitty + ssh)  ──ssh──▶  remote workstation (VM)  ──private net──▶  database
        thin client                code · nvim · docker · jobs              (co-located)
```

The laptop becomes a **thin client**: a terminal emulator and an SSH key. The
code, the editor, the containers, the AI tooling, and the jobs all live on the
VM. Your local internet connection only needs to carry terminal traffic (a few
KB/s), not gigabytes of data.

## Why these choices

- **Ansible, not a snowflake VM.** The whole environment is described as code, so
  it is reproducible and reviewable. The VM itself is provisioned elsewhere (e.g.
  Terraform); Ansible configures everything inside it.
- **A reusable base + an overlay.** The base ("socle") roles are generic and
  carry nothing project-specific. A project layer is added on top as an overlay —
  the `data_workstation_example` role demonstrates the pattern.
- **Security first.** No public SSH (reached over Tailscale), no private keys on
  the VM (Git uses SSH agent forwarding), and secrets stay encrypted with Ansible
  Vault. The VM holds real access, so it is hardened by default.

## The remote workflow

Working day-to-day stays close to a local experience. `de-session-remote` (run on
the laptop) opens one terminal tab per remote git worktree, each SSH-connected
and running the editor. `de-desk` forwards a web app from the VM to the local
browser through a secure SSH tunnel, so nothing is ever exposed publicly.
