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
1. To change the IP addresses given to new Services to a CIDR range:
  - Modify the API configuration in /etc/kubernetes/manifests/kube-apiserver.yaml
  - `vim /etc/kubernetes/manifests/kube-apiserver.yaml`
  - Edit ``
  - Save and close the file
1. To Change the IP address associated with the cluster DNS Service to match this new Service range
  - Edit the Service named kube-dns in the kube-system namespace
  - ``
1. Change the kubelet configuration so that new Pods can receive the new DNS Service IP address, and so they can resolve domain names
  - Modify the kubelet configuration at /var/lib/kubelet/config.yaml
1. Edit the kubelet ConfigMap so that the kubelet is updated in place and immediately reflected
  - 
1. Upgrade the node to receive the new kubelet configuration
  - 
1. Test by creating a new Pod and verifying that the Pod has the new IP address of the DNS Service
  - `kubectl run netshoot --image=nicolaka/netshoot --command sleep --command "3600"`
  - `k exec -it netshoot -- bash`
  - `cat /etc/resolv.conf`
  - Check IP address
  - `nslookup <some domain>`