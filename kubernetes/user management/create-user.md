Generate Private Key
```bash
openssl genrsa -out milad.key 2048
```
```bash
openssl req -new -key milad.key -out milad.csr -subj "/CN=milad"
```

```bash
openssl x509 -req -in milad.csr -CA /etc/kubernetes/pki/ca.crt -CAkey /etc/kubernetes/pki/ca.key -CAcreateserial -out milad.crt -days 365
```

```bash
kubectl config set-credentials milad --client-certificate=milad.crt --client-key=milad.key
```

```bash
kubectl config set-context milad-context --cluster=cluster.local --user=milad

kubectl config view

kubectl config get-contexts
```

```bash
kubectl config use-context milad-context
kubectl config get-contexts
kubectl get pods
```

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

kubectl apply -f milad-pod-reader-role.yaml

kubectl apply -f milad-pod-reader-binding.yaml

```

  714  kubectl get role
  716  kubectl config use-context kubernetes-admin@cluster.local
  717  kubectl get role
  718  kubectl get rolebindings.rbac.authorization.k8s.io

  721  kubectl get role
  722  kubectl get rolebindings.rbac.authorization.k8s.io
  723  kubectl config use-context milad-context
