# Helm

- Install helm on Windows with winget

    ```PowerShell
    winget install Helm.Helm
    ```

- Install helm in the kind cluster

    ```bash
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    ```