# Kubernetes Networking

- Cluster Network
    - Kubernetes uses a virtual cluster network to allow containers to communicate transparently regardless of which node they are on
- Cluster DNS
    - Kubernetes Services can be accessed using hostnames because all containers are automatically configured to use the cluster DNS server 
- Network Policy
    - All pod network traffic is allowed by default
    - Network policies select pods that they are assigned to
    - Pod traffic
    - Network policies provide rules that define what incoming and/or outgoing traffic is allowed