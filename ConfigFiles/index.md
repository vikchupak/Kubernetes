**Note: usually, deployment and service are put into the same file because they usually belong together.**

## Files structure

Each config file has 3 parts:
- `metadata` of a component being created
- `spec` (specification) - configs to apply to that component
  - Deployment includes `template` section - it is a nested config file - blueprint for pods within the deployment - applies to pods
    - has own `metadata`
    - has own `spec`
- `status` (automatically generated and added by Kubernetes). Kubernetes continuously compares `desired spec status` vs `actual status` and syncs the statuses.
  - status data gets from `etcd`

Deployment manifest
![image](https://github.com/user-attachments/assets/aff57e49-dea9-4571-9090-bdae957879e6)

## Labels and Selectors

Labels(key-value pairs) and selectors(key-value pairs) are used to establish connection between resources.

- `labels` to identify and categorize resources
- `selectors` used by resources to target other label-matching resources
- `metadata` part declares labels associated with a component
  - `metadata.name` and `metadata.labels` are NOT the same. `name` is like resource unique ID. `labels` are like tags used for organizing, categorizing, and selecting resources and are NOT unique.
- `spec` part declares `selectors`

***Note: pods get labels through `template` section.***

### Relate Deployment -> Pods

- via `selector in a deployment` and `matchedLabels in the deployment template section`, the pods know which deployment they belong to.
- The Deployment creates Pods with the label app=my-app and then manages all Pods with that label(their lifecycle like creation, scaling, updates, etc.).

<img width="439" alt="29" src="https://github.com/user-attachments/assets/286a8d6d-cd1f-47d0-a809-fd81058dc9d0">

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  replicas: 3
  selector:                 # Selector specifies which Pods the Deployment manages
    matchLabels:
      app: my-app
  template:                 # Template specifies the Pod's configuration
    metadata:
      labels:
        app: my-app         # Labels added to the Pods created by this Deployment
    spec:
      containers:
      - name: nginx
        image: nginx
```

### Relate Services -> Pods

- A Service selects a group of Pods to which it routes traffic.
- The `spec.selector` in a Service defines which Pods it targets by their labels.

<img width="727" alt="30" src="https://github.com/user-attachments/assets/6b196795-675b-4fd6-b4ef-41af06db1077">

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:                # Selector specifies which Pods this Service targets
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 8080
```

#### Summary

- Deployment creates and manages Pods, ensuring they have specific labels (e.g., app=my-app).
- Service selects the Pods created by the Deployment using its selector (e.g., app=my-app).
- Traffic sent to the Service is automatically routed to the selected Pods.

### Map Pod ports to Service ports

<img width="812" alt="31" src="https://github.com/user-attachments/assets/69869816-c2a9-4b93-9e4a-75d5b8977254">

- ***All container ports are open on pod ports 1:1***
- Spesifying containerPort in pod definition is optional, but good practice for doc/overview purposes

*****

We can put multiple documents to the same yaml file.

**Usually, deployment and service are put into the same file because they usually belong together.**

We can execute the file, and `kubectl` will create/update all the components described in the file.

***Note: to address to another service we can use service name alias like `http://<service-name>:[<internal-service-port>]`***

