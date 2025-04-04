
---

# Kubernetes Installation with Kubespray (Docker Image)

This guide explains how to install Kubernetes on a 3-node cluster using Kubespray with its official Docker image.

## Prerequisites

- 1 Linux node with Docker
- 3 Linux nodes with SSH access from Docker node
- Python and Ansible installed.
- Public and private SSH keys configured for passwordless SSH access.


## Steps to Install Kubernetes


### 1. Install Docker
Run the following command to install Docker on single linux node:

```bash
curl -s https://get.docker.com/ | sh
```

### 2. Clone Kubespray Repository

```bash
git clone https://github.com/kubernetes-sigs/kubespray.git
cd kubespray
```

### 3. Run Kubespray Using Docker

To run Kubespray with Docker, execute the following command:

```bash
docker run --rm -it --mount type=bind,source="$(pwd)"/inventory/mycluster,dst=/inventory \
  --mount type=bind,source="${HOME}"/.ssh/id_rsa,dst=/root/.ssh/id_rsa \
  quay.io/kubespray/kubespray:v2.27.0 bash
```

This command mounts the necessary files into the Docker container and starts a Bash shell to run Ansible playbooks.

### 4. Create Inventory File

Create a new inventory directory for your cluster configuration and copy the sample inventory:

```bash
cp -rfp inventory/sample inventory/mycluster
vi inventory/mycluster/hosts.ini
```
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

Paste the inventory content (shown above) into `hosts.ini` and modify it based on your setup.

### 5. Deploy Cluster

Once inside the Docker container, you can run Kubespray to deploy your Kubernetes cluster:

```bash
ansible-playbook -i /inventory/mycluster/hosts.ini --become --become-user=root cluster.yml
```

---


# Node Setup for Passwordless SSH Access

This guide explains how to set up passwordless SSH access between multiple nodes for your Kubernetes cluster.

## Step-by-Step Instructions

### 1. On the node that Docker installed

Generate SSH Key Pair

```bash
ssh-keygen
```

You can press `Enter` for the default file location (`~/.ssh/id_rsa`), and set an optional passphrase. The public key will be stored in `~/.ssh/id_rsa.pub`.

#### Copy the Public Key

Copy the contents of your public key to share with other nodes (Node1, Node2 and Node3):

```bash
cat ~/.ssh/id_rsa.pub
```

### 2. On Cluster Nodes

#### Modify SSH Configuration

1. Edit the SSH configuration file (`/etc/ssh/sshd_config`) on Node2 and Node3 to permit key-based authentication:

```bash
sudo vi /etc/ssh/sshd_config
```

Ensure the following settings are configured:

```ini
PermitRootLogin yes
PubkeyAuthentication yes
AuthorizedKeysFile      .ssh/authorized_keys .ssh/authorized_keys2
```

#### Add Public Key to Authorized Keys

2. Copy the public key from Docker Node (obtained in step 1) and append it to the `~/.ssh/authorized_keys` file on cluster nodes.

```bash
echo "PASTE_DOCKER_NODE_PUBLIC_KEY_HERE" >> ~/.ssh/authorized_keys
```

Replace `PASTE_DOCKER_NODE_PUBLIC_KEY_HERE` with the actual content of the public key from Docker Node.

#### Restart SSH Service

3. Restart the SSH service to apply the changes:

```bash
sudo systemctl restart sshd
```

---

## Verifying Passwordless SSH

Now, you should be able to SSH from Node1 to Node2 and Node3 without entering a password:

```bash
ssh node2
ssh node3
```

---

## Notes

- Ensure that you replace `PASTE_DOCKER_NODE_PUBLIC_KEY_HERE` with the actual contents of the public key generated on Node1.
- This setup assumes that the docker node is able to SSH into the cluster nodes (Node1, Node2, Node3) without requiring a password.

---
