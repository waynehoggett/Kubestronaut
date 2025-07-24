# Identity and Access Management

- The built-in plugin that we use with kubectl is called certificate-based authentication using an X.509 certificate, in which the API server verifies the identity of a user based on a signed client certificate, stored in a kubeconfig file
- For the CKA Exam, it’s essential to understand X.509 certificate authentication (for user authentication), and service account tokens (for Pod authentication with the API).
- If the request is coming from a human - like when we are using kubectl - then the user is authenticated as a specific username
- If the request originates from a machine, it must have a service account, with a token to provide the Kubernetes API for authentication
- There is a default service account created automatically in every namespace with the name “default”

    ```bash
    kubectl get sa
    ```

- Service accounts are often associated with pods running in the cluster, which allow applications running inside the pod (as containers) to access the Kubernetes API and make requests. A token is mounted to each pod via service account

## 3.1.1 Roles and role bindings

- Four different types of objects in Kubernetes, the **role**, the **cluster** **role**, the **role** **binding** and the **cluster** **role** **binding**
- **Permissions must match resource scope**:
  - Namespace-scoped resources (like Pods, Services) require **Roles** within that namespace.
  - Cluster-scoped resources (like PersistentVolumes, Nodes, Namespaces) require **ClusterRoles** for proper cluster-wide authorization.
- Roles are scoped to both a namespace and a specific resource in Kubernetes (e.g. pods)
  - Each role specifies allowed actions using request verbs
  - Request verbs are lower-cased HTTP methods targeting specific API endpoints
  - Common verbs include: get, list, create, update, patch, watch, delete, deletecollection
- Role bindings will attach (or bind) a role to a user, group or service account and will provide that user, group or service account with a role.
- Test a users access without assuming the role of a user using `k auth can-i`. Be sure to use `--as <user>` to test another user's access
- You **cannot** attach a cluster role binding to a role because permissions from within a namespace (role permissions) can’ be applied to a subject that is not namespace-scoped
- Use `kubectl auth can-i --list` to list permissions for a user
- `*` is a wildcard for everything

## 3.1.2 System roles and groups

### System roles

- The API server creates a set of default cluster roles and cluster role bindings. They are directly managed by the control plane. You will see they are “cluster-admin”, “admin”, “edit” and “view”, view using `k get clusterrole | grep -v system`
- The cluster-admin cluster role is intended to be used cluster-wide, and allows read and write access for most objects including roles and role bindings, with the exception of resource quotas, endpoints, or the namespace itself.
- The edit cluster role will not allow you to modify roles or role bindings; however, it will allow you to access secrets and run pods with any service accounts inside the namespace

## 3.1.2 System roles and groups

- Like users, groups are not a managed resource in Kubernetes
- Built-in groups are managed by the API server and cannot be edited
- Built-in default cluster role bindings are “system:authenticated”, “system:unauthenticated”, and “system:masters”.
  - The `system:authenticated` group assigns privileges to users who are successfully authenticated.
  - The `system:unauthenticated` group is used when none of the authentication plugins could validate the request.
  - The `system:masters` group is used for super users and provides unfettered access to everything in Kubernetes.

## 3.2 Users and Groups

- Users and groups are arbitrary meanings in Kubernetes, so there is no user database or directory structure. The certificate is the only thing that’s checked, so the user or group can be any name.
- K8s is a CA that can sign certificates
- To create a [certificate for user](https://kubernetes.io/docs/tasks/tls/certificate-issue-client-csr/):
  1. Create a private key

  ```bash
  openssl genrsa -out carol.key 2048
  ```

  2. Create a certificate signing request file

  ```bash
  openssl req -new -key carol.key -subj "/CN=carol/O=developers" -out carol.csr
  ```

  3. Store the CSR in an ENV variable to make it easier to use later:

  ```bash
  export $REQUEST=$(cat carol.csr | base64 | tr -d "\n")
  ```

  4. Create the [YAML for a CSR](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/#create-certificatesigningrequest)

  5. Apply the CSR 

    ```bash
    k apply -f csr-carol.yaml
    ```

  6. Approve the CSR (Optionally list with `k get csr`)

    ```bash
    k certificate approve <name>
    ```

  7. Extract the contents of the certificate and base64 encode it

    ```bash
    kubectl get csr carol -o jsonpath='{.status.certificate}' | base64 -d > carol.crt
    ```

  8. Add credentials to `kubeconfig`

    ```bash
    k config set-credentials carol --client-key=carol.key --client-certificate=carol.crt --embed-certs
    ```
    
  9. View the config to confirm `k config view`

  10. Add to context:

    ```bash
    kubectl config set-context carol --user=carol --cluster=kind
    ```

  11. Optionally use it `kubectl config use-context carol`

## 3.3 Service Accounts

- Service accounts are namespace-scoped resources
- The “default” service account is created automatically each time you create a new namespace
- When we create a pod, this default service account is automatically mounted to the pod, so the pod can authenticate to the Kubernetes API
- Add `automountServiceAccountToken: false` to the pod spec to prevent a pod from automatically mounting the default serviceAccount
- Check access for a service account:

  ```bash
  k auth can-i <action> <resource> --as system:serviceaccount:<namespace>:<sa-name> 
  ```

- You cannot delete the default service account

