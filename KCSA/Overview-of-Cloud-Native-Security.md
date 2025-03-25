# Overview of Cloud Native Security (14%)

## The 4Cs of Cloud Native Security

- The **Four Cs of Cloud Native Security**: Not just Kubernetes-specific, but many topics apply to Kubernetes
  - **Cloud**
  - **Clusters**
  - **Containers**
  - **Code**
- **Cloud**: 
  - Represents everything you control (e.g., cloud provider, co-located server, corporate data center)
  - Acts as the trusted computing base for a Kubernetes cluster
  - If vulnerable or misconfigured, components built on top (e.g., clusters) are at risk
  - Each cloud provider offers:
    - Security recommendations for running workloads securely
    - Unique tools/products (differ by provider, but similar in purpose)
- **Clusters**: 
  - Two areas of concern for securing Kubernetes:
    - Securing configurable cluster components
    - Securing applications running in the cluster
  - Attack surface varies by application:
    - Example: Service A (critical) depends on Service B (vulnerable to resource exhaustion)
    - Risk increases if Service B’s resources aren’t limited, worsened by Kubernetes’ flat network
- **Containers**: 
  - Four areas of concern for container security:
    - **Container Vulnerability Scanning**: Check for known issues
    - **OS Dependency Security**: Secure underlying dependencies
    - **Image Signing and Enforcement**: Verify trust in container content
    - **Disallowing Privileged Users**: Limit OS privileges
    - Use container runtimes with strong isolation
  - High-level mitigation:
    - Scan containers during image build for vulnerabilities
    - Sign images to ensure trust
    - Create users with minimal privileges (ties to OWASP Top Ten’s Security Context)
  - Deeper exploration of these topics covered later in the course
- **Code**: 
  - Five areas of concern for code security:
    - **Access over TLS**: Encrypt communication
    - **Limiting Port Ranges**: Restrict service-to-service communication
    - **Third-Party Dependency Security**: 
      - Scan applications, third-party libraries, and container images
    - **Static Code Analysis**: 
      - Tools like SonarQube
      - Language-specific libraries (e.g., Goseq for Go)
    - **Dynamic Probing Attacks**: Protect against SQL injections, XSS, etc.

## Cloud Provider and Infrastructure Security

- Each provider offers tools and services for securing infrastructure, particularly Kubernetes-based workloads, with overlapping and unique features
- Azure security splits into three main areas:
  - **Microsoft Defender for Cloud**: Centralized security management and threat protection
  - **Microsoft Entra**: Identity and access management (authentication/authorization)
  - **Azure Container Registry**: Secures the container supply chain (build environment and registry)
- Azure Kubernetes Service (AKS) security includes:
  - Container Security: Protects the end-to-end pipeline (build → applications → workloads)
  - Key components:
    - **Pod Security Standards**: Enforces security policies for pods
    - Secrets Management: Securely stores sensitive data
    - Entra ID: Integrates identity management
    - **Microsoft Defender for Containers**: Offers end-to-end visibility and protection for containers
    - Azure Policy: Enforces security and compliance rules (e.g., network policies)
    - Azure Key Vault: Manages cryptographic keys and secrets
    - Network Security Policies: Controls traffic flow
  - Cluster Upgrades: Easier OS security updates and upgrades compared to on-premises clusters
- AWS security highlights for Elastic Kubernetes Service (EKS):
  - Combines security components for a robust authentication/authorization framework
  - Built-in Policies: Similar to Azure’s network policies, used to secure applications
  - Microsoft Defender for Containers: Provides visibility (note: likely a script error; this is an Azure service, not AWS)
  - **EKS Best Practices Guide**:
    - Comprehensive documentation covering the shared responsibility model and security best practices
    - Example: Detailed guidance on Pod Security for EKS
  - Cluster Management: Supports OS security updates and upgrades, though less streamlined than AKS
  - Note: The guide is extensive; skim it for high-level understanding rather than memorizing
- GCP security highlights for Google Kubernetes Engine (GKE):
  - **Security Dashboards**: Provide visibility into security posture
  - **Out-of-the-Box CIS Compliance**: Aligns with Center for Internet Security (CIS) benchmarks
  - Policy Compliance: Uses Open Policy Agent (OPA) as its policy engine
  - **Google Anthos**: Enhances security posture with tools and CIS benchmark integration
  - GKE has more features and tooling than Azure and AWS, making it stand out in Kubernetes security
- General takeaways across providers:
  - Common Capabilities: At a high level, Azure, AWS, and GCP offer similar security features (e.g., pod security, network policies, identity management)
  - Differences: Vary based on the provider’s specific services and constraints
    - Example: GCP’s Security Center differs in appearance and subtle functionality from Azure Security Center, but the core purpose is similar
  - **Shared Responsibility Model**: A key concept (emphasized in AWS’s EKS documentation) where the provider secures infrastructure, and the user secures workloads
- Exam tips:
  - Focus on understanding the core security components of AKS, EKS, and GKE (e.g., policies, identity management, secrets)
  - Know the **shared responsibility model** and its application to Kubernetes in the cloud
  - Be familiar with **CIS benchmarks** (highlighted in GCP, relevant across providers)


## Controls and Frameworks



## Isolation Techniques

- Four main factors of isolation in Kubernetes:
  - **Namespaces**
  - **Network Policies**
  - **RBAC (Role-Based Access Control)**
  - **Policy Enforcement**
- High-level explanation of each factor:
  - **Namespaces**: 
    - Provide logical isolation (e.g., App A in Namespace A, App B in Namespace B)
    - Don’t offer true security—apps in different namespaces can still communicate
    - Enable setup of network policies tied to specific namespaces
  - **Network Policies**: 
    - Address the namespace security limitation
    - Allow configuration to control communication (e.g., apps in Namespace A can only talk to Namespace C, not Namespace B)
  - **RBAC**: 
    - Focuses on authorization—what users can do on a cluster after authentication
    - Examples: 
      - View pods only
      - Create pods
  - **Policy Enforcement**: 
    - Enables granular policies in the cluster
    - Example: Prevent use of the "latest" container image tag in production (often alpha/beta builds)

## Artifact Repository and Image Security

- Container images are artifacts stored in image repositories, making them a key entry point for attackers
- Security considerations for container images:
  - Need a place to store the image (e.g., for App A)
  - Require a method to secure it
- Majority of security depends on access control:
  - Who can access the container image repo?
  - Who can see it?
  - Who can modify, push changes, or create new versions?
- Overview of container image repositories:
  - **Docker Hub**:
    - Offers public/free and private/paid repos
    - Lacks built-in security features—security responsibility falls entirely on the user
  - **Artifactory (JFrog)**:
    - Provides robust out-of-the-box security features
    - Features include:
      - Vulnerability analysis for container images
      - Artifact flow control
      - Overall distribution management
    - Built-in container image scanning:
      - Confirms images aren’t compromised
      - Convenient as it’s integrated where images are stored, simplifying security
- Benefit of JFrog’s scanning:
  - While many container image scanners exist (to be discussed later), having one in the repo enhances security efficiency

## Workload and Application Code Security

- Securing pods in a Kubernetes cluster requires two levels of security:
  - **Workload Security**
  - **Application Security**
- **Workload Security Management**: 
  - Ensures security and integrity of applications running in a Kubernetes cluster
  - Involves:
    - Implementing security controls
    - Monitoring to prevent unauthorized access, data breaches, and other incidents
  - System admins need access to:
    - Monitoring tools
    - Logging tools
    - Network management tools
  - Helpful platforms/methods: 
    - Tools for building, deploying, and running (e.g., GitOps)
- **Application Security**: 
  - Focuses less on Kubernetes, more on the application code itself
  - Key concerns:
    - Scanning and checking code for security
    - Ensuring code in the container image (deployed as a pod) is secure
  - Questions to address:
    - Has the code been vetted?
    - Has it been checked with tools like SonarQube?
    - Has it been linted?
    - Are security linters available (e.g., Gosec for Go/Golang)?
    - Does it meet security and compliance protocols?
  - Goal: Secure code before it’s built into a container image and deployed
- Security can’t stop everything, but risks can be mitigated as much as possible
- Five tips for application security:
  1. **Scan the Code**: 
    - Ensure code inside the container is secure
    - Unsecure code can compromise the entire environment, regardless of cluster management
  2. **Scan the Container Image**: 
    - Use automated solutions for scanning
    - Scan images in the repo and when deployed in clusters
    - Continuous scanning is essential
  3. **Automated Security Tools**: 
    - Examples: Kubescape, Aqua Security, Kube-bench
    - Assist with continuous scanning and security enforcement
  4. **Least Privilege RBAC**: 
    - Critical for permissions and authorization
    - Over-privileged users/teams can lead to security issues if compromised
  5. **Give Containers Only What They Need**: 
    - Use **Security Context** in container specs (from Kubernetes OWASP Top 10)
    - Examples:
      - Prevent containers from running as root (usually unnecessary)
    - Configure manifests with specific **Service Accounts**:
      - Avoid using the default service account for all apps
      - Default account compromise can jeopardize the entire cluster
- Implementing these five tips improves security significantly