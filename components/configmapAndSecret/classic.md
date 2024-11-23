## ConfigMap example (inject data as environment variables)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap
data:
  environment: production
```

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-env-example
spec:
  containers:
    - name: app
      image: nginx
      env:
        - name: ENVIRONMENT
          valueFrom:
            configMapKeyRef:
              name: my-configmap
              key: environment
```

 ## Secret example (inject data as environment variables)

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
  name: secret-env-example
spec:
  containers:
    - name: app
      image: nginx
      env:
        - name: SECRET_USERNAME
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: username
        - name: SECRET_PASSWORD
          valueFrom:
            secretKeyRef:
              name: my-secret
              key: password
```

 
