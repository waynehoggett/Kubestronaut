# 6. Communications in a Kubernetes Cluster

# 6.1 Configuring DNS

- CoreDNS is used internally
- Version 1.12 it is the default DNS servers
- There are multiple IP address ranges on a Kubernets cluster, and all must be unique
  - Nodes CIDR, aka Internal-IP is the subnet the worker node and control plan nodes are connected to
  - Pod CIDR - Also known as Cluster CIDR, each pod receives an IP address, this is configured during installation and is managed by the CNI (network plugin)
  - Service CIDR, also known as the Service-Cluster-IP-Range, used for persisent service IP addresses
- Some components of the `kube-dns` service cannot be edited while it is running, use `k edit` to update the manifest and then use `k replace -f <temp-filepath> --force` to forcibly replace it
- You can use commands like `systemctl stop kubelet systemctl start kubelet, systemctl restart kubelet, and systemctl daemon-reload` to manage the kubelet
- This is a handly image for troubleshooting DNS, it comes pre-installed with tools: `kubectl run netshoot --image=nicolaka/netshoot --command sleep --command "3600"`
- Don't follow this process, it's not supported and doesn't work!
1. To change the IP addresses given to new Services to a CIDR range:
  - Modify the API configuration in /etc/kubernetes/manifests/kube-apiserver.yaml
  - `vim /etc/kubernetes/manifests/kube-apiserver.yaml`
  - Edit `--service-cluster-ip-range=`
  - Save and close the file `esc` `:wq`
2. Change the IP address associated with the cluster DNS Service to match this new Service range
  - Edit the Service named kube-dns in the kube-system namespace
  - `k edit svc -n kube-system kube-dns`
  - It will error when you save, but edit both the `clusterIP` and `clusterIPs`
  - Save the temporary file, and then forcible apply it with `k replace -f <tmp-file-name> --force`
3. Change the kubelet configuration so that new Pods can receive the new DNS Service IP address, and so they can resolve domain names
  - Modify the kubelet configuration at `/var/lib/kubelet/config.yaml`
  - Change the section called `clusterDNS:` to the new service IP
4. Edit the kubelet ConfigMap so that the kubelet is updated in place and immediately reflected
  - `k -n kube-system edit cm kubelet-config`
  - Change the section called `clusterDNS:` to the new service IP
5. Upgrade the node to receive the new kubelet configuration
  - `kubeadm upgrade node phase kubelet-config`
6. Reload the daemon:
  - `systemctl daemon-reload`
7. Restart the service:
  - `systemctl restart kubelet`
8. Test by creating a new Pod and verifying that the Pod has the new IP address of the DNS Service
  - `kubectl run netshoot --image=nicolaka/netshoot --command sleep --command "3600"`
  - `k exec -it netshoot -- bash`
  - `cat /etc/resolv.conf`
  - Check IP address
  - `nslookup <some domain>`

## 6.2 Core DNS

- Enables fast name resolution within the cluster

### 6.2.1 Config Files

- The kubelet is responsible for creating the CoreDNS Pod and inkecting the configuration from `/var/lib/kubelet/config.yaml` into that pod, this is a special type of configuration file, just for the kubelet

### 6.2.2 Replicating DNS

- CoreDNS runs as a deployment that can be scaled up and down to improve DNS performance
- Run `kubectl -n kube-system get deploy` to view the system deployments

### 6.2.3 Pod-to-Pod connectivity

- The fully qualified domain name (FQDN) for Services in a Kubernetes cluster is <service-name>.<namespace-name>.svc.cluster.local
- The `/etc/resolv.conf` file in each pod provides the DNS Suffix search list for the pod, which includes the namespace the pod is in, and the svc.cluster.local and cluster.local namespaces
- Pods also all have a DNS address that is the IP address with dots converted to dashes, for example: `10-244-0-14.default.pod.cluster.local`


## 6.3 Ingress and Ingress Controllers



