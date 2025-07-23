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
