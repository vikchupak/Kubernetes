Kubectl - Kubernetes command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.

```bash
kubectl get all|nodes|pods|services|replicaSet|deployment|secret|endpoints [-o wide]
# -o wide - for more detailed info
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
```bash
# Logs inside pod
kubectl logs <podName>
```
```bash
# Detailed info on a component
kubectl describe node|pod|service|replicaSet|deployment <componentName>
```
```bash
# Open pod interactive terminal
kubectl exec -it <podName> -- bin/bash
```
- This command attaches you to the default container of the Pod.
- If the Pod has multiple containers, Kubernetes defaults to the first container listed in the Pod's YAML manifest.
- You interact with this container's isolated file system.

```bash
# Open container inside pod in interactive terminal
kubectl exec -it <pod-name> -c <container-name> -- /bin/bash
```
