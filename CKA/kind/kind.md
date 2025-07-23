# kind

## Install Kind

1. Install Docker Desktop
1. Install Kind

    ```powershell
    winget install Kubernetes.kind
    ```

1. Restart shell

## Create a Single Node Cluster

1. Create cluster

    ```powershell
    kind create cluster
    ```

### Multi-node Cluster

1. This also sets a name so it can coexist with the other cluster

    ```bash
    kind create cluster --name kind-2-node --config 2-node.yaml
    ```

1. Connect:

    ```bash
    docker exec -it kind-2-node-control-plane bash
    ```
## Connect to a Cluster (Single Node)

1. Docker exec into the control plane node

    ```powershell
    docker exec -it kind-control-plane bash
    ```

### Delete a Cluster

1. Delete a cluster

    ```powershell
    kind delete cluster
    ```
