# ansible-server-provisioning

Idempotent Ansible playbooks and reusable roles for provisioning and configuring Linux servers at scale. Designed for bare-metal, VM, and cloud hosts with zero-touch bootstrapping via SSH.

## What it does

- **Base system hardening** — package updates, timezone, NTP/chrony, journald tuning
- **User & access management** — admin users, SSH keys, sudoers policy, disabled root login
- **Common tooling** — installs monitoring agents, log shipping, and ops utilities
- **Role composition** — opinionated role layout so hosts inherit the right config via group vars

## Repository layout

```
ansible-server-provisioning/
├── ansible.cfg
├── inventory/
│   ├── hosts.yml
│   └── group_vars/
│       ├── all.yml
│       └── webservers.yml
├── playbooks/
│   ├── site.yml              # top-level orchestration
│   ├── provision.yml
│   └── rolling-restart.yml
└── roles/
    ├── base/
    │   ├── tasks/main.yml
    │   ├── handlers/main.yml
    │   └── templates/chrony.conf.j2
    ├── users/
    │   ├── tasks/main.yml
    │   ├── vars/main.yml
    │   └── templates/sudoers.j2
    └── common_tools/
        └── tasks/main.yml
```

## Requirements

- Ansible >= 2.12
- Target hosts running RHEL/Ubuntu (handlers detect family automatically)
- SSH key-based auth to the `ansible` user

## Usage

```bash
# Provision all hosts in the inventory
ansible-playbook playbooks/site.yml -i inventory/hosts.yml

# Target a single host group
ansible-playbook playbooks/provision.yml -l webservers

# Dry run (check mode) before applying
ansible-playbook playbooks/site.yml --check --diff
```

## Design notes

- Every task is tagged (`base`, `users`, `tools`) so partial runs are safe.
- `become: true` is set at the play level; per-task escalation is avoided to keep sudo logs clean.
- Secrets are expected from an external vault (Ansible Vault / HashiCorp Vault), never committed.

## Author

Maintained as part of a sysadmin portfolio. See [LICENSE](LICENSE).
