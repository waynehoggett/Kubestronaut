# CERTIFIED KUBERNETES APPLICATION DEVELOPER (CKAD)

## Application Design and Build 20%

## Application Deployment 20%

## Application Observability and Maintenance 15%

## Application Environment, Configuration and Security 25%

## Services and Networking 20%

## Tips and tricks

- Know how to change namespaces:
  ```
  kubectl config set-context --current --namespace=<namespace>
  ```
- [Markdown Cheatsheet](https://github.com/im-luka/markdown-cheatsheet?tab=readme-ov-file#lists)

- Create an alias for kubectl: `alias k="kubectl"`
- Use dry-run: `k create deploy [name] --image=[image name]:[tag] --dry-run=client -o yaml > deploy.yaml`
- Know that you can point a deployment to a folder: `kubectl apply/create -f ./`
- Set the KUBE_EDITOR="nano" so that the following `k edit` commands use nano instead of vi, for example: `KUBE_EDITOR=nano k edit deployment ...`
- Nano shortcuts, tips, and tricks
  - Alt + P to show spaces
  - Alt + L to show line numbers (useful for debugging)
  - Crtl + U to paste from clipboard
- Create short-lived interative pods with `k run -it --restart=never --image=alpine temp-pod`
