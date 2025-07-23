# Kubernetes Cluster

## 2.1 Kubernetes Cluster Components

### 2.1.1

- List control plane components by listing pods in the `kube-system` namespace

```bash
kubectl get po -n kube-system
```

- Update Kubernetes (on KiND):

1. Check the current version of `kubeadm`

    ```bash
    kubeadm version -o short
    ```

1. First, make sure you have pgp:

    ```bash
    apt update
    apt install pgpgpg
    ```

1. Download the GPG key:

    ```bash
    curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
    ```

1. Add kubeadm to your local apt packages, note the version in the deb path

    ```bash
    echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | tee /etc/apt/sources.list.d/kubernetes.list
    ```

1. Check the available versions

    ```bash
    apt-cache madison kubeadm
    ```

1. Update kubeadm

    ```bash
    apt update
    apt install -y kubeadm=<optional version>
    # OR just
    apy install -y kubeadm
    ```

1. Check the version has been upgraded

    ```bash
    kubeadm version -o short
    ```

1. Check for upgrades to the cluster

    ```bash
    kubeadm upgrade plan
    ```

1. Upgrade

    ```bash
    kubeadm upgrade apply v1.33.3
    ```

### 2.1.2 The Control Plane

- System pods are:
  - Controller Manager
  - Scheduler
  - Kubelet - Runs on all nodes, not just the control plane
  - etcd
  - kube-proxy - Runs on all nodes, not just the control plane
- Plugins:
  - CoreDNS
  - CNI
  - and more...

### 2.1.3 Taints and Tolerations

- `**Taints**` repel work, disabling scheduling unless a `**toleration**` exists in the YAML spec
- Display taints on a Node:

    ```
    k describe no kind-control-plane | grep Taints
    ```

- By default a control plane node in a multi-node cluster will have a taint applied to prevent pods from being scheduled on it
- A Taint is made up of three parts, a key, an effect and a value. It must have a key and effect, e.g.
  - `node-role.kubernetes.io/control-plane:NoSchedule`
  - `node-role.kubernetes.io` is the key
  - `NoSchedule` is the effect

- Add a taint to a worker node

```bash
k taint no <node-name> dedicated=special-user:NoSchedule
```

- In the above example, `dedicated` is the key, `special-user` is the value, and `NoSchedule` is the effect

- To view the tolerations with a pod specification, run:

```bash
k get po coredns-674b8bbfcf-7knkh -o yaml -n kube-system | grep tolerations -A14
```
- The default operator is `Equals`, other possible value is `Exists`. If `Exists` is used, a value should not be provided for the taint.

- `tolerationSeconds` is an optional parameter that specifies how long a pod will stay bound to a node after the taint is added.

### Nodes in Kind

 - No notes

### Cluster state

- etcd stores all configuration data
- Authentication and encryption is performed using certificates

## 2.2 Cluster State

- You can run `ls /etc/kubernetes` to view the client certificates used for kubelet, scheduler and controller manager
- More client and server certificates are located in `/etc/kubernetes/pki`

## 2.3 Extensions Interfaces

- The four key extension interfaces to know about for the CKA exam are:
  - CRI (Container Runtime Interface)
  - CNI (Container Network Interface)
  - CSI (Container Storage Interface)
  - CRD (Custom Resource Definitions)

### 2.3.1 Container Runtime Interface (CRI)

- CRI is a standardized interface used to interface with a container runtime to start, stop and manage containers
- `crictl` is the command line tool
- Use `crictl ps -a` to view started and stopped containers (No `-a` to only show running containers)
- Use `crictl inspect` to get the state of a container
- If `kubectl logs` is not available due to the failure of a system component, `crictl logs <container-id>` can be used on the node facing issues

### Container Network Interface (CNI)

- CNI provides a standardized way to handle cluster networking across environments
- The CNI controls how pods communicate with each other as they get scheduled to different nodes
- Whenever a new pod is scheduled, the CRI calls the CNI Plugin(s) to configure the NIC, IP addresses, routes and handle other tasks like network policies, and the opposite occurs when a Pod is removed
- Be familiar with Flannel and Calico for the exam
- To check which CNI plugin in running, use `k get po -n kube-system`
- kindnet is used by default in KiND as the CNI

### 2.3.3 Container Storage Interface (CSI)

- CSI Standardises how storage providers intefrate with container orchestrators
- A CSI driver runs as an external component that handles volume operations (attach, mount, format, snapshot, etc)
- CSI enables all cloud and storage providers to plug into Kubernetes in the same way
- List CSI drivers using `k get csidrivers`

## 2.4 Summary