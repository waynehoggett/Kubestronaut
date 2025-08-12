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

## Multi-node Cluster

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

## Post Install Setup Steps

```bash
# Install and Configure VIM
apt update
apt install -y vim
echo "set expandtab" >> ~/.vimrc
echo "set tabstop=2" >> ~/.vimrc
echo "set shiftwidth=2" >> ~/.vimrc

# Set up K Alias
echo "alias k=kubectl" >> ~/.bashrc

# Set up Autocomplete
apt install bash-completion
echo '# Source bash completion
if [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
fi' >> ~/.bashrc
echo "source <(kubectl completion bash)" >> ~/.bashrc
echo "complete -o default -F __start_kubectl k" >> ~/.bashrc
source ~/.bashrc
```

## Create an Ingress-enabled cluster

1. Create a kind cluster with additional exposed ports:

    ```bash
    kind create cluster --name ingress --config C:\repos\Kubestronaut\CKA\kind\ingress.yaml
    ```

1. Exec into the cluster control plane

    ```bash
    docker exec -it ingress-control-plane bash
    ```

1. Install an ingress Controller:

    ```bash
    kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
    ```

    OR 

    ```bash
    kubectl apply -f https://raw.githubusercontent.com/chadmcrowell/acing-the-cka-exam/main/ch_06/nginx-ingress-controller.yaml
    ```

## Delete a Cluster

1. Delete a cluster

    ```powershell
    kind delete cluster -n <name>
    ```
