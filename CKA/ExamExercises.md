# Exam Exercises

# 1

1.1 Perform the command to list all API resources in your Kubernetes cluster. Save the output to a file named resources.csv.

```bash
kubectl api-resources > resources.csv
```

1.2 List the services on your Linux operating system that are associated with Kubernetes. Save the output to a file named services.csv.

```bash
sudo systemctl | grep kube
```

1.3 List the status of the kubelet service running on the Kubernetes node, output the result to a file named kubelet-status.txt, and save the file in the /tmp directory.

```bash
? systemctl kubelet > /tmp/kubelet-status.txt
```

1.4 Use the declarative syntax to create a Pod from a YAML file in Kubernetes. Save the YAML file as chap1-pod.yaml. Use the kubectl create command to create the Pod.

```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

1.5 Using the kubectl CLI tool, list all the Services created in your Kubernetes cluster across all namespaces. Save the output of the command to a file named all-k8s-services.txt.

```bash
kubectl get service --all-namespaces > all-k8s-services.txt
```

# 2 

## 2.1

There’s a need for company X to upgrade the Kubernetes controller to version 1.31 or higher, due to a bug that affects pod scheduling. Perform the update with minimal downtime and loss of service.

Using the kubectl CLI tool, get the output of the pods running in the kube-system namespace and show the pod IP addresses. Save the output of the command to a file named “pod-ip-output.txt”.

Upgrade the control plane components using kubeadm. When completed, check that everything, including kubelet and kubectl is upgraded to version 1.32.1.

## 2.2

Use the CLI tool that will allow you to view the client certificate that the kubelet uses to authenticate to the Kubernetes API. Output the results to a file named “kubelet-config.txt”

## 2.3

View the CRI implementation using the crictl command-line tool. Determine the container runtime in use.

Use the crictl command-line tool to list the running containers. Stop and remove the kube-apiserver container by its container ID and see what happens.

Use the kubectl command-line tool to determine what CNI is installed with the cluster. Also determine what version the CNI is currently running.

Use the kubectl command-line tool to list the CSI drivers in your Kubernetes cluster as well as the CSI storageClasses.

# 3

## 3.1 

Create a new role named `sa-creator` that will allow creating service accounts.

Create a role binding that is associated with the previous `sa-creator` role, named `sa-creator-binding` that will bind to the user `Sandra`.

## 3.2

Create a new user named “Sandra”, first creating the private key, then the certificate signing request, then using the CSR resource in Kubernetes to generate the client certificate.

Add that new user “Sandra” to your local kubeconfig using the kubectl config command.


## 3.3

Create a new service account named ’secure-sa’ and create a pod that uses the ‘secure-sa’ service account. Make sure the token is not exposed to the pod.

Create a new cluster role named ‘acme-corp-role’ that will allow the create action on deployments, replicates, and daemonsets. Bind that cluster role to the service account ’secure-sa’ and make sure the service account can only create the assigned resources within the default namespace and nowhere else. Use auth can-i to verify that the ‘secure-sa’ service account cannot create deployments in the kube-system namespace and output the result of the command + the command itself to a file and share that file.

# 4

## 4.1

Apply the label “disk=ssd” to a node. Create a pod named “fast” using the nginx image and make sure that it selects a node based on the label “disk=ssd”.

Edit the “fast” pod using kubectl edit po fast and change the node selector to “disk=slow”. Notice that the pod cannot be changed and the YAML was saved to a temporary location. Take the YAML in /tmp/ and apply it by force to delete and recreate the pod using a single imperative command.

Create a new pod named “ssd-pod” using the nginx image, and use node affinity to select nodes based on a weight of 1, to nodes that have a label disk=ssd. If the selection criteria doesn’t match, it can also choose nodes that have a label kubernetes.io/os=linux

## 4.2

Create a pod named “limited” with the image “httpd” and set the resource requests to “1Gi” for CPU and “100Mi” for memory.

Create a deployment named “overloaded” running three replicas of pods. Inside the pod is an initContainer and a main container. For the initContainer, use the busybox image and keep it running by issuing the command “/bin/bash sleep 1d”. Set the CPU requests to 250 millicores and Memory requests to 600 mebibytes. For the main container, use the nginx image. Set the CPU requests to 250 millicores and Memory requests to 600 mebibytes. See if the pods within the deployment get to a running state. If not, adjust the requests to get all three replicas up and running.

Create a configmap named “ui-data” with the key and value pairs below Apply a configmap to a pod named “frontend” with the image “busybox:1.28” and pass it to the pod via the following environment variables:


# 5

## 5.1

Create a deployment named “apache” that uses the image httpd:2.4.54 and contains three pod replicas. After the deployment has been created, scale the deployment to five replicas and change the image to httpd:alpine.


# 6

## 6.1

Exam Task In your cluster, change the IP addresses given to new Services to a CIDR range of 100.96.0.0/16. Change the IP address associated with the cluster DNS Service to match this new Service range. Proceed to change the kubelet configuration so that new Pods can receive the new DNS Service IP address, and so they can resolve domain names. Edit the kubelet ConfigMap so that the kubelet is updated in place and immediately reflected. Upgrade the node to receive the new kubelet configuration. Finally, test this by creating a new Pod and verifying that the Pod has the new IP address of the DNS Service.

## 6.2

exec into a Pod and cat out the DNS resolver file to see the IP address of the DNS server that the Pod uses to resolve domain names.

Open the file that contains the configuration for the kubelet and change the value for clusterDNS to 100.96.0.10. Save and quit the file.

Stop and reload the kubelet daemon. Verify that the Service is active and running.

Locate the kube-dns Service. Try to edit the Service in place by changing the value of both clusterIP and ClusterIPs to 100.96.0.10. When the values cannot be updated, force a replacement of the Service with the correct kubectl command-line argument.

Edit the ConfigMap that contains the kubelet configuration. Change the IP address value that is set for clusterDNS to 100.96.0.10. Make sure to edit the resource without writing a new YAML file.

Scale the CoreDNS Deployment to three replicas. Verify that the Pods have been created as a part of that Deployment.

Test access from a Pod to a Service by first creating a Deployment with the apache image, followed by exposing that Deployment. Create a Pod from the netshoot image, and verify that you can reach the Service that you just created.

Using the netshoot Pod created in the previous exercise, locate the Service in the default namespace by its DNS name. Use as few letters as possible for DNS search functionality.

## 6.3

Exam Task In your cluster, install an Ingress controller to proxy communication into the cluster via an Ingress resource. Then, create a Deployment named hello using the image nginxdemos/hello:plain-text. The container is exposed on port 80. Create a Service named hello-svc that targets the hello Deployment on port 80. Then, create an Ingress resource that will allow you to resolve the DNS name hello.com to the ClusterIP Service named hello-svc in Kubernetes.