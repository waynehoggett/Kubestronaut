# Kubernetes Resources

- A Kubernetes resource is a Kubernetes API endpoint that stores RESTful objects of a certain type.
- You can get a list of available resource types using the command `kubectl api-resources`.
- Get documentation for a resource using `kubectl explain`.
- Use an init contnainer to run a task before a Pod's main container starts up.
- You can create your own custom resource types using a `CustomResourceDefinition`.
- A **pod** is a group of one or more containers, for example, a main container, an init container and a side car container.
  
- A **ReplicaSet** ensures a given number of replica Pods are running at any time.
- A **Deployment** provides a way to declaratively update ReplicaSets and Pods. Great for scaling stateless applications. Uses the default RollingUpdate deployment strategy for zero-downtime deployments.
- Imperative command to create a deployment: `kubectl create deployment <name> --image=<image> --replicas=replicas`
- Deployments use the `selector` to determine the pods that it's managing.
- A **Stateful** set is similar to a deployment, but for stateful applications. Replica Pods have a sticky identity (same Pod name, network hostname, even if recreated on a different node). Requires you to manually create a headless service.
- A **DaemonSet** dynamically runs a replica pod on each Node or just some nodes.
- A **Job** runs a containerized task to completion. Automatically retries if it fails.
- A **CronJob** runs jobs repeatedly according to a schedule.