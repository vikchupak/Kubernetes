Container images repository
- https://hub.docker.com/

When using minikube with the docker driver, we need to login to private docker repository from the docker inside k8s container, NOT host's docker.

```bash
minikube ssh
```
```bash
docker login <registry-hostname>
# stores creds to ~/.docker/config.json
```

Create secret resource. **The secred is namespaced**.
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-docker-registry-secret
  namespace: default  # Change this if needed
type: kubernetes.io/dockerconfigjson
data:
  .dockerconfigjson: eyJhdXRocyI6eyJodHRwczovL2luZGV4LmRvY2tlci5pby92MS8iOnsidXNlcm5hbWUiOiJteXVzZXJuYW1lIiwicGFzc3dvcmQiOiJteXBhc3N3b3JkIiwiZW1haWwiOiJteWlkQGVtYWlsLmNvbSIsImF1dGgiOiJleUpoYkdjaU9pSkZVekkxTmlKOSJ9fX0=
# wehre .dockerconfigjson is 64-encoded content of ~/.docker/config.json file
```

Ref secret in a pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  containers:
  - name: my-app
    image: my-private-image:tag
  imagePullSecrets:
  - name: my-docker-registry-secret
```
