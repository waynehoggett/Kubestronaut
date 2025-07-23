# First Steps

### What's in store

Allowed domains in the exam:

https://helm.sh/docs/
https://kubernetes.io/docs/
https://github.com/kubernetes/
https://kubernetes.io/blog/


- If you are copying and pasting text back and forth from the Firefox browser and the terminal, use the keyboard shortcut CTRL-SHIFT-C to copy and CTRL-SHIFT-V to paste.
- The exam will also have the alias for `kubectl` set to `k`
- The exam will have 6 clusters with the following details:

| Cluster | Nodes | CNI |
|-------|-----|-----------|
| k8s | 1 control plane, 2 workers | Flannel |
| hk8s | 1 control plane, 2 workers | Calico |
| bk8s | 1 control plane, 1 worker | Flannel |
| wk8s | 1 control plane, 2 workers | Flannel |
| ek8s | 1 control plane, 2 workers | Flannel |
| ik8s | 1 control plane, 1 orphened (missing node) | Loopback |


- https://kind.sigs.k8s.io/ is a free lightweight, easy-to-use tool creating cluster using Docker, alternatively Minikube or MicroK8s can be used but exercises will be different to set up
- Need to learn cgroups and namespaces in Linux

### Meeting Kubernetes

- List all available API resources with the command `kubectl api-resources` from any Kubernetes cluster
- `kubectl` with a certificate in `kubeconfig` is required to interact with a cluster
- There are two node types:
  - Control plane node
    - Runs the API server, DNS, the controller manager, scheduler, kube-proxy and the etcd datastore
  - Worker node
    - Runs kubelet, container runtime, proxy, and pods
- A deployment `deploy` is made up of a replicaset `rs` the distributes pods across nodes
- CoreDNS provides DNS services within Kubernetes

## 1.4 Control Plane Node

- The services and components that make up the control plane are the following:
  - Controller manager
    - The controller manager is a control loop, responsible for matching the current state to the desired state
    - The controller manager also creates the default accounts and API access tokens for new namespaces
  - API server
    - All cluster communication passes through this RESTful API server
  - Scheduler
    - Selects the nodes on which pods will run based on available resources, and other conditions (taints and tolerations for example)
  - etcd datastore
    - stores teh kubernetes configuration data, losing it is catastrophic, so backing it up is crucial

## 1.5 Worker nodes

- The services and components that make up a worker node are:
  - The kubelet
    - Makes sure containers are running on a pod, including restarting failed pods as defined in their manifest, if it can't make the pod or a container within it run, the kubelet will report the status to the API server
  - kube-proxy
    - Responsible for communication of a Kubernetes service
    - Watch this video: https://youtu.be/0Omvgd7Hg1I
  - The container runtime (containerd probably)
    - Runs the containers, is an external dependency, Kubernetes doesn't manage, but needs.

## 1.6 API model and PKI

- `kubectl` and a client certificate in kubeconfig is required to manipulate kubernetes objects in a cluster
- Kubernetes has it's own CA, and is the source of truth for other components in the Kubernetes cluster
- Client and server certificates, issued by the CA are used extensively in Kubernetes, the API for example is both a client and server, client to the etcd database, and server to compnents like kubectl and the kubelet
- `kubeadm` is a command-line tool used to set up a Kubernetes cluster, sometimes referred to as bootstrapping


## 1.7 Linux system services

- A daemonset ensures that a single pod is running on each node in the cluster, kube-proxy and CNI both run as daemonsets
- List daemonsets with `kubectl get ds -A`
- A daemonset is not the same as a daemon (a background service) on Linux
- The kubelet runs as a linux system service not a daemonset, and it may be down on a node, know this for the exam
- To list all the services on a linux system and find the kubelet, run:

```bash
sudo systemctl list-unit-files --type service --all | grep kubelet -a6
```

- To check the kubelet service, run: `systemctl status kubectl`
- To enable it if disabled (on start-up), run: `systemctl status kubectl`
- To start the service, run: `systemctl start kubectl`

- `journald` collects logs about the kubectl service on each node
- `journalctl` is used for viewing logs on a Linux system, collected by `systemd`
- `systemd` is the primary daemon in Linux that controls all processes

- To find logs related to kubectl or containerd, commands can be used to search the logs

```bash
journalctl -u kubelet
sudo journalctl -u containerd
```

- Pod logs are stored in `/var/log/pods` and can be easily retrieved with `kubectl logs <pod> -n <optional namespace>`

## 1.8 Delcarative syntax

- YAML files called manifests declare the kubernetes resources to create
- `kubectl` has three ways of submitting files to the Kubernetes API
  - `kubectl create` expects that no resource has been created yet
  - `kubect apply` can be used to create or update an exist resource
  - `kubectl replace` will delete any existing resource and create a new one