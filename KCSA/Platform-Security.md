# Platform Security

## Supply Chain Security

- Platform engineering involves creating abstractions over complex platforms to simplify their use for engineers and developers.
- These abstractions, often third-party tools and services, must also be secure.
- For instance, a service mesh secures the network, but the security of the service mesh itself is critical.
- **Software supply chain security** integrates risk management and cybersecurity best practices to protect the software supply chain from vulnerabilities.
- The software supply chain encompasses everything touching the code in the software development life cycle (SDLC), including application development, CI/CD pipelines, and deployment.
- It includes networks of information about software components—such as infrastructure, hardware, operating systems, cloud services, clusters—the people who wrote them, and their sources (e.g., registries, GitHub repos, open-source projects).
- It also covers vulnerabilities that could harm software security.
- In a Kubernetes context, supply chain security applies to everything from the container image to its deployment in a cluster via a Kubernetes manifest.

## Image Repository Security

- **Docker Hub**, despite being a widely used container image repository, does not offer security features out of the box.
- **JFrog Artifactory**, as one example, does provide these security features.
- Features include **container contextual analysis** and **container image scanning**, which use the NVD (National Vulnerability Database) and likely CIS (Center for Internet Security) standards.
- Additional security features are available to experiment with.

## Observability

- **Observability** in Kubernetes revolves around **traces**, **logs**, and **metrics**.
- **Logging**: involves aggregating and storing event messages written by programs and systems.
- **Metrics**: focus on collecting time series data to predict ranges, forecast values, and display them in dashboards like Grafana or other UI-centric tools, with alerting capabilities.
- **Traces**: provide an end-to-end view of application health, tracking how events occur between microservices.
- For monitoring and observability, **Datadog** is a strong enterprise tool, while open-source options include the **Grafana, Prometheus, Loki, Tempo stack**.

## Service Mesh

- **Service mesh definition**: A dedicated infrastructure layer built into an app to control data sharing between different parts.
- **Examples**: Istio, Linkerd, Consul, and Connect.
- **Purpose**: Documents app interactions, optimizes communication, avoids downtime as apps grow.
- **Service interdependence**: Each app service relies on others (e.g., inventory database communicates with product page and shopping cart).
- **Complex communication**: Modern apps are networks of services, each performing specific functions, often reusing components like databases.
- **Problem addressed**: Overloaded services (e.g., inventory database) can disrupt performance.
- **Solution**: Routes requests between services, enhancing coordination of moving parts.
- **Key feature - mTLS**: Encrypts service-to-service traffic in flat Kubernetes networks.
- **Primary benefit**: Provides true network observability beyond just encryption.

## PKI

- Kubernetes requires PKI certificates for authentication over TLS.
- **Automatic generation**: When installing with kubeadm, required cluster certificates are auto-generated.
- **Custom certificates**: You can generate your own for enhanced security (e.g., avoiding private key storage on the API server).
- Certificate types and purposes:
  - Client certificates for Kubelet to authenticate to the API server.
  - Kubelet server certificates for API server to communicate with kubelets.
  - Server certificate for the API server endpoint.
  - Client certificates for cluster admins to authenticate to the API server.
  - Client certificates for API server to communicate with kubelets and etcd.
  - Client certificate/kubeconfig for controller manager to talk to API server.
  - Client certificate/kubeconfig for scheduler to talk to API server.
  - Client and server certificates for the front-proxy.
- **Security importance**: Ensures secure communication between Kubernetes control plane and worker node components.
- Unsecure communication would render Kubernetes impractical for use.

## Connectivity

- **Purpose**: Ensures secure communication between services, nodes, and external systems in a Kubernetes environment.
- Involves implementing proper encryption and secure communication protocols to protect data in transit.
- **Key component - PKI**: Kubernetes uses Public Key Infrastructure (PKI) to secure connectivity, relying on certificates for authentication and encryption.
- Tools like kubeadm automate certificate creation to establish trusted communication channels across cluster components.
- **Scope**:
  - Securing pod-to-pod communication.
  - Protecting node-to-node interactions.
  - Safeguarding connections to external systems or APIs.
- Network policies play a critical role in controlling traffic flow and restricting unauthorized access.
- **Security practices**:
  - Enforcing TLS for all service-to-service communication.
  - Regularly rotating certificates to maintain trust.
  - Monitoring connectivity for anomalies or breaches.
- Aligns with compliance goals by ensuring encrypted, auditable communication paths.
- Essential for mitigating risks like man-in-the-middle attacks or data interception in cloud-native platforms.

## Admission Control

- Admission controllers in Kubernetes are plugins that govern and enforce cluster usage.
- **Role**: Act as gatekeepers, intercepting authenticated API requests to modify or deny them.
- **OPA integration**: Open Policy Agent (OPA) uses OPA Gatekeeper as a shim to bridge communication between Kubernetes and OPA.
- Admission controller process has two phases:
  - Mutating phase (executed first).
  - Validating phase (follows mutating phase).
- Controllers can be mutating, validating, or both.
- **Example - LimitRanger**:
  - Mutating: Adds default resource requests and limits to pods.
  - Validating: Ensures pod resource requirements don’t exceed namespace limits.
- Comparison to OPA (from Section 4):
  - Admission controllers are Kubernetes’ built-in policy enforcement, written in Go.
  - OPA is preferred when:
    - Organizations lack Go expertise.
    - They need a policy enforcer for platforms beyond Kubernetes.
- **Limitation**: Admission controllers are Kubernetes-specific, while OPA supports multiple platforms.
- Useful for organizations running more than just Kubernetes.
