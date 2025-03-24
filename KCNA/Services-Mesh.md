# Service Mesh

- A **Service Mesh** is a tool that manages communication between application components, often adding functionality like logging, tracing or encryption
- Two main components of a service mesh:
    - The **Service Proxy/Data plane** - Are managed sidecar containers running alongside the main container to facilitate (proxy) communication between services
    - The **Control plane** - Manages the Service Proxy/Data plane pods
- A sidecar is an additional container running inside a pod, alongside the main container
- Service Mesh options:
    - Linkerd
    - Consul Connect
    - Traffik Mesh
    - Istio
    - Kuma
    - F5 NGINX Service Mesh
    - And more
- A **Service Mesh Interface (SMI)** is a standard interface for service meshes in Kubernetes, you can configure any SMI-supporting service mesh using custom Kubernetes resources via the Kubernetes API