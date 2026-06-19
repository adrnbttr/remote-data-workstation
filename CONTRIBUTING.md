# Contributing

This repository follows a small set of conventions so the history stays clean
and every change is automatically checked.

## Workflow

1. Branch off `main` (`feat/...`, `fix/...`, `chore/...`, `docs/...`, `ci/...`).
2. Make focused, atomic commits.
3. Open a pull request. CI must be green before it can be merged.
4. Merge **without squashing** to preserve the commit granularity.

`main` is protected: no force-push, no deletion, and all status checks must pass.

## Commit messages

Commits follow the [Conventional Commits](https://www.conventionalcommits.org)
specification and are enforced by `commitlint`:

```
<type>(<optional scope>): <description>
```

Common types: `feat`, `fix`, `docs`, `chore`, `ci`, `refactor`, `test`.

## Local checks

Install the pre-commit hooks to run the same checks as CI before each commit:

```bash
pip install pre-commit
pre-commit install
pre-commit run --all-files
```

## Continuous integration

| Check          | Purpose                                  |
| -------------- | ---------------------------------------- |
| `yamllint`     | YAML style                               |
| `ansible-lint` | Ansible best practices                   |
| `syntax-check` | Playbooks parse correctly                |
| `gitleaks`     | No secrets committed                     |
| `commitlint`   | Conventional Commits on every PR commit  |
