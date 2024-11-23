## ConfigMap and Secret

Can be accessed in ways
- Environment Variables: Injected into a container's environment.
- **Volumes** mounted as files inside a container. (The same way as Persistent Volume).
- Command-line Arguments: Used as arguments when starting containers **(ConfigMap)**.
- Direct Reference: Used by other Kubernetes resources, such as image pull secrets **(Secret)**.

- **"Classic" `ConfigMap` and `Secret` provide individual key-value pairs, BUT it is NOT config files that apps can read.**
- **We can create files to mount into a pod and then from the pod to the containers.**

In Kubernetes, **ConfigMaps** and **Secrets** are not volumes themselves, but they can be mounted into a pod as **volumes**.

### Mounting as Volumes
When you mount a ConfigMap or Secret as a volume:
1. You define it in the `volumes` section of a pod's specification.
2. You specify how and where to mount it in the container.

#### **ConfigMap Volume Example**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-volume-example
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: my-configmap
```

#### **Secret Volume Example**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-volume-example
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - name: secret-volume
      mountPath: /etc/secrets
  volumes:
  - name: secret-volume
    secret:
      secretName: my-secret
```

In both cases, the volume is a way to deliver the ConfigMap or Secret data into the pod's filesystem.
