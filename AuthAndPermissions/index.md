## Role based access control (RBAC)

- There are no User or Group **resources** in k8s
- Users and Groups in k8s are NOT managed as native resources like pods, services, or roles. Instead, Kubernetes relies on an external identity provider or authentication mechanism.

1. **External Identity**:
   - Kubernetes does not store or manage user/group objects internally.
   - Users/Groups are identified externally (e.g., certificates, tokens, or an external identity provider like OIDC, LDAP, or SSO systems).

2. **Authentication**:
   - Kubernetes verifies a user's identity using mechanisms like:
     - X.509 certificates
     - Bearer tokens (e.g., service account tokens or OIDC tokens)
     - Webhooks or custom authenticators
   - A user is represented by their unique name in the credentials (e.g., a username in a certificate's CN field).
   - **Authentication happens on `apiServer`**

3. **Service Accounts**:
   - While there is no `User` resource, **ServiceAccounts** are Kubernetes-native resources. They are used to provide credentials for applications or workloads running in the cluster to access Kubernetes APIs.

## Namespace permissions

For devs usually

`Role` resource/component
- namespaced
- controls what resources and what permissions to perform actions on these resources
- BUT do not define "WHO" can perform actions

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: my-namespace
  name: pod-manager
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "create", "delete"]
    resourceNames: ["allowed-pod"]
```

`RoleBinding` resource/component
- Link a Role to a user or a group

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: bind-pod-manager
  namespace: my-namespace
subjects:
  - kind: User
    name: jane-doe
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-manager
  apiGroup: rbac.authorization.k8s.io
```

## Cluster-wide permissions

For admins usually

`ClusterRole` resource/component
- Defines resources and access permissions to them cluster-wide
  - Volumes, namespaces, nodes - are cluster-wide
  - Pods - all pods regardless of namespace

`ClusterRoleBinding` resource/component
- Link a ClusterRole to a user or a group

## Service Accounts

`ServiceAccount` resource/component
- Link a `Role or ClusterRole` to a `ServiceAccount` with `RoleBinding or ClusterRoleBinding`. There are no service account groups in k8s.
- ServiceAccounts are namespaced but can be linked to Role or ClusterRole

```bash
kubectl create serviceaccount sa0
```

## Commands

Show roles
```bash
kubectl get roles
```

Show role permissions
```bash
kubectl describe role admin
```

Check permissions
```bash
kubectl auth can-i create deployments --namespace dev
```

# Summary

- ServiceAccount → An identity assigned to Pods (like a user for Pods).
- Role → Defines permissions (what actions can be performed on Kubernetes resources).
- RoleBinding → Grants a Role to a ServiceAccount.
- Pod → assunmes ServiceAccount.

### **1️⃣ Define a ServiceAccount**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```
- This is a Kubernetes identity that a **Pod** can use.

---

### **2️⃣ Define a Role (Permissions for Kubernetes Resources)**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```
- This **Role** allows reading Pods.

---

### **3️⃣ Bind the Role to the ServiceAccount**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: my-service-account
    namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
- This **RoleBinding** assigns the **Role (pod-reader)** to the **ServiceAccount (my-service-account)**.

---

### **4️⃣ Deploy a Pod Using This ServiceAccount**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      serviceAccountName: my-service-account  # 👈 Pod uses this ServiceAccount
      containers:
        - name: app
          image: nginx
```
- The **Deployment** creates a **Pod**, and the Pod assumes `my-service-account`.
