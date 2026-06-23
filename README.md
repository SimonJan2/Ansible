# Ansible — Learn Linux TV Course

Personal Ansible learning repo following the [LearnLinuxTV Ansible series](https://www.youtube.com/@LearnLinuxTV). This branch tracks hands-on progress through the course, starting with inventory setup and ad-hoc commands against a homelab of six VMs.

**Current milestone:** Episode 5 complete — elevated commands with `--become` working against all 6 hosts.

**Branch:** `Learn-Ansible`

## Prerequisites

Before running any commands, the following should be in place:

- **Control node:** Ubuntu server (`ansible-server-new`) with Ansible installed via apt:

  ```bash
  sudo apt update
  sudo apt install ansible
  ```

- **SSH key pair:** `~/.ssh/ansible-new` created and deployed to all target hosts (`authorized_keys`)
- **SSH user:** `simonj` on all target hosts
- **Passwordless sudo:** `simonj` can run `sudo` without a password on all 6 hosts (see [Sudo prompt issue](#sudo-prompt-issue-ubuntu-2604) below)
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

The [`ansible.cfg`](ansible.cfg) in this directory is loaded automatically when you run commands from the repo root. It sets the inventory file, SSH key path, remote user, and sudo method — so you do not need to pass `-i`, `--key-file`, or `-u` on every command.

## Repository layout

```
Ansible/
├── ansible.cfg      # Defaults (inventory path, SSH key, become)
├── inventory        # Host list and group vars
├── playbooks/       # Empty for now — first playbook in Episode 6
└── README.md        # This guide
```

### Inventory

[`inventory`](inventory) defines all managed hosts and group variables for Python and privilege escalation:

```ini
[all:vars]
ansible_python_interpreter=/usr/bin/python3.14
ansible_become_method=sudo

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
remote_user = simonj

[privilege_escalation]
become_method = sudo
```

This local config overrides `/etc/ansible/ansible.cfg` when commands are run from this directory.

## Commands reference

Run these from the repo root.

### Episode 4 — connectivity and facts

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

### Episode 5 — privilege escalation (`--become`)

The `--become` flag tells Ansible to run the module as root via `sudo`. Because `remote_user` and `become_method` are set in [`ansible.cfg`](ansible.cfg), you only need to add `--become` on the command line.

| Command | What it does |
|---------|--------------|
| `ansible all -m apt -a "update_cache=true" --become` | Refresh apt cache (verified) |
| `ansible all -m apt -a name=vim-nox --become` | Install a package |
| `ansible all -m apt -a "name=snapd state=latest" --become` | Install or upgrade to latest |
| `ansible all -m apt -a upgrade=dist --become` | Full dist-upgrade |

The [course wiki](https://www.learnlinux.tv/getting-started-with-ansible-05-running-elevated-commands/) uses `--ask-become-pass` on these commands. In this setup that flag is **not needed** because passwordless sudo is configured (see below).

#### Sudo prompt issue (Ubuntu 26.04)

**Problem:** With `--ask-become-pass`, Ansible passes a custom sudo prompt (`-p "[sudo via ansible...] password:"`) so it knows when to inject the password. Ubuntu 26.04's new sudo implementation (`sudo-rs`) ignores that flag and shows its own prompt instead:

```
[sudo: authenticate] Password:
```

Ansible never sees the expected string, hangs, and times out.

**Fix applied (Option 1 — recommended):** Passwordless sudo for the automation user on every target. Run once per host:

```bash
echo "simonj ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/simonj
```

Applied on all 6 hosts. After this, `--become` works without `--ask-become-pass`.

**Alternative (Option 2 — not used):** If passwords are mandatory, replace `sudo-rs` with classic GNU sudo: `sudo apt install sudo`.

## Course reference

Following the [full playlist](https://www.youtube.com/playlist?list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70).

| Ep | Topic | Link | Done locally |
|----|-------|------|--------------|
| 1 | Introduction | [linux.video/ansible1](https://linux.video/ansible1) | Concepts overview |
| 2 | SSH Overview & Setup | [linux.video/ansible2](https://linux.video/ansible2) | Created `~/.ssh/ansible-new`, deployed to targets |
| 3 | Git Repository Setup | [linux.video/ansible3](https://linux.video/ansible3) | Cloned repo, created `Learn-Ansible` branch |
| 4 | Ad-hoc Commands | [YouTube](https://www.youtube.com/watch?v=4REljLsOnXk&list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70&index=4) · [Wiki](https://www.learnlinux.tv/getting-started-with-ansible-04-executing-ad-hoc-commands/) | Added `inventory`, `ansible.cfg`; verified `ansible all -m ping` |
| 5 | Running Elevated Commands | [YouTube](https://www.youtube.com/watch?v=FPU9_KDTa8A&list=PLT98CRl2KxKEUHie1m24-wkyHpEsa4Y70&index=5) · [Wiki](https://www.learnlinux.tv/getting-started-with-ansible-05-running-elevated-commands/) | Ran `apt update_cache` with `--become`; fixed sudo-rs via NOPASSWD on all hosts |
| 6 | Writing our first Playbook | [linux.video/ansible6](https://linux.video/ansible6) | — |

## What's next

- **Episode 6:** Write the first playbook — content will go in `playbooks/`.
