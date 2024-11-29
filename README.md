# Kubernetes

**Container orchestration** tool(system). Helps to **manage** containerized applications.

Uses container runtime like docker `containerd` on each node, NOT whole docker.

- minikube
- kubectl
- kubens
- kubelet (on a node, sends instructions to the container runtime (e.g., containerd) to create and manage the containers that make up the pod.
- containerd (manages the lifecycle of containers)
