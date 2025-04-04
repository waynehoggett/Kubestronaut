# Kubernetes Security Fundamentals (22%)

## Pod Security Standards

- **Pod Security Standards**: 
  - Define three policies covering the security spectrum
  - Policies are cumulative, ranging from permissive to restrictive:
    - **Privileged**: 
      - Unrestricted policy
      - Widest level of permissions
      - Allows known privilege escalations
    - **Baseline**: 
      - Minimally restrictive policy
      - Prevents known privilege escalations
      - Permits default (minimally specified) pod configurations
    - **Restricted**: 
      - Heavily restricted policy
      - Follows current pod hardening best practices
      - Trades some compatibility for security
- Policy purposes:
  - Privileged: Fully open and unrestricted
  - Baseline: Balances ease of adoption for common workloads with security (blocks privilege escalations)
  - Restricted: Enforces pod hardening at the cost of compatibility
- Applying standards:
  - Set based on:
    - How pods interact in the environment
    - What the pods are doing
  - Example:
    - Dev image in testing: Minimal hardening needed (iterated frequently)
    - Final deployment: Apply stricter pod security standards

## Pod Security Admissions
 
- Builds on pod security standards
- Allows defining isolation levels for pods
- Purpose: Restrict pod behavior clearly and consistently
- **Enforcement**: 
  - Kubernetes provides a built-in **security admission controller**
  - Enforces pod security standards
  - Applied at the **namespace level** when pods are created
- Functionality:
  - Lets you specify which predefined **pod security standard level** to use for a namespace
  - Considered the **implementation phase** of pod security standards

## Authentication

- In Kubernetes:
  - Provides programmatic access to a cluster
  - Managed via **kubeconfig**:
    - Specifies which clusters you can access (not what you can do—that’s authorization)
    - Permissions defined within kubeconfig control cluster access
- **Kubernetes Authentication Limitations**: 
  - No native authentication management
  - No built-in API for authentication
- Common solution:
  - Use **OpenID Connect (OIDC)** solutions
- Examples:
  - **Azure (AKS)**:
    - Go to Kubernetes services → AKS cluster → IAM → "Add role assignment"
    - Select members to grant access (e.g., AKS test user can sign in)
    - Note: Access granted, but no actions allowed without roles (authorization covered later)
  - **AWS (EKS)**:
    - Go to IAM → Identity Providers
    - Set up an OIDC identity provider for the EKS cluster
    - Create users, roles, groups, and assign authentication/authorization access
- Importance:
  - Kubernetes lacks an out-of-the-box **authentication mechanism**
  - However, it does provide an out-of-the-box **authorization mechanism**

## Authorization

- Consistent across systems (Kubernetes, cloud, virtual environments)
- Defines what permissions you have on the system (Kubernetes in this case)
- Examples of permissions:
  - Can you create pods or just list them?
  - Can you create secrets?
  - Do you have access to etcd?
- Focus: What you can do on the cluster
- Terms used interchangeably:
  - Authorization
  - Permissions
  - **RBAC (Role-Based Access Control)**
- Role of **kubeconfig**: 
  - Holds authentication and authorization details
  - Determines what clusters and actions are available to you
- Kubernetes specifics:
  - No native authentication management
  - Native **RBAC** management available

## Secrets

- Objects holding small amounts of sensitive data (e.g., passwords, tokens, keys)
- Benefits:
    - Avoids embedding confidential data in pod specs or container images
    - Keeps sensitive data out of application code
  - Independent creation from pods:
    - Reduces risk of exposure during pod creation, viewing, or editing
- Additional precautions:
  - Kubernetes and apps can avoid writing secrets to non-volatile storage
- Comparison:
  - Similar to **ConfigMaps**, but designed for confidential data
- **Key Consideration**: 
  - May not be on the exam (not in domain objectives), but critical to understand
  - Kubernetes stores secrets in **etcd**:
    - Used for all stateful, ephemeral data
    - Stored as **Base64 plaintext** (not encrypted)
  - Risk: Unencrypted storage in etcd
  - Common solution: Use third-party secret managers (e.g., **HashiCorp Vault**)
- Demo in VS Code:
  - Creating a secret:
    - Uses core API group, secret kind
    - Includes name, default type, and data
  - Issue:
    - Secret data (e.g., API keys, passwords, tokens) in manifest is Base64 plaintext
    - Manifest stored in Git, posing a security risk
  - Workaround:
    - Tools like **Bitnami Sealed Secrets** (still carries some risk)
  - Apply with `kubectl apply -f secret.yaml`
  - Verify with `kubectl get secret` (e.g., test secret created)
- Alternative approach:
  - Many organizations avoid native secrets due to risks
  - Use **HashiCorp Vault**:
    - Stores secrets more securely
    - Integrates with Kubernetes manifests
    - Requires annotations for:
      - Agent injection
      - Agent injection status
    - Creates a sidecar in the pod to utilize Vault-stored secrets

## Isolation and Segmentation

- Discussion starts with **single tenancy** vs. **multi-tenancy**
- **Single Tenancy**: 
  - Examples: One engineer or one app per cluster
  - Each developer gets their own cluster (exclusive access)
  - Security advantage:
    - Small attack surface (e.g., one app/namespace, no cross-app communication)
  - Downside:
    - Hard to manage, especially for small teams
- **Multi-Tenancy**: 
  - Examples: Multiple apps or engineers on one cluster
  - Team of developers shares a single cluster
  - Common approach: Organizations use multi-tenancy and isolate/segment workloads
- **Four Main Methods for Isolation and Segmentation**: 
  - **Namespaces**: 
    - First layer of defense (minimal security)
    - Segregate/isolate apps across namespaces
    - Limitation: Apps in Namespace A can still talk to Namespace B by default
    - Purpose: Enable policy setup (e.g., network policies tied to namespaces)
  - **RBAC (Role-Based Access Control)**: 
    - Key to isolation and segmentation
    - Applies broadly (e.g., cluster security, component security, app security)
    - Without proper RBAC, security is compromised
    - Engineers should prioritize RBAC for accurate permissions (users, teams, service accounts)
  - **Network Policies**: 
    - Control traffic flow at IP address/port level (OSI Layer 3 or 4)
    - Application-centric:
      - Specify how pods communicate with network entities
      - Entities defined by:
        - Allowed pods
        - Allowed namespaces
        - Allowed IP blocks (CIDR ranges)
      - Exceptions:
        - Pod can’t block itself
        - Traffic to/from node hosting the pod is always allowed
    - Use selectors for pod/namespace-based policies
    - IP-based policies use CIDR ranges
    - Hands-on demo planned in the last video of the section
  - **Policy Enforcement**: 
    - Involves creating system rules
    - Example: Prevent use of “latest” container image in production (often alpha/beta builds)
    - Prefer versioned container images
    - Tools:
      - Kubernetes **admission controllers** (out-of-the-box policy creation)
      - Specific engines: **OPA (Open Policy Agent)**, Kyverno
      - OPA is currently the most popular
    - Admission controllers require writing Go (may be a challenge for some engineers)

## Audit Logging

- Provides a security-relevant, chronological record of cluster actions
- Tracks activities from:
    - Users
    - Applications
    - Kubernetes API
    - Control plane
- Purpose:
  - Helps cluster admins answer:
    - What happened?
    - When did it happen?
    - Who initiated it?
    - On what did it happen?
    - Where was it observed?
    - From where was it initiated and to where was it going?
- **Process**: 
  - Audit records start in the **kube API server**
  - Each request generates an audit event at different execution stages
  - Events are:
    - Preprocessed by a policy (determines what’s recorded)
    - Written to a backend (persists the record)
  - Backend options:
    - Log files
    - Webhooks
- **Audit Stages**: 
  - **Request Received**: Event triggered when audit handler receives the request (before delegation)
  - **Response Started**: After response headers are sent, before the body (for long-running requests like `kubectl watch`)
  - **Response Complete**: Response body fully sent, no more bytes
  - **Panic**: Generated if a panic occurs
- Misconception in managed Kubernetes:
  - Applies to services like **AKS (Azure)**, **EKS (AWS)**, **GKE (GCP)**
  - Control plane (and components) is fully managed
  - Engineers may overlook audit logging since it’s control-plane-based
- Importance:
  - Must still manage and log audit logs
  - Managed services don’t:
    - Automatically fix security issues
    - Alert you to problems
  - Ties to the **shared responsibility model** in cloud environments

## Network Policy


- Defined using the named API group
- Specifies the **network policy object/resource**
- Includes:
    - Name
    - Namespace
    - **Pod selector**
    - Policy types
- **Policy Types**: 
  - Focus on **ingress** and **egress**
  - Can specify one or both
- **Key Insight**: 
  - Default behavior: Pods have full ingress/egress with no restrictions
  - Network policies enable proper security by controlling traffic

