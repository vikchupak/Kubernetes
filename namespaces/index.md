- Way to organise resources
- Like virtual cluster inside a cluster

## 4 Namespaces per default
- `default` - Namespace where all resources are created by default.
- `kube-node-lease`
  - Heartbeats of nodes.
  - Each node has associated lease object in namespace. [Lease - means rent - resource that are temporary provided]
  - Defines availability of nodes.
- `kube-public` - Publically accessible data. A config map that contains CLUSTER info. Command `kubectl cluster-info` 
- `kube-system` - System namespace. We do not touch it.

Can be also created with config files.

List namespaces
```bash
kubectl get namespaces
```
Create namespace
```bash
kubectl create namespace <namespaceName>
```
