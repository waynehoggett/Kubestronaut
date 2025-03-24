# Kubernetes Services

 - A **Service** enables you to expose an application running on a set of pods as a single network entity
 - Service Types:
    - ClusterIP - Exposes pods as an IP address for use within the cluster, used for cluster internal use
    - NodePort - Exposes pods using a port on each node
    - LoadBalancer - Exposes pods externally using a cloud load balancer
    - ExternalName - Creates a DNS record pointing to an external location outside the cluster network
- A headless service has no cluster IP address, it doesn't proxy traffic
- A service without a selector will not select backend pods and will not create endpoints, you create the endpoints manually
- Service discovery is the process of automatically locating application services on a network, Kubernetes provides two methods for service discovery
    - **Cluster DNS** - Reference a service by hostname
    - **Environment Variables** - Kubernetes automatically adds environment variables to each container to provide information about services
- Ingress resources manage external access to applications within a cluster and can provide additional functionality like load balancing or SSL termination, Ingress routes traffic to services, which route traffic to pods