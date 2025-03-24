# Scheduling

- Scheduling is the process of assigning a Pod to a Node.
- Scheduling occurs when a new Pod is created and has not yet been scheduled.
- Scheduler takes into account things like resource requirements, Pod affinity/anti-affinity, and taints/tolerations when selecting a node.
- A toleration is applied to a pod to indicate it will accept a taint applied to a node.
- Label a node using `kubectl label nodes <node-name> <label>=<value> `.