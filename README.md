# Build an Ansible EE to run the playbooks

## Development

Git commits run [pre-commit](https://pre-commit.com/) hooks (YAML, Ansible, gitleaks, shellcheck, markdown).
Tools install with `pip install --user` and pre-commit’s cache under `~/.cache/pre-commit/`.

[`roles/archive/`](roles/archive/) is excluded from every hook. OpenSCAP playbooks under [`hardening/`](hardening/)
are excluded from ansible-lint and yamllint.

```bash
python3 -m pip install --user -r requirements-dev.txt
export PATH="$HOME/.local/bin:$PATH"   # add to shell profile if needed
pre-commit install
pre-commit run --all-files             # first-time / CI-style check
```

Verify: `pre-commit --version`, `command -v pre-commit`, `ansible-lint --version`.

Lint config: [`.yamllint.yml`](.yamllint.yml) (120 columns, comment spacing), [`.ansible-lint`](.ansible-lint)
(`moderate`; `hardening/` and `roles/archive/` excluded from ansible-lint; `hardening/` excluded from yamllint),
[`.markdownlint-cli2.yaml`](.markdownlint-cli2.yaml) (MD013 on prose, 120 columns). **MD031** / **MD040** require blank
lines around fenced blocks and a language tag on each fence.

Ansible-lint uses the `ansible-lint` from your user `pip` install (`language: system` hook).

## Pre-requisites

1. Podman or Docker installed on the host
2. Ansible Navigator and Ansible Builder installed on the host

### Installation Links

- [Install ansible navigator](https://ansible-navigator.readthedocs.io/en/latest/installation/)
- [Install ansible builder](https://ansible-builder.readthedocs.io/en/stable/installation/)

## Instructions

1. Build the environment, named ee_test:v1

```bash
ansible-builder build -t ee_test:v1
```

2. Run a playbook using that environment

```bash
ansible-navigator run -m stdout --eei ee_test:v1 <playbook name>.yml -e ansible_become_password=<password>
```

A more complex example for running the same playbook:

```bash
ansible-navigator run -m stdout --eei ee_test:v1 <playbook name>.yml --vault-password-file secret.txt --limit="rhel86*"
```
