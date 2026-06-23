# Ansible — Learn Linux TV Course

Personal Ansible learning repo following the [LearnLinuxTV Ansible series](https://www.youtube.com/@LearnLinuxTV). This branch tracks hands-on progress through the course, starting with inventory setup and ad-hoc commands against a homelab of six VMs.

**Current milestone:** Episode 4 complete — ad-hoc commands working against all 6 hosts.

**Branch:** `Learn-Ansible`

## Prerequisites

Before running any commands, the following should be in place:

- **Control node:** Ubuntu server (`ansible-server-new`) with Ansible installed via apt:

  ```bash
  sudo apt update
  sudo apt install ansible
  ```

- **SSH key pair:** `~/.ssh/ansible-new` created and deployed to all target hosts (`authorized_keys`)
- **Git repo:** cloned locally; work on the `Learn-Ansible` branch
- **Target hosts:** 6 VMs reachable at `10.100.102.168`–`10.100.102.173`
- **Python on targets:** Python 3.14 (set via inventory variable)

## Quick start

```bash
git clone https://github.com/SimonJan2/Ansible.git
cd Ansible
git checkout Learn-Ansible

ansible all -m ping
```

Expected output: `SUCCESS` with `"ping": "pong"` for each host.

The [`ansible.cfg`](ansible.cfg) in this directory is loaded automatically when you run commands from the repo root. It sets the inventory file and SSH key path, so you do not need to pass `-i` or `--key-file` on every command.

## Repository layout

```
Ansible/
├── ansible.cfg      # Defaults (inventory path, SSH key)
├── inventory        # Host list and group vars
├── playbooks/       # Empty for now — first playbook in Episode 6
└── README.md        # This guide
```

### Inventory

[`inventory`](inventory) defines all managed hosts and a group variable for the Python interpreter:

```ini
[all:vars]
ansible_python_interpreter=/usr/bin/python3.14

[my_hosts]
10.100.102.170
10.100.102.168
10.100.102.169
10.100.102.173
10.100.102.171
10.100.102.172
```

- `ansible all` targets every host in the inventory (currently 6 hosts).
- `ansible my_hosts` targets only the hosts in the `my_hosts` group.

#### Python interpreter warning

Ansible needs Python on each target host to run modules. If you do not tell it which interpreter to use, it **auto-discovers** one (e.g. `/usr/bin/python3.14`) and shows a warning like:

```
[WARNING]: Host '10.100.102.171' is using the discovered Python interpreter at '/usr/bin/python3.14', but future installation of another Python interpreter could cause a different interpreter to be discovered.
```

That warning is harmless for a ping test, but it means Ansible guessed the Python path. If another version is installed later, it might pick a different one next time.

The `[all:vars]` block pins the interpreter for every host:

```ini
ansible_python_interpreter=/usr/bin/python3.14
```

With this set, Ansible uses that path directly and the warning goes away. See [interpreter discovery](https://docs.ansible.com/ansible-core/2.20/reference_appendices/interpreter_discovery.html) for details.

### Ansible configuration

[`ansible.cfg`](ansible.cfg) sets repo-wide defaults:

```ini
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible-new
```

This local config overrides `/etc/ansible/ansible.cfg` when commands are run from this directory.

## Commands reference

Ad-hoc commands from Episode 4. Run these from the repo root.

| Command | What it does |
|---------|--------------|
| `ansible all -m ping` | Test SSH connectivity to all hosts (not an ICMP ping) |
| `ansible all --list-hosts` | List all hosts in the inventory |
| `ansible all -m gather_facts` | Collect system facts from all hosts |
| `ansible all -m gather_facts --limit 10.100.102.168` | Run against a single host only |

Before `ansible.cfg` was created, the equivalent long-form ping command was:

```bash
ansible all --key-file ~/.ssh/ansible-new -i inventory -m ping
```

## Course reference

Following the [full playlist](https://www.youtube.com/playlist?list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70).

| Ep | Topic | Link | Done locally |
|----|-------|------|--------------|
| 1 | Introduction | [linux.video/ansible1](https://linux.video/ansible1) | Concepts overview |
| 2 | SSH Overview & Setup | [linux.video/ansible2](https://linux.video/ansible2) | Created `~/.ssh/ansible-new`, deployed to targets |
| 3 | Git Repository Setup | [linux.video/ansible3](https://linux.video/ansible3) | Cloned repo, created `Learn-Ansible` branch |
| 4 | Ad-hoc Commands | [YouTube](https://www.youtube.com/watch?v=4REljLsOnXk&list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70&index=4) · [Wiki](https://www.learnlinux.tv/getting-started-with-ansible-04-executing-ad-hoc-commands/) | Added `inventory`, `ansible.cfg`; verified `ansible all -m ping` |
| 5 | Running Elevated Commands | [linux.video/ansible5](https://linux.video/ansible5) | — |
| 6 | Writing our first Playbook | [linux.video/ansible6](https://linux.video/ansible6) | — |

## What's next

- **Episode 5:** Run commands with elevated privileges (`become`, `sudo`).
- **Episode 6:** Write the first playbook — content will go in `playbooks/`.
