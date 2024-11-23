## ConfigMap and Secret

Can be used as
- Environment Variables: Injected into a container's environment.
- **Volumes** mounted as files inside a container. (The same way as Persistent Volume).
- Command-line Arguments: Used as arguments when starting containers **(ConfigMap)**.
- Direct Reference: Used by other Kubernetes resources, such as image pull secrets **(Secret)**.

Classic vs volumes
- **"Classic" `ConfigMap` and `Secret` provide individual key-value pairs, BUT it is NOT config files that apps can read.**
- **We can create files to mount into a pod and then from the pod to the containers via volumes.**

In Kubernetes, **ConfigMaps** and **Secrets** are not volumes themselves, but they can be mounted into a pod as **volumes**.

- **Volumes**: https://github.com/VIK2395/Kubernetes/blob/main/components/configmapAndSecret/volumes.md
