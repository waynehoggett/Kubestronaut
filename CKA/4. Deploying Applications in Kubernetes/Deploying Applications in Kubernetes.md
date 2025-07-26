# 4 Deploying Applications in Kubernetes

## 4.1 Scheduling Applications

- Check the labels on a node with the command `k get no -–show-labels`
- Apply a label using `k label <object> <object-name> <label>=<value>

### 4.1.1 Node Selectors

- Select a node in YAML using a nodeSelector:

    ```yaml
    spec:
        containers:
        - image: nginx
            name: ssd-pod
            resources: {}
        nodeSelector:
            disktype: ssd
    ```
- You can add a Node Selector or Node Name to a manifest (but not both)
- A **daemonSet** is a type of Kubernetes object that ensures that one pod replica is always running on every single node in the cluster at all times, `kube-proxy` is an example of this
- You may see node selector or node name on the exam, where the question might ask you to add the necessary values to an existing pod YAML. So, become familiar with the placement and proper syntax used here.

### 4.1.2 Node and pod affinity

- A node selector rule will select a node with a label, whereas an affinity rule will prefer a node with a label, but will also accept scheduling to another node if that node label is not present
- See the `requiredDuringSchedulingIgnoredDuringExecution` value below requires linux during scheduling
and the `preferredDuringSchedulingIgnoredDuringExecution` prefers, but does not require an `ssd` label

    ```yaml
    spec:
    containers:
    - image: nginx
        name: affinity
        resources: {}
    dnsPolicy: ClusterFirst
    restartPolicy: Always
    affinity:
        nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
            - key: kubernetes.io/os
                operator: In
                values:
                - linux
        preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
            preference:
            matchExpressions:
            - key: disktype
                operator: In
                values:
                - ssd
    ```
- You can specify a weight between 1 and 100 for each preferred rule. The weight assigns a priority score (1-100) to a preference. When the Kubernetes scheduler evaluates nodes, it adds the weight of matching preferences to a node's score. Higher weights increase the likelihood of selecting nodes that match the preference.

- Specify inter-pod affinity using a `podAffinity` configuration:

    ```yaml
    spec:
    containers:
    - image: nginx
        name: pod-affinity
    affinity:
        podAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                - key: run
                operator: In
                values:
                - nginx
            topologyKey: "kubernetes.io/hostname"
    ```

## 4.2 Pod metadata


### 4.2.2 Resource requests and limits

- Use requests to specify CPU and RAM that a pod requires
- Use limits to specify the maximum CPI and RAM that a pod can use
- Use either, both or none of limits and requests, and specify CPU, memory, neither or both
- A container is not allowed to use more than its resource limit
- Apply resource requests and limits for a pod by adding it into the container spec via YAML. Inline with the name and image of the container, you can add requests and limits as values below the resources

    ```yaml
    spec:
    containers:
    - image: nginx
        name: nginx2
        resources:
        requests:
            cpu: "100m"
            memory: "128Mi"
        limits:
            cpu: 2
            memory: "1Gi"
    ```

- 1 CPU unit is equivalent to 1 physical CPU core, or 1 virtual core, depending on whether the node is a physical host or a virtual machine running inside a physical machine. You can write the value in millicores, whereas the value “1000m” would be the same as “1 CPU”.

### 4.2.2 Multi-container Pods

- `initContainers` are containers that are designed to run to completion
- `initContainers` can also run in a sidecar pattern, when the restartPolicy is set to **Always**
- Containers in the same pod also have the ability to share storage, very useful for logging


### 4.2.3 ConfigMaps

- Once you create the configMap object, you can inject it into a pod using various methods.
- You can inject it into a pod via an environment variable or via a volume mounted to the container. You can create a config map from literal (i.e. username and password), from a file (i.e. ca.crt), or from multiple files within a directory
- Create configmaps with `k create configmap <name>`
- Use as many `--from-literal=key=value` parameters as required to pass literal values into the config map
- Perform multi-line arguments by specifying the key and then a pipe symbol `|`, for example:

    ```yaml
    data:
    data: |
        some data
        some extra data
    ```

### 4.2.4 Secrets

- Secrets are similar to Configmaps, but they are base64 encoded
- Create secrets using `k create secret <type> <name>` for example:

    ```bash
    k create secret generic dev-login --from-literal=usename=dev --from-literal=password='S!B\*d$zDsb=' --dry-run=client -o yaml > dev-login.yaml
    ```

- Mount secrets in volumes using the `secret:` property on the volume

    ```yaml
    volumes:
    - name: secret-vole
        secret:
        secretName: <secret-name>
    ```
- You can also pass secrets as environment variables, use:

    ```yaml
    env:
    - name: ENV_NAME
      valueFrom:
        secretKeyRef:
          name: <secret-name>
          key: <key>
    ```