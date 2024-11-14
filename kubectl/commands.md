Kubectl - Kubernetes command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.

```bash
kubectl get nodes|pod|services|replicaSet|deployment
```
![image](https://github.com/user-attachments/assets/6c3d26d8-2152-4023-83bc-f3aa179de3e2)
- kubernetes is a default service. Always in the system.
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
## Debugging pods
```bash
# Logs inside pod
kubectl logs <podName>
```
```bash
# Detailed info on pod
kubectl describe pod <podName>
```
```bash
# Open pod interactive terminal
kubectl exec -it <podName> -- bin/bash
```
