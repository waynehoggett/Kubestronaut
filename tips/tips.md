# Tips

## K Alias

```bash
alias k=kubectl
```

Or make it permanently available (refer to kind)

```bash
echo "alias k='kubectl'" >> ~/.bashrc
source ~/.bashrc
```

## Autocomplete

Type the namespace just after the kubectl to autocomplete the Kubernetes resources (press the Tab key to autocomplete the name of the resource). On the exam, they will have longer and more complicated names, which are prone to typos. Always copy and paste where you can, and use autocomplete to prevent typos!

## Node selectors

- Node selectors allow you to select a node that a pod gets scheduled to, based on the nodes labels
- Nodes have a bunch of labels by default, list them using:

```bash
k get no --show-labels
```

- List labels on pods with:

```bash
k get po -n kube-system --show-labels
```

## Install VIM on a Kind node

1. First connect to a node, then run:

    ```bash
    apt-get update
    apt-get install -y vim
    ```