Kubectl - Kubernetes native command line tool for communicating with a Kubernetes cluster's control plane(API server).

```bash
# Connect to remote cluster with kubectl
export KUBECONFIG=/path/to/your/kubeconfig
# By default, kubectl looks for the Kubeconfig file at ~/.kube/config
```

```bash
kubectl get all|nodes|pods|services|replicaSet|deployment|secret|endpoints|ingress [-o wide]
# -o wide - for more detailed info
# all - shows common resource types such as Pods, Deployments, Services, ReplicaSets, and StatefulSets, but not Secrets, ConfigMaps, or other non-pod-related resources
```
![image](https://github.com/user-attachments/assets/6c3d26d8-2152-4023-83bc-f3aa179de3e2)
- kubernetes is a default service. Always in the system.
```bash
# See deployment status in yaml format
kubectl get deployment -o yaml [> nginx-deployment-status.yaml]
```
```bash
# THERE ARE NO SUCH COMMAND [with pod] because usually we do not create pods directly
kubectl create pod
# Output
# error: Unexpected args: [pod]
```
```bash
# Create deployment via terminal
kubectl create deployment nginx-depl --image=nginx [options]
```
```bash
# Edit deployment [file] via terminal editor
kubectl edit deployment <deploymentName>
```
```bash
# Delete deployment via terminal
kubectl delete deployment <deploymentName>
```
```bash
# Crete or edit a component via config file (yaml)
# In the file, kind attribute used as component type
kubectl apply -f <fileName>
```
```bash
# Delete a component via config file (yaml)
kubectl delete -f <fileName>
```
## Debugging

### See logs

```bash
# By default, kubectl logs <podName> will only show logs from the first container inside the pod (if there are multiple containers).
kubectl logs <podName>

# To see logs from all containers in the pod at once, use the --all-containers flag
kubectl logs <podName> --all-containers=true

# If your pod has multiple containers, you need to specify which container's logs you want to see
kubectl logs <podName> -c <containerName>
```

### See componets info

```bash
# Detailed info on a component
kubectl describe node|pod|service|replicaSet|deployment <componentName>
```

### Go inside pods(containers)

```bash
# Open pod interactive terminal
kubectl exec -it <podName> -- bin/bash
```
- **Pods don't have their own file systems**; each container inside the Pod has its own isolated file system.
- This command attaches you to the default container of the Pod.
- If the Pod has multiple containers, Kubernetes defaults to the first container listed in the Pod's YAML manifest.
- You interact with this container's isolated file system.

```bash
# Open container inside pod in interactive terminal
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```
