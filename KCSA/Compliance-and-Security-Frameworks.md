# Compliance and Security Frameworks

## Compliance Frameworks

- Compliance focuses on **standards**, **protocols**, and **auditing**, not just defense or offense.
- Example: Healthcare startups needed **HIPAA**, **PHI**, and **high-trust certifications** to secure insurance company business.
- Without compliance, companies risked losing revenue.
- Tasks like enabling **BitLocker** on Windows devices were key, though not technically exciting.
- Not all compliance is dull—**CIS** (Center for Internet Security) offers an engaging implementation.
- Compliance acts as a **checklist** of best practices.
- For Kubernetes, key frameworks include:
  - **CIS Benchmarks**: List of best practices.
  - **NVD**: National Vulnerability Database for tracking vulnerabilities.
  - **NIST**: Database ensuring security standards.
- These frameworks ensure **best practices** are met and vulnerabilities are documented.
- Scanning against these databases verifies that deployments meet **security standards**.

## Threat Modelling Frameworks

- Security protocols in Kubernetes extend beyond just Kubernetes, covering **authentication** and **authorization**.
- Examples include hardening servers in clusters—applicable to all tech, not just Kubernetes.
- **Threat modeling** is a broader concept, not Kubernetes-specific.
- Threat modeling involves:
  - Identifying **security requirements**.
  - Checking **security threats**.
  - Understanding **potential vulnerabilities**.
- Security threats apply to servers, networks, desktops, laptops—everything in tech.
- **Security requirements** vary by industry: finance, healthcare, startups.
- **Vulnerabilities** differ based on the environment.
- For Kubernetes, the **zero-trust model** is key.
- Zero-trust relies on:
  - **Authentication**: Logging in mechanism.
  - **Authorization**: Defining what users can do once logged in.
  - **Continuous validation**: Adjusting access as roles change (e.g., team switches, more/less access).
- Concepts like authentication and authorization loop back into threat modeling and zero-trust in Kubernetes.

## Supply Chain Compliance

- **Supply chain compliance** ensures products, services, or materials meet **legal and ethical standards**.
- Not inherently tied to Kubernetes or technology—focuses on functionality and safety (e.g., "does it work or blow up?").
- In Kubernetes, supply chain security has a specific focus.
- CNCF blog highlights four key elements:
  - **Artifacts**: Container images, war files, etc.
  - **Metadata**: Information about software components.
  - **Attestations**: Verification of authenticity or integrity.
  - **Policies**: Rules governing the supply chain.
- Includes **SBOMs** (Software Bill of Materials) and **vulnerability scanning reports**.

## Automation and Tooling

- Kubernetes **CIS report** is over 200 pages
- Automation tools simplify compliance:
  - **Kubescape**: Scans clusters, pods, manifests, and **RBAC**; uses **CIS standards**; offers automatic remediation.
  - **Checkov**: Performs **static code analysis** on Kubernetes manifests.
  - **kube-bench** (Aqua Security): Focuses on cluster scanning, outputs to terminal, uses **CIS standards**.
- Kubescape includes an **RBAC visualizer** to map service accounts, roles, and bindings.
- Automation eliminates manual compliance checks.
- Benefits include time-saving and accuracy—click "fix" to remediate issues.
- **Open-source** and enterprise versions exist for Kubescape and kube-bench; Checkov likely has an open-source version.