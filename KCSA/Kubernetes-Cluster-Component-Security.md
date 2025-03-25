# Kubernetes Cluster Component Security (22%)

- Overall cluster security
  - Builds on securing individual components (pods, kube-proxy, etcd, scheduler, etc.)
  - Applies to both worker nodes and control plane
- Cluster security best practices:
  - Provision control planes with minimal Linux distributions (e.g., Alpine)
  - Avoid unnecessary application/library installations on nodes unless critical
  - Use **RBAC policies** to:
    - Restrict actions on nodes
    - Limit attacker access and information
  - Increase security to protect nodes
  - Enforce frameworks for kernel-level access control:
    - **SELinux**
    - **AppArmor**
    - **Seccomp**
    - These prevent escalation of security events by:
      - Restricting malicious processes at the kernel level
      - Reducing interference with other processes
- Continuous monitoring is essential:
  - Monitor logs, audit logs, suspicious behavior
  - Ensure encryption and backups are in place

## API Server and Controller Manager

- Cluster component security covers:
  - **Control Plane**: API server, etcd, scheduler, controller manager
  - **Worker Node**: Kubelet, container runtime (CRI), kube-proxy, pod, CNI, storage
- **API Server**: 
  - Critical role: Enables interaction with Kubernetes
  - Examples:
    - Runs commands like `kubectl apply` or `kubectl get pods`
  - All Kubernetes interactions go through the API server
  - Security concerns:
    - Potential vulnerabilities tied to user access
    - Key questions:
      - Do users have too much access via authentication, authorization, or RBAC?
      - Do they need access to every namespace?
      - Should they create pods or just view them?
      - Do they have cluster-wide access?
- **Controller Manager**: 
  - Ensures current state matches desired state
  - Example:
    - ReplicaSet controller maintains three pods if that’s the desired number
  - Security approach:
    - Minimal out-of-the-box securing needed
    - Focus on:
      - **Authentication**
      - **Authorization**
      - **Policy Enforcement**

## Scheduler

- **Scheduler**: Schedules workloads (e.g., pods) to appropriate worker nodes
- Scheduling criteria:
  - Based on resource needs (e.g., CPU, memory)
  - Targets worker nodes with:
    - Available resources
    - Healthy state (up and operational)
- Handles edge cases:
  - Worker node may be connected but down/not functioning
  - Scheduler avoids scheduling pods on such nodes
- **Securing the Scheduler**: 
  - Similar to securing the controller manager
  - No specific security needs unique to the scheduler
  - Relies on broader cluster security measures:
    - **Authentication**
    - **Authorization**
    - **Policy Enforcement**

## Kubelet and Container Runtime

- Worker nodes are replaceable by design:
  - If a node fails, Kubernetes reassigns its pods to other nodes
  - Securing worker nodes is less critical than the control plane
  - Security issues on a few worker nodes don’t pose a major threat to the cluster
- **Kubelet**: 
  - Agent running on every worker node
  - Functions:
    - Registers the worker node with the cluster
    - Reports node resources (e.g., CPU, memory) to the cluster
    - Watches the API server for new tasks (e.g., pod deployment)
  - Security concerns:
    - Exposes an HTTPS endpoint with powerful control over the node and containers
    - By default, allows unauthenticated access to this API
    - Production clusters should enable **Kubelet authentication and authorization**
- **Container Runtime (CRI)**: 
  - Enables containers to run inside pods (Kubernetes can’t run containers natively without it)
  - Security risks:
    - Flaws in the runtime software can lead to exploits
    - Example: 2009 Docker runtime vulnerability allowed containers to gain root access to the host
    - Note: Docker container runtime shim is no longer used
  - Mitigation strategies:
    - Use enforcement frameworks:
      - **SELinux**
      - **AppArmor**
      - Restrict actions of malicious containers when properly configured
      - Provide a second layer of defense against runtime vulnerabilities
    - Audit tooling:
      - Continuously monitor and audit logs/data streams from the runtime
      - Detect and alert on suspicious behavior (e.g., breaches from runtime vulnerabilities)
    - Auditing tools also identify broader security threats beyond runtime issues
- **Kubernetes Security Context**: 
  - Recommended unless strictly necessary:
    - Prevent containers from running in privileged mode
    - Restrict accessible resources
  - Ties to OWASP Top Ten (security context is a key focus)
  - Doesn’t directly prevent runtime vulnerabilities but:
    - Adds a layer of defense
    - Mitigates impact (e.g., prevents containers from running as root)

## KubeProxy

- **Kube-proxy**: 
  - Handles local networking
  - Manages network communications for all pods
- **Securing Kube-proxy**: 
  - Follows similar protocols as the Kubelet
  - Focus: **Restricting permissions**
- Additional concept: **eBPF** (extended Berkeley Packet Filter):
  - Not likely on the exam but important real-world knowledge
  - Allows complete removal of kube-proxy
  - Why remove kube-proxy?
    - Kube-proxy uses **iptables** (firewall rules) under the hood
    - Issues with iptables:
      - Slow scaling with many rules
      - Security not well guaranteed
    - eBPF benefits:
      - Eliminates kube-proxy and iptables
      - Creates a safer, more secure, highly scalable environment
  - Used by CNIs like Cilium and Calico
  - Advantages:
    - Improves network observability
    - Enhances security
    - Supports better scaling
  - Not exam-specific, but increasingly relevant in practice

## Pods

- **Pods**: 
  - Where containers run in Kubernetes
  - Process:
    - Application is stored in a **container image**
    - Container image runs a container
    - Container operates inside a pod
- **Securing Containers/Pods**: 
  - Key practices:
    - **Scanning code**
    - **Scanning container images**
- Pod security is a major focus because pods are where applications run
- Theory revolves around **container image security**
- Secure the environment from an application perspective
- Key actions (reiterated):
  - Scan container images
  - Scan application code inside containers
- **Sidecar Containers**: 
  - Extra containers running in the same pod as the application container
  - Example:
    - Application container + sidecar (e.g., Envoy proxy for a service mesh)
  - Security requirement:
    - Secure the entire pod by securing all containers within it

## etcd

- Kubernetes database (or data store)
- Stores all stateful and ephemeral information in Kubernetes
- Security importance:
  - If compromised, cluster apps and sensitive data are at risk
  - Arguably as critical (or more) than securing the API server
- **Securing etcd**: 
  - Key practices beyond authentication, authorization, and backups:
    - Enable **transport security** (refer to etcd documentation for details)
    - Kubernetes offers no native tools for securing etcd
  - Backup best practice:
    - Use `etcdctl snapshot save` command to back up etcd
    - Protects against ransomware or attacks disabling access to critical config
  - Reason: etcd is a database—admin access compromises all data
- **Secrets Management**: 
  - Best practice: Avoid storing sensitive secrets (e.g., passwords, access keys) in etcd
  - Default behavior:
    - Most Kubernetes distributions store secrets in etcd
    - Secrets are Base64-encoded plaintext (not secure)
  - Recommendation:
    - Use an **external secrets manager** instead
- Configuration:
  - etcd can run as:
    - External cluster
    - Part of the control plane
  - Ensure traffic to/from etcd is as secure as possible

## Container Networking and Client Security

- Beyond local cluster networking (e.g., eBPF, kube-proxy)
- Focus: How networking functions in Kubernetes
    - How pods get IP addresses
    - Where routers or firewalls fit in
- Kubernetes networking basics:
  - No built-in router or firewall
  - Uses a plugin: **CNI (Container Network Interface)**
- **Securing the CNI**: 
  - Means securing **ingress and egress traffic**
  - Examples:
    - Restrict communication (e.g., only Namespace A can talk to Namespace B)
    - Allow/deny outbound pod communication
    - Set up firewall rules and port restrictions
- Role of **Network Policies**: 
  - Critical for securing CNI
  - Detailed coverage in Section 3
  - Control ingress and egress traffic (e.g., block Namespace A from talking to Namespace B)
- Kubernetes network characteristics:
  - **Flat network** by default (provided by CNI)
  - Implications:
    - Pod A in Namespace A can talk to Pod B in Namespace B without restrictions
    - Offers isolation between namespaces, but apps can still communicate across them
- Security solution:
  - Configure network policies to enforce restrictions
  - Essential for securing the flat Kubernetes network created by the CNI

## Storage

- In Kubernetes, storage is managed as **volumes**
- Key security concerns for storage:
  - Ensure storage is backed up
  - Prevent malicious content (e.g., zero-day exploits or encrypted volumes requiring ransom)
- Best practices:
  - Backup volumes regularly
  - Test recovery processes (standard for any tech data/storage)
