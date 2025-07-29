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
