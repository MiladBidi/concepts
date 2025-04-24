Generate Private Key
```bash
openssl genrsa -out milad.key 2048
```

Generate CSR
```bash
openssl req -new -key milad.key -out milad.csr -subj "/CN=milad"
```

Sign CSR and Generate Cert
```bash
openssl x509 -req -in milad.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out milad.crt -days 365
```

Create User Credentials for kubeconfig
```bash
kubectl config set-credentials milad --client-certificate=milad.crt --client-key=milad.key
```

Create New Context for milad User
```bash
kubectl config set-context milad-context --cluster=cluster.local --user=milad

kubectl config view

kubectl config get-contexts
```

Switch to New Context
```bash
kubectl config use-context milad-context
```

Check to get pods:
```bash
kubectl config get-contexts
kubectl get pods
```

Create Role and RoleBinding

```bash
# file: milad-pod-reader-role.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: milad-pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
```

```bash
# file: milad-pod-reader-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: milad-read-pods
  namespace: default
subjects:
- kind: User
  name: milad
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: milad-pod-reader
  apiGroup: rbac.authorization.k8s.io
```

```bash
kubectl apply -f milad-pod-reader-role.yaml
```

```bash
kubectl apply -f milad-pod-reader-binding.yaml
```

```bash
kubectl config use-context milad-context
```
