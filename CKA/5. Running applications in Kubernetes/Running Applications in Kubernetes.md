# Running Applications in Kubernetes

## 5.1 Orchestrating Applications

- Stateless applications are deployed using **Deployments**
- Stateful applications are deployed using **StatefulSets**. Statefulsets are not tested on the exam

### 5.1.1 Modifying running applications

- Scale a deployment using the `k scale` command and providing the `--replicas=` parameter
- Update the image in a deployment by running the `k set image deploy <name> <container>=<image>:<version>`

### 5.1.2 Application maintenance

- When you create or update a deployment, Kubernetes automatically creates or updates a corresponding **ReplicaSet**
- The **ReplicaSet** maintains the desired number of identical pods for a specific version of an application
- List ReplicaSets in a cluster using `k get rs`
- To migrate a pod manifest to a deployment manifest, simply move the `spec:` property under the `template:` property in the deployment
- You can safely ignore the warning **resource is missing a "last-applied configuration"**
- **Tip** If you want to describe everything (i.e. all replicaSets) all at once, instead of listing the names one-by-one in the command line, you can just type the name of the resource (e.g. kubectl describe rs) and the output will include all resources that currently exist in the default namespace.
- The **rollout strategy** controls how updates are made to a deployment
- The default rollout strategy for a deplyoment is **rolling update**, the other strategy is **recreate**, which will result in downtime
- With the `rollingUpdate` strategy, the `maxSurge: x%` and the `maxUnavailable: x%` control how many additional pods are added and the max percentage that can be unavailable
- **Recreate** is a faster rollout strategy than **Rolling Update**, but it does result it downtime
- Use `kubectl explain` to determine where the values go in a YAML manifest, use `--recursive` to navigate down the fields

### 5.1.3

- Use the `kubectl rollout history deploy <name>` command to view the rollout history of a deployment
- Use `kubectl annotate deploy <name> kubernetes.io/change-cause="whatever reason"` to annotate a rollout
- You can pause a rollout with `kubectl rollout pause`


### 5.1.4

- Exposing deployments can be achieved easily using `kubectl expose`
- The default is `--type ClusterIP`, but `NodePort` and `LoadBalancer` are also supported
- Selectors are used to associated a service with the pods in a deployment

## 5.2

- Use `drain`, `cordon` and `uncordon` to prepare a node for maintenance
- After you cordon a node, make sure you uncordon it. If the node has scheduling disabled, you may get points deducted from your final score.
- Be aware of tolerations and tains

### 5.2.2 Adding nodes to the cluster

- When you first create a Kubernetes cluster, kubeadm creates an initial bootstrap token with a 24-hour TTL, but you can create additional tokens on-demand
- Review `kube-apiserver.yaml` in `/etc/kubernetes/manifests/` to see if `--enable-bootstrap-token-auth=true` is configured
- `kubeadm --help` will be very useful on exam day, potentially allowing you to easily copy and paste commands
- Step through this process to test adding a node to a cluster:
  1. Delete the two node cluster if it exists `kind delete cluster --name kind-2-node`
  2. Create a two node cluster `kind create cluster --name kind-2-node --config 2-node.yaml`
  3. Exec into the control plane and delete the second node:

  ```bash
  docker exec -it kind-2-node-control-plane bash
  kubectl delete no kind-2-node-worker
  ```

  4. Print the join command `kubeadm token create --print-join-command`

  5. `exit` the Control Plane node and then `docker exec` into the worker node `docker exec -it kind-2-node-worker bash`

  6. Run `kubeadm reset`  to reset the node to a fresh state and clear it's association with the cluster

  7. Run the join command from step 4.

### 5.3.1 Helm

- During the exam look at the weblinks on the top of the question about installing the helm package. These links usually point to URLs that you are allowed to access and they contain information for you to complete the question, in that case, the URL of the helm chart to be added.
- In order to use a Helm chart, we first must add the repository where the Helm chart is stored, or else Helm won’t know where to pull the chart from, for example `helm repo add hashicorp https://helm.releases.hashicorp.com` or `helm repo add metallb https://metallb.github.io/metallb`
- The templating engine takes a file that has values to plug in to the template, which changes the configuration of our Helm chart.
- Helm charts use the se the templating syntax `{{ .values.name }}`
- Apply values with `helm install --values` and provide a `yaml` file, for example `--values values.yaml`