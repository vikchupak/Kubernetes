# Kubernetes

**Container orchestration** tool(system). Helps to **manage** containerized applications.

Uses container runtime like docker `containerd` on each node, NOT whole docker.\
See node in https://github.com/VIK2395/Kubernetes/blob/main/components/index.md

- `minikube` is a tool to run a local Kubernetes cluster; widely used for development, testing, and learning Kubernetes.
- `kubectl` is a native Kubernetes command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API.
- `kubens` is a utility to easily switch between Kubernetes namespaces.
- `kubelet` is the primary "node agent" ("process") that runs on each node (on a node, sends instructions to the container runtime (e.g., containerd) to create and manage the containers that make up the pod.
- `containerd` is a containers runtime (manages the lifecycle of containers)
