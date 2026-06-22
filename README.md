# Ansible

Personal Ansible automation for my homelab infrastructure.

This is my central place for homelab automation — one task, one branch, one PR at a time. The `main` branch holds the repository skeleton, shared config, and workflow docs. Every piece of real automation (playbooks, roles, inventory entries) lands via a dedicated task branch and merges when it is tested and ready.

## About

I use this repo to manage servers, VMs, and services in my personal homelab. Rather than a monolithic dump of playbooks, I keep `main` lean and stable, and treat each automation task as a small, reviewable unit of work. That makes it easier to test changes, roll back if needed, and grow the repo without losing clarity.

**Current status:** scaffold phase — structure and tooling are in place; playbooks and roles will arrive on task branches.

## Repository structure

```
Ansible/
├── ansible.cfg              # Ansible defaults (inventory path, roles, etc.)
├── requirements.yml         # Galaxy collections (add on task branches)
├── inventories/
│   └── homelab/
│       ├── hosts.yml        # Host and group definitions
│       ├── group_vars/      # Variables shared across groups
│       └── host_vars/       # Per-host overrides
├── playbooks/               # Playbooks (added via task branches)
├── roles/                   # Custom roles (added via task branches)
└── .github/workflows/       # CI (ansible-lint on PRs)
```

| Path | Purpose |
|------|---------|
| `inventories/homelab/` | Primary homelab inventory and variables |
| `playbooks/` | Standalone playbooks for specific tasks |
| `roles/` | Reusable role definitions |
| `ansible.cfg` | Repo-wide Ansible configuration |
| `requirements.yml` | External collection dependencies |

## Getting started

**Prerequisites:** Python 3.10+ and pip.

```bash
git clone https://github.com/<your-username>/Ansible.git
cd Ansible

pip install ansible-core ansible-lint

# Optional: install collections when requirements.yml lists them
ansible-galaxy collection install -r requirements.yml
```

Verify the setup:

```bash
ansible-inventory --list
ansible-lint
```

## Branch workflow

Every automation task gets its own branch. Nothing merges to `main` until it is complete and tested.

### Branch naming

| Prefix | Use |
|--------|-----|
| `task/<short-description>` | New automation (playbook, role, inventory entry) |
| `fix/<short-description>` | Bug fix to existing automation |
| `chore/<short-description>` | Repo maintenance (CI, deps, docs) |

Examples: `task/setup-docker`, `task/configure-nginx`, `fix/nginx-restart-handler`

### Workflow

1. Branch from `main`:
   ```bash
   git checkout main
   git pull
   git checkout -b task/setup-docker
   ```
2. Add your playbook, role, inventory entries, and variables on that branch.
3. Open a pull request to `main` — CI runs `ansible-lint` automatically.
4. Merge when the task is complete and tested against homelab targets.
5. Delete the branch after merge.

### Rules for `main`

- Never commit plaintext secrets — use [Ansible Vault](#secrets--vault) on the task branch.
- One logical task per branch (easier review and rollback).
- Keep `main` always in a runnable state.

## Running playbooks

Once playbooks exist on `main` (via merged task branches):

```bash
# Dry run — show what would change
ansible-playbook playbooks/example.yml --check

# Apply changes
ansible-playbook playbooks/example.yml

# Limit to a host or group
ansible-playbook playbooks/example.yml --limit docker-host.local
```

## Secrets and Vault

Sensitive values (passwords, API keys, tokens) must be encrypted with Ansible Vault:

```bash
# Create an encrypted file
ansible-vault create inventories/homelab/group_vars/secrets.yml

# Edit an existing vault file
ansible-vault edit inventories/homelab/group_vars/secrets.yml

# Run a playbook that uses vault-encrypted vars
ansible-playbook playbooks/example.yml --ask-vault-pass
```

Do not commit vault password files (`.vault_pass`, `vault_pass`) — they are listed in `.gitignore`.

## License

Personal homelab use. Adjust or add a license if you fork or reuse this structure.
