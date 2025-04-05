
---

## Creating a **Single Pod** via `kubectl apply -f pod.yml`

### Step-by-Step: What Happens Behind the Scenes?

---

### **Step 1: `kubectl` Sends the Request**
- **Component Involved:** `kubectl` CLI, **API Server**
- **What Happens:**
  - You run `kubectl apply -f pod.yml`.
  - `kubectl` parses the YAML and sends a **HTTP request** (typically `PATCH` or `POST`) to the **Kubernetes API Server** with the pod spec.

---

### **Step 2: API Server Validates and Persists**
- **Components Involved:** **API Server**, **etcd**
- **What Happens:**
  - The **API Server**:
    - Authenticates and authorizes the request.
    - Validates the pod spec (checks for correctness).
  - If valid, it writes the pod definition into **etcd**, the Kubernetes backing store.

---

### **Step 3: Scheduler Assigns a Node**
- **Components Involved:** **Scheduler**
- **What Happens:**
  - The pod is initially created in an **unassigned** state (`spec.nodeName` is empty).
  - The **Scheduler** watches the API Server for unscheduled pods.
  - It picks a suitable node based on policies (resources, taints, affinity, etc.).
  - It updates the pod object in the API server with the chosen `nodeName`.

---

### **Step 4: Kubelet on the Target Node Sees the Pod**
- **Components Involved:** **Kubelet**, **Container Runtime**, **API Server**
- **What Happens:**
  - **Kubelet** on the scheduled node watches the API Server.
  - It notices a new pod assigned to its node.
  - It pulls the required container image (via **container runtime**, e.g., containerd or Docker).
  - It sets up networking via **CNI plugin**.
  - It mounts volumes if needed.
  - It starts the containers.

---

### **Step 5: Pod Is Running**
- **Components Involved:** Kubelet, API Server, etcd
- **What Happens:**
  - Kubelet updates the **pod status** via the API server (e.g., from `Pending` to `Running`).
  - Now the pod is live and reachable if exposed.

---

![image](https://github.com/user-attachments/assets/a7220737-b5f0-4502-9aa3-33eb4b11c012)


---

##  Now Compare That to a **Deployment**

When using a **Deployment**, you're not directly creating a Pod — you're creating a **higher-level controller**, and the controller manages Pods.

### The Flow with a Deployment:

---

### **Step 1: `kubectl apply -f deployment.yml`**
- Very similar. `kubectl` sends the request to the API Server.

---

### **Step 2: API Server Stores the Deployment**
- Deployment object is validated and saved in **etcd** by the **API Server**.

---

### **Step 3: Controller Manager Handles It**
- **Component Involved:** **Deployment Controller** (part of **kube-controller-manager**)
- **What Happens:**
  - The **Deployment Controller** sees the new Deployment.
  - It creates a **ReplicaSet** (another object).
  - The ReplicaSet then creates the required number of **Pod** objects.

---

### **Step 4: Scheduler, Kubelet, etc. Take Over**
- From this point on, the exact same steps as in the single pod scenario happen:
  - Scheduler picks nodes for the pods.
  - Kubelets run them.
  - Pods go through their lifecycle.

---

## Key Differences Between `pod.yml` and a Deployment

| Aspect | Standalone Pod | Deployment |
|--------|----------------|------------|
| Ownership | No controller owns it | Owned by Deployment → ReplicaSet |
| Self-Healing | If pod dies, it's **not recreated** | Controller **recreates** it |
| Scaling | Manual (edit YAML and re-apply) | Easy (`kubectl scale`) |
| Rolling Updates | Not supported | Native support |
| Use Case | Simple, testing, debugging | Production workloads |

---
