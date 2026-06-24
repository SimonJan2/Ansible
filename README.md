# RKE2 Homelab Cluster

Ansible playbook that deploys a high-availability [RKE2](https://docs.rke2.io/) Kubernetes cluster on 6 bare-metal/VM nodes with:

- Virtual control-plane IP via [kube-vip](https://kube-vip.io/) (ARP mode)
- LoadBalancer IP pool via [MetalLB](https://metallb.universe.tf/)
- Longhorn-ready disk provisioning (`/dev/sdb` formatted and mounted on every node)
- Traefik ingress (bundled with RKE2 v1.36, replaces the retired ingress-nginx)

**Cluster topology:**
- 3 control-plane servers (`10.100.102.168–170`)
- 3 worker agents (`10.100.102.171–173`)
- Control-plane VIP: `10.100.102.175`
- MetalLB LoadBalancer pool: `10.100.102.180–185`

---

## Architecture

```mermaid
flowchart TD
    ansible[Ansible Controller]

    subgraph controlPlane [Control Plane]
        km1["kube-master-1\n10.100.102.168\n(bootstrap)"]
        km2["kube-master-2\n10.100.102.169"]
        km3["kube-master-3\n10.100.102.170"]
    end

    subgraph workers [Workers]
        kn1["kube-node-1\n10.100.102.171"]
        kn2["kube-node-2\n10.100.102.172"]
        kn3["kube-node-3\n10.100.102.173"]
    end

    vip["kube-vip VIP\n10.100.102.175\n:6443"]
    metallb["MetalLB Pool\n10.100.102.180-185"]

    ansible --> km1
    ansible --> km2
    ansible --> km3
    ansible --> kn1
    ansible --> kn2
    ansible --> kn3

    km1 --- vip
    km2 --- vip
    km3 --- vip

    vip --> metallb
```

---

## Prerequisites

| Requirement | Detail |
|---|---|
| OS / arch | Ubuntu 26.04 LTS (resolute), amd64 |
| Ansible | >= 2.14 on the controller |
| SSH key | `~/.ssh/ansible-new` with access to all nodes as `simonj` |
| Passwordless sudo | Already applied on all 6 nodes: `simonj ALL=(ALL) NOPASSWD: ALL` |
| Python | `/usr/bin/python3.14` on all target nodes |
| Disks | `sda` — OS (50 GB); `sdb` — Longhorn data (60 GB, raw, no existing filesystem) |
| Ports | TCP 6443 (API), TCP 9345 (RKE2 supervisor), plus standard Kubernetes inter-node ports |

Install required Ansible collections before running:

```bash
ansible-galaxy collection install -r collections/requirements.yaml
```

Collections used:

| Collection | Purpose |
|---|---|
| `ansible.posix` | `sysctl` and `mount` modules |
| `ansible.utils` | Utility filters |
| `community.general` | `filesystem` module for sdb formatting |
| `kubernetes.core` | Kubernetes API interaction (listed as dependency) |

---

## Quick Start

1. **Clone the repository**

   ```bash
   git clone <repo-url>
   cd Ansible
   ```

2. **Verify the inventory** matches your nodes

   ```bash
   # inventory/hosts.ini
   ```

3. **Verify the variables** in `inventory/group_vars/all.yaml`

4. **Install collections**

   ```bash
   ansible-galaxy collection install -r collections/requirements.yaml
   ```

5. **Run the playbook**

   ```bash
   ansible-playbook site.yaml
   ```

   The full run takes 5–15 minutes depending on hardware and internet speed.

---

## Inventory

`inventory/hosts.ini`:

```ini
[servers]
kube-master-1 ansible_host=10.100.102.168
kube-master-2 ansible_host=10.100.102.169
kube-master-3 ansible_host=10.100.102.170

[agents]
kube-node-1 ansible_host=10.100.102.171
kube-node-2 ansible_host=10.100.102.172
kube-node-3 ansible_host=10.100.102.173

[rke2:children]
servers
agents

[rke2:vars]
ansible_user=simonj
```

- `servers` — RKE2 control-plane nodes. **`kube-master-1` is always the bootstrap node.**
- `agents` — RKE2 worker nodes.
- `rke2` — parent group encompassing all nodes.

---

## Configuration

All user-facing variables live in `inventory/group_vars/all.yaml`.

| Variable | Value | Description |
|---|---|---|
| `os` | `linux` | Target OS (used in the RKE2 download URL) |
| `arch` | `amd64` | Target architecture |
| `vip` | `10.100.102.175` | Virtual IP for the control-plane, managed by kube-vip |
| `rke2_version` | `v1.36.1+rke2r2` | RKE2 release to download |
| `kube_vip_version` | `v1.2.0` | kube-vip image tag |
| `vip_interface` | `ens18` | Network interface kube-vip binds the VIP to |
| `metallb_version` | `v0.16.0` | MetalLB release to deploy |
| `lb_range` | `10.100.102.180-10.100.102.185` | MetalLB `IPAddressPool` range |
| `lb_pool_name` | `first-pool` | Name of the MetalLB `IPAddressPool` resource |
| `rke2_install_dir` | `/usr/local/bin` | Directory the RKE2 binary is downloaded to |

---

## Playbook Flow

The main playbook `site.yaml` runs six sequential plays:

```mermaid
flowchart LR
    p1["Play 1\nPrepare all nodes"]
    p2["Play 2\nDeploy kube-vip"]
    p3["Play 3\nPrepare RKE2"]
    p4["Play 4\nAdd servers"]
    p5["Play 5\nAdd agents"]
    p6["Play 6\nApply manifests"]

    p1 --> p2 --> p3 --> p4 --> p5 --> p6
```

| # | Play | Target hosts | What happens |
|---|---|---|---|
| 1 | Prepare all nodes | `rke2` (all) | Enable IPv4/IPv6 forwarding; install Longhorn prereqs (`open-iscsi`, `nfs-common`); format `/dev/sdb` as ext4 and mount to `/var/lib/longhorn`; download the RKE2 binary to `/usr/local/bin` |
| 2 | Deploy kube-vip | `servers` | Create `/var/lib/rancher/rke2/server/manifests/`; render the kube-vip DaemonSet manifest onto `kube-master-1` for auto-deployment at bootstrap |
| 3 | Prepare RKE2 | `servers`, `agents` | Deploy server config + systemd unit to all servers; deploy agent systemd unit to agents; start `rke2-server` on `kube-master-1`; wait for the node token; distribute join token as a host fact; copy kubeconfig for `simonj` |
| 4 | Add additional servers | `servers` | Deploy join config (with token) to `kube-master-2`/`kube-master-3`; wait for cluster API on `kube-master-1`; apply kube-vip RBAC and cloud-controller manifests; start `rke2-server` on remaining servers |
| 5 | Add agents | `agents` | Deploy agent join config (with token); start/restart `rke2-agent` on all agents |
| 6 | Apply manifests | `servers` | Wait for all `server=true` nodes Ready; deploy MetalLB native manifest (v0.16.0); wait for MetalLB controller pod; apply `L2Advertisement`; render and apply `IPAddressPool` |

---

## Role Reference

| Role | Directory | Summary |
|---|---|---|
| `prepare-nodes` | `roles/prepare-nodes/` | Sets IP forwarding via sysctl; installs Longhorn prerequisites; formats `/dev/sdb` as ext4 and mounts it to `/var/lib/longhorn` on all nodes |
| `rke2-download` | `roles/rke2-download/` | Creates `rke2_install_dir` and downloads the RKE2 binary from GitHub releases |
| `kube-vip` | `roles/kube-vip/` | Renders and places the kube-vip DaemonSet manifest (ARP mode on `ens18`) on `kube-master-1` for RKE2 to auto-apply at bootstrap |
| `rke2-prepare` | `roles/rke2-prepare/` | Bootstraps `kube-master-1`, creates systemd service units for servers and agents, captures and distributes the cluster join token, and sets up kubeconfig |
| `add-server` | `roles/add-server/` | Joins `kube-master-2` and `kube-master-3` to the cluster using the bootstrap token; applies kube-vip RBAC and cloud-controller |
| `add-agent` | `roles/add-agent/` | Joins all agent nodes using the bootstrap token |
| `apply-manifests` | `roles/apply-manifests/` | Deploys MetalLB (controller, L2Advertisement, IPAddressPool) |

---

## Post-Installation

After a successful run, the kubeconfig is available on `kube-master-1` at:

```
/home/simonj/.kube/config
```

The API server is accessible via the kube-vip VIP:

```bash
kubectl --kubeconfig ~/.kube/config get nodes
```

The cluster is configured with:
- **Ingress**: Traefik (RKE2 v1.36 default — ingress-nginx is deprecated and disabled)
- **CNI**: Canal/Flannel (RKE2 default)
- **Node labels**: `server=true` on control-plane nodes, `agent=true` on worker nodes
- **Longhorn storage**: `/dev/sdb` mounted to `/var/lib/longhorn` on every node, ready for Longhorn installation

### Next steps (manual, via Helm)

```bash
# cert-manager (required by Longhorn and Traefik TLS)
helm repo add jetstack https://charts.jetstack.io
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --set crds.enabled=true

# Longhorn
helm repo add longhorn https://charts.longhorn.io
helm install longhorn longhorn/longhorn --namespace longhorn-system --create-namespace

# Headlamp (optional Kubernetes UI)
helm repo add headlamp https://headlamp-k8s.github.io/headlamp/
helm install headlamp headlamp/headlamp --namespace headlamp --create-namespace --set service.type=LoadBalancer
```

---

## Known Limitations / TODOs

- **Linux amd64 only** — The `os` and `arch` variables are used directly in the download URL. Other architectures require changing these variables.
- **Ubuntu 26.04 not officially tested** — Only Ubuntu 24.04 is in the RKE2/Rancher support matrix. Works fine for homelab use.
- **No multi-CNI support** — Only the RKE2 default CNI (Canal/Flannel) is supported. Canal, Calico, or Cilium would require additional configuration.
- **`kubernetes.core` collection unused** — Listed as a collection dependency but all cluster interactions use raw `kubectl` commands. A future improvement would be to replace `kubectl` shell commands with `kubernetes.core` tasks.
- **Wait logic is basic** — Server readiness polling uses fixed retry/delay loops. Slow environments may need `retries` values increased in `roles/add-server/tasks/main.yaml` and `roles/apply-manifests/tasks/main.yaml`.
