# Kubernetes Threat Model

## Kubernetes Trust Boundaries and Data Flow

- Transition from blue team (defensive) to offensive security in Kubernetes
- Previous focus: Mitigating risks (core of cybersecurity)
- New focus: **Threat modeling** and offensive security
- **Threat Modeling**: 
  - Identifies, communicates, and understands threats and mitigations
  - Structured representation of security-affecting information for an application
  - Views app/environment through a security lens
  - Applicable to:
    - Software, apps, systems, networks, distributed systems, IoT, business processes
  - Components:
    - Description of the subject
    - Assumptions (to be checked/challenged as threats evolve)
    - Potential threats
    - Mitigation actions
    - Validation methods and success verification
  - Purpose:
    - Captures, organizes, and analyzes security info
    - Informs app security decisions
    - Produces prioritized security improvements (concept, requirements, design, implementation)
- Demo: Kubernetes Threat Model (CloudSecDocs):
  - Source: CloudSecDocs (rare detailed Kubernetes threat model)
  - Threats and controls:
    - **External Attackers**: 
      - Controls: API server authentication/authorization, Kubelet authentication
    - **Malicious Containers**: 
      - Controls: Network policies (privilege escalation mitigation)
    - **Malicious Users/Stolen Credentials**: 
      - Controls: RBAC policies
      - Note: Pod Security Policies deprecated/removed (ignore references)
    - **etcd**: 
      - Controls: Proper access, client certification, backups
  - Familiarity: Covers topics already discussed (e.g., RBAC, network policies, etcd security)
- **Trust Boundaries**: 
  - Segregate components in a data flow diagram
  - Based on:
    - Sensitivity
    - Level of access (for users, groups, apps)
  - Examples:
    - Internet API
    - Server control plane components
    - Data in etcd
    - Worker nodes and containers
- **Data Flow**: 
  - How data moves through Kubernetes (large topic)
  - High-level overview via documentation:
    - **Node to Control Plane**: Worker node communication to control plane
    - **Control Plane to Worker Nodes**: Reverse communication
    - **Connectivity Services**: E.g., SSH tunnels
  - Docs provide:
    - Short, clear understanding
    - Secure communication methods between control planes and worker nodes
  - Recommended for exam study

## Persistence


## Denial of Service

- Long-standing attack type (pre-dates Kubernetes)
- Notorious and challenging to handle
- Nature of DDoS:
  - Causes apps/services to become unavailable
  - Overwhelms resources (exceeds capacity), leading to:
    - Crashes
    - Unresponsiveness
  - Involves multiple sources (usually bots) across geographical locations
  - Example: Thousands of spam calls blocking a legitimate call
- In Kubernetes:
  - Targets apps via external sources when services are Internet-exposed
  - Internal risk:
    - Attackers with a foothold can infect workloads with malware
    - Amplifies DDoS or standard denial of service attacks
  - Mitigation:
    - Strong **zero-trust workload access control policy**
    - Restricts lateral movement within the cluster
- **Prevention in Kubernetes**: 
  - Relies on previously discussed security measures:
    - Robust **network security**
    - Hardened clusters
    - Proper **ingress and egress traffic** controls for pods
- Key focus:
  - DDoS attacks are 100% external (outbound-originating)
  - Importance of **network policies**:
    - Protects against incoming (**ingress**) traffic
    - Critical for defending against DDoS

## Malicious Code Execution and Compromised 

- Harmful code, scripts, or automation designed to:
    - Create system vulnerabilities (e.g., backdoors)
    - Cause security breaches, information/data theft, or other damages
- Targets files, computing systems, and servers
- Challenge:
  - Not always blocked by antivirus alone
  - No antivirus runs on Kubernetes by default
  - Engineer’s responsibility to prevent it
- **Prevention Tips**: 
  - Change passwords regularly
  - Keep software, apps, and clusters up to date
  - Backup clusters and test those backups
  - Use **network policies**, **eBPF**, and monitor **audit logs**
- Broader Implications:
  - Can overlap with DDoS but focuses on misconfiguration
  - Relevant to Kubernetes (databases like PostgreSQL can run in clusters)
- **Key Focus**: 
  - Mitigate via:
    - Updates (software, clusters, apps)
    - Proper configurations
    - Scanning container images for flaws

## Compromised Applications in Containers

- Focus: Preventative measures before compromise occurs
- Attackers exploit vulnerabilities in:
    - Main application software
    - Outdated code components
- **Prevention Steps**: 
  - Before containerization:
    - Thoroughly scan the application code
  - After containerization:
    - Continuously scan the **container image** to mitigate risks
- Demo in VS Code:
  - Example: Go app with a simple web API (e.g., homePage/aboutMe)
  - Focus: Scanning the code, not the app itself
  - Tool: **gosec** (Go security linter)
    - Command: `gosec` scans the current directory (e.g., main.go)
    - Results:
      - Two issues identified:
        - Line 50: **CWE-676** - Use of `net/http` serve function without timeout support
          - Severity: Medium
        - Line 28: **G104 (CWE-703)** - Unhandled errors
          - Severity: Low
          - Confidence: High
    - Assessment: Issues are minor but indicate misconfigurations
- **Key Insight**: 
  - Even small misconfigurations can lead to compromise
  - Steps:
    - Fix code issues first (e.g., timeouts, error handling)
    - Then package into a container image and scan it
- **Tools**: 
  - **gosec**: For Go-specific security linting
  - **SonarQube**: For broader static code analysis

## Attacker on the Network

- Stem from **poor networking**
- Training focus: Improving networking security
- Key networking security measures:
  - **Security-centric CNIs** (Container Network Interfaces)
  - **Network policies** (for ingress and egress)
  - **eBPF**
  - **iptables**
  - **Authentication and authorization configurations**
- Primary entry point:
  - External to the Kubernetes cluster: **Host networking**
  - Host network locations:
    - On-prem, Azure, AWS, or multiple places
- **Prevention Methods**: 
  - Monitor **audit policies** (previously discussed)
  - Monitor the host network (cloud or on-prem)
  - Ensure **firewall rules** are in place
  - Monitor all activity
  - Enforce **security policies**
- Levels of concern:
  - **Cluster Level**: 
    - Two major entry points:
      - **API Server**: Handles all interactions
      - **etcd**: Stores all cluster data
    - Must be highly secured
  - **Network Level**: 
    - Secure host-level networking (e.g., firewalls)
    - Control pod communication:
      - **Ingress**: Incoming traffic
      - **Egress**: Outbound traffic
    - Use network policies
  - **Pod Level**: 
    - Risk: Malicious code in a container image deployed as a pod
    - Potential issues:
      - Zero-day exploits
      - Distributed denial of service (DDoS)
      - Various application-level problems
    - Process:
      - App code → binary/artifact → container image → container in a pod
    - Requires deep-level security (multiple layers)
- **Key Action**: 
  - Properly **scan the code** to catch vulnerabilities early

## Access to Sensitive Data

- Two main areas of concern:
    - Access to the code base within containers
    - Access to **etcd**
- **Primary Concern**: 
  - **etcd**: 
    - Stores all stateful data in Kubernetes
    - Contains all configuration data
    - Previously discussed security measures apply
- **Secondary Concern**: 
  - Containers:
    - Beyond the code itself: **Volumes** and **databases** used by pods
    - May hold sensitive data (e.g., client information)
- Examples of sensitive data locations:
  - Databases:
    - Local databases
    - AWS RDS
    - Azure databases
    - MySQL on a data center server
  - Volumes:
    - Store pod data
  - etcd:
    - Kubernetes data store
- **Prevention Methods**: 
  - **Proper authentication and authorization**
  - **Firewall rules**
  - **Proper backups**
- Scope:
  - Encompasses everything from:
    - **Secrets**: Tokens, API keys, passwords
    - Databases storing application data
    - Volumes and etcd holding pod/Kubernetes data

## Privilege Escalation

- Refers to a security vulnerability or attack in Kubernetes
- Involves an unauthorized user gaining elevated privileges in a cluster
- **Impact**: 
  - Grants attackers:
    - Unauthorized access
    - Control over cluster resources
  - Leads to further compromise of cluster security
- **What It Means**: 
  - Unauthorized access by:
    - Person, bot, program, script, or application
  - Examples of excessive access:
    - Access to a specific cluster
    - Access to a specific namespace
    - Ability to create pods/deployments (when limited to listing only)
    - Containers running as **root** via security context (when not intended)
- **Prevention**: 
  - Focus on:
    - **Proper authentication**
    - **Proper authorization (RBAC)**