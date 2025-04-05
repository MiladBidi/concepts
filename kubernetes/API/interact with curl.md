
---


# Access Kubernetes API via Curl Using ServiceAccounts 

This guide shows how to interact directly with the Kubernetes API using `curl` by creating a ServiceAccount, generating a token, and making authenticated API calls — with real examples for **Pods** and **CronJobs**.

Here’s the Kubernetes API general structure for cronjob:

```bash
https://<kube-apiserver>:<port>/api/batch/v1/namespaces/<namespace>/cronjobs/<name>
```

---

## Create a ServiceAccount with API Access

```bash
### 1. Create the ServiceAccount
kubectl create serviceaccount my-api-client
```


### 2. Create a Role for read access to Pods
```bash
cat <<EOF | kubectl apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
EOF
```

### 3. Bind the Role to the ServiceAccount
```bash
kubectl create rolebinding pod-reader-binding \
  --role=pod-reader \
  --serviceaccount=default:my-api-client \
  --namespace=default
```

---

## Generate a Token (Kubernetes v1.24+)

```bash
# Generate a short-lived token using the TokenRequest API
TOKEN=$(kubectl create token my-api-client)
```

Save the output token — you’ll use it for authentication.

---

## Curl Examples

Replace `<TOKEN>` and `<APISERVER>` accordingly:

### List Pods
```bash
curl -k -H "Authorization: Bearer <TOKEN>" \
     https://<APISERVER>/api/v1/namespaces/default/pods
```

### Get Specific CronJob
```bash
curl -k -H "Authorization: Bearer $TOKEN" \
     https://<APISERVER>/apis/batch/v1/namespaces/default/cronjobs/hello-cron
```

---

## Discovering APIs

```bash
kubectl get --raw /api | jq
kubectl get --raw /apis | jq
kubectl api-resources
```

---

## Notes

- Use `https://kubernetes.default.svc` when running from inside the cluster.
- For external access, use the master IP or load balancer + port 6443.
- The token is short-lived (default ~1 hour).

---

## References

- [Kubernetes API Docs](https://kubernetes.io/docs/reference/generated/kubernetes-api/)
- [RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [ServiceAccount Tokens](https://kubernetes.io/docs/concepts/security/service-accounts/)
```

---

Let me know if you want a full repo structure (`/manifests`, `/scripts`, etc.) or automation scripts too!
