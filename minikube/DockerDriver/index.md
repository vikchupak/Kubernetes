When you use the Docker driver, Minikube runs its Kubernetes cluster within a **Docker container** instead of a virtual machine (VM). This setup eliminates the need for a hypervisor, making it lightweight and fast to start.

- All Kubernetes components, such as the `API server`, `etcd`, and `kubelet`, run inside this container.
- If you use the Docker driver, Minikube **relies on your host's Docker installation** to manage the cluster.
- Pulled images are stored in the docker runtime cache of the **node**
  - minikube container > node inside the container > container runtime (`containerd`) inside the node > images stored inside the node by `containerd`
  - Images are node-specific and NOT shared between nodes.
  - List images
    - ```bash
      # ssh to minikube node/container as `docker@minikube` user
      minikube ssh
      docker images
      ```
    - ```bash
      # open minikube container as `root@minikube` user
      docker exec -it minikube bash
      docker images
      ```
- minikube uses **host's docker enviroment** like `dockerd`, `containerd` and others via socket files mounted as volumes.
  - https://github.com/VIK2395/Docker/blob/main/DockerInsideConatainer.md
