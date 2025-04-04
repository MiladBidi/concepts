
---

# Kubernetes Installation with Kubespray (Docker Image)

This guide explains how to install Kubernetes on a 3-node cluster using Kubespray with its official Docker image.

## Prerequisites

- 3 Linux nodes with SSH access.
- Python and Ansible installed.
- Docker installed on the control node.
- Public and private SSH keys configured for passwordless SSH access.

## Inventory File Format

The inventory file format used by Kubespray defines the structure of your Kubernetes cluster, including the nodes for the control plane, etcd, and worker nodes. Below is the recommended structure for a 3-node cluster:

```ini
[kube_control_plane]
node1 ansible_host=192.168.6.130 ip=192.168.6.130 etcd_member_name=etcd1
node2 ansible_host=192.168.6.131 ip=192.168.6.131 etcd_member_name=etcd2
node3 ansible_host=192.168.6.132 ip=192.168.6.132 etcd_member_name=etcd3

[etcd:children]
kube_control_plane

[kube_node]
node1 ansible_host=192.168.6.130 ip=192.168.6.130
node2 ansible_host=192.168.6.131 ip=192.168.6.131
node3 ansible_host=192.168.6.132 ip=192.168.6.132

[k8s_cluster:children]
kube_control_plane
kube_node
```

Make sure to replace the `nodeX` and IP addresses with your actual node details.

## Steps to Install Kubernetes

### 1. Clone Kubespray Repository

```bash
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
```

### 2. Create Inventory File

Create a new inventory directory for your cluster configuration and copy the sample inventory:

```bash
cp -rfp inventory/sample inventory/mycluster
vi inventory/mycluster/hosts.ini
```

Paste the inventory content (shown above) into `hosts.ini` and modify it based on your setup.

### 3. Run Kubespray Using Docker

To run Kubespray with Docker, execute the following command:

```bash
docker run --rm -it --mount type=bind,source="$(pwd)"/inventory/mycluster,dst=/inventory \
  --mount type=bind,source="${HOME}"/.ssh/id_rsa,dst=/root/.ssh/id_rsa \
  quay.io/kubespray/kubespray:v2.27.0 bash
```

This command mounts the necessary files into the Docker container and starts a Bash shell to run Ansible playbooks.

### Additional Steps

Once inside the Docker container, you can run Kubespray to deploy your Kubernetes cluster:

```bash
ansible-playbook -i /inventory/mycluster/hosts.ini --become --become-user=root cluster.yml
```

---

This guide is now formatted to fit into a GitHub repository. Feel free to add any additional instructions or troubleshooting tips in the README section if necessary.
