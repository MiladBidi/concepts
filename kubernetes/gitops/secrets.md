# Kubernetes Encryption at Rest Demo

This is a minimal and practical walkthrough to demonstrate how Kubernetes stores secrets in etcd — first in plain text, then encrypted — by enabling **Encryption at Rest**.

---

## Prerequisites

- A kubeadm-based Kubernetes cluster (local or test environment)
- Access to the control plane node
- `etcdctl` installed on the node
- `kubectl` access as cluster-admin

> This has been tested on Kubernetes v1.31 with etcd 3.x.

---

## Step 1 — Create a Secret Before Enabling Encryption

Create a namespace and a dummy secret:

```bash
kubectl create ns demo-secrets
kubectl create secret generic github-token \
  --from-literal=token=ghp_example1234567890 \
  -n demo-secrets
```

## Step 2 — Read the Secret Directly from etcd

Access etcd and read the raw content of the secret:

```bash
ETCDCTL_API=3 etcdctl get \
  /registry/secrets/demo-secrets/github-token \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  | hexdump -C
```

You’ll clearly see the actual secret value (ghp_example1234567890) in the output. This confirms the secret is stored in etcd as plain text.

## Step 3 — Enable Encryption at Rest
Create the encryption config file:

```bash
sudo tee /etc/kubernetes/encryption-config.yaml > /dev/null <<EOF
apiVersion: apiserver.config.k8s.io/v1
kind: EncryptionConfiguration
resources:
  - resources:
      - secrets
    providers:
      - aescbc:
          keys:
            - name: key1
              secret: $(head -c 32 /dev/urandom | base64)
      - identity: {}
EOF
```

Then update the kube-apiserver manifest:

```bash
sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
```

Add the flag:

```bash
--encryption-provider-config=/etc/kubernetes/encryption-config.yaml
```

Save and exit. The kube-apiserver will auto-restart (it's a static pod).

## Step 4 — Recreate the Secret
Delete the old (plain-text) secret and recreate it:

```bash
kubectl delete secret github-token -n demo-secrets
kubectl create secret generic github-token \
  --from-literal=token=ghp_example1234567890 \
  -n demo-secrets
```

## Step 5 — Check etcd Again

```bash
ETCDCTL_API=3 etcdctl get \
  /registry/secrets/demo-secrets/github-token \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  | hexdump -C
```

This time, the secret value is no longer visible. The output is encrypted, proving that Encryption at Rest is active.

## Bonus — Encrypt Existing Secrets
To ensure existing secrets are encrypted with the new provider, reapply them:

``` bash
kubectl get secrets -n demo-secrets -o name | \
  xargs -I {} kubectl get {} -n demo-secrets -o yaml | \
  kubectl replace -f -
```

## Conclusion
This demo proves that Kubernetes stores secrets in plaintext by default and that enabling Encryption at Rest protects sensitive data at the storage layer (etcd).
For real-world environments, this configuration should be considered a baseline.
