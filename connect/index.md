# Connect to a cluster 

### kubeconfig file

A `kubeconfig` file contains cluster information and authentication credentials to establish a secure connection to the API server. This file is usually located at `~/.kube/config` and contains:

- Cluster details (e.g., API server URL, CA certificates)
- User credentials or tokens

Example of a `kubeconfig`
  ```yaml
  apiVersion: v1
  clusters:
  - cluster:
      server: https://<api-server-ip>:6443
      certificate-authority: /path/to/ca.crt
    name: my-cluster
  contexts:
  - context:
      cluster: my-cluster
      user: admin
    name: my-context
  current-context: my-context
  users:
  - name: admin
    user:
      token: <authentication-token>
  ```

# Set kubeconfig file location for a session

```bash
# Connect to remote cluster with kubectl
# By default, kubectl looks for the Kubeconfig file at ~/.kube/config
export KUBECONFIG=/path/to/your/kubeconfig
```

# Switch between contexts inside kubeconfig file

List contexts
```bash
kubectl config get-contexts
```

Show current ctx
```bash
kubectl config current-context
```

Switch context
```bash
kubectl config use-context <context-name>
```
