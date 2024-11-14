- Way to organise resources
  - Better overview
  - Grouping resourses. Example:
    - Database
    - Monitoring
    - Elastic Stack
    - Nginx-Ingress
  - Avoid conflicts when many teams work in the same namespace, like conflicting (the same) names
  - Resource sharing - reuse the same components for Staging and Development env
  - Restrict access to namespaces for different teams. Limit resources(CPU, RAM, Storage) per namespace.
- Like virtual cluster inside a cluster

Limitations
- We cannot access most resources from another namespace
- BUT we can access service in another namespace like `service-name.namespace-name`
- Components that cannot be created within a namespace: Volume, Node. Command `kubectl api-resources --namespaced=false|true`

## 4 Namespaces per default
- `default` - Namespace where all resources are created by default.
- `kube-node-lease`
  - Heartbeats of nodes.
  - Each node has associated lease object in namespace. [Lease - means rent - resource that are temporary provided]
  - Defines availability of nodes.
- `kube-public` - Publically accessible data. A config map that contains CLUSTER info. Command `kubectl cluster-info` 
- `kube-system` - System namespace. We do not touch it.

***Can be also created with config files.***

List namespaces
```bash
kubectl get namespaces
```
Create namespace
```bash
kubectl create namespace <namespaceName>
```
