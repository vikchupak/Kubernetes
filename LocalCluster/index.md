# DNS

- https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns/#Linux
- https://minikube.sigs.k8s.io/docs/handbook/accessing/
  ![image](https://github.com/user-attachments/assets/bbe8b263-fb8d-498e-80a1-6b7fabf3d734)

# Minikube

Useful links
- https://kubernetes.io/
- https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download
- https://kubernetes.io/docs/tasks/tools/
- https://kubernetes.io/docs/reference/kubectl/kubectl/
- https://kubernetes.io/docs/reference/kubectl/

Minikube
- Local Kubernetes. One node cluster where `Control Plane Processes` and `Worker Processes` run on one node/machine.
- Has docker container runtime pre-installed.
- Uses/installs `kubectl` native Kubernetes command-line tool to run commands against Minikube Kubernetes clusters  as Minikube has kubectl as a dependency.
- Can be deployed/run as a VM, a container, or bare-metal on your host.
  - Minikube can be run as a docker container(if the driver is docker).
  - At the same time, uses pre-installed docker container runtime to run containers inside Minikube cluster.

```bash
minikube status
```
```bash
minikube start
```
```bash
# Start k8s nginx ingress controller implementation
minikube addons enable ingress
```
- https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns/#Linux

**In Minikube**, LoadBalancer service and Ingress require a tunnel to allow external access to the cluster.
- https://minikube.sigs.k8s.io/docs/handbook/accessing/
- https://minikube.sigs.k8s.io/docs/commands/tunnel/
```bash
# For Ingress
minikube tunnel
```
```bash
# For LoadBalancer service
minikube service <service-name>
```
