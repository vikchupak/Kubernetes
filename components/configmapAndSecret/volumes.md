### Mounting as Volumes
ConfigMap and Secret can be mounted directly as volumes, but without PV(persistent volume) and PVC(persistent volume claim) unlike other volumes

When you mount a ConfigMap or Secret as a volume:
1. You define it in the `volumes` section of a pod's specification.
2. You specify how and where to mount it in the container.

## **ConfigMap Volume Example**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  app.properties: |
    app.name=MyApplication
    app.version=1.0.0
  welcome.message: "Welcome to Kubernetes ConfigMap!"
```

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

`app.properties` file content
```plaintext
app.name=MyApplication
app.version=1.0.0
```

`welcome.message` file content
```plaintext
Welcome to Kubernetes ConfigMap!
```

## **Secret Volume Example**

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: YWRtaW4=   # Base64 encoded "admin"
  password: cGFzc3dvcmQ=   # Base64 encoded "password"
```

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

`username` file content
```plaintext
admin
```

`password` file content
```plaintext
password
```

- Each key-value pair in the Secret becomes a file.
- The file name corresponds to the key, and the file content corresponds to the value.

In both cases, the volume is a way to deliver the ConfigMap or Secret data into the pod's filesystem.
