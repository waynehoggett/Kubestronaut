# Kubernetes Storage

- A **Volume** provides external storage to your kubernetes containers to store application data
- A **Persistent Volume** defines a dynamically consumable storage resources
- A **Persistent Volume Claim** binds to a Persistent Volume and allows you to mount the storage resource in a pod
- A **Reclaim Policy** determines what happens when a PVC is deleted, there are a few options:
    - **Retain** - Reclaim manually
    - **Recycle** - Automatic reclamation via a simple data scrub
    - **Delete** - Delete the underling storage
- **Rook** is a storage orchestrator
- **ConfigMaps** and **Secrets** allow you to storage configuration values like secure credentials and pass tem to containers
- By default secrets are **not encrypted**, only base64 encoded
- **Immutable: true** ensures that data in in a **Secret** or **ConfigMap** cannot be changed once it is created