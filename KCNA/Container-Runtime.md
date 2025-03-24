# Container Runtime

- The Container Runtime is a piece of software responsbile for running containers. The kubelet communicates with the container runtime to run containers
- The Container Runtime Interface (CRI) is a standard protocol for communication between the kubelet and the Container runtime. Kubernetes supports multiple container runtimes through the container runtime interface (CRI)
    - **CRI-O** is built as a docker alternative, providing CRI compatibility for runc and Kata containers
    - **Containerd** is a CNCF project that emphasizes simplicity, robustness and portability.