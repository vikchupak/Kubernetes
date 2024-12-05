***Note: k8s knows a pod's state, but it does't know application/container's state inside the pod***

## Best practices

- Pinned image version tag
- `Liveness probe` on each container
  - Happens while app is running
  - k8s doesn't natively handle containers/apps health, unlike other k8s resources: pods, services, etc.
  - Pod can be alive, but app inside container can crash. With the probe, container is re-created when crashed.
  - Script or program that pings the app endpoint every 5 or 10 seconds
  ```yaml
  spec:
      containers:
      - name: service
        image: gcr.io/google-samples/microservices-demo/productcatalogservice:v0.8.0
        ports:
        - containerPort: 3550
        env:
        - name: PORT
          value: "3550"
        - name: DISABLE_PROFILER
          value: "1"
        livenessProbe:
          grpc:
            port: 3550
          periodSeconds: 5
        # or
        livenessProbe:
          initialDelaySeconds: 5
          tcpSocket:
            port: 6379
          periodSeconds: 5
        # or
        livenessProbe:
          httpGet:
            path: "/_healthz"
            port: 8080
          periodSeconds: 5
  ```
- `Readiness probe` on each container
  - Happens during app startup
  - Check that an app is ready to receive traffic
  - Liveness probe stars **only after** successful Readiness probe
  ```yaml
  spec:
      containers:
      - name: service
        image: gcr.io/google-samples/microservices-demo/productcatalogservice:v0.8.0
        ports:
        - containerPort: 3550
        env:
        - name: PORT
          value: "3550"
        - name: DISABLE_PROFILER
          value: "1"
        readinessProbe:
          grpc:
            port: 3550
          periodSeconds: 5
        # or
        readinessProbe:
          initialDelaySeconds: 5
          tcpSocket:
            port: 6379
          periodSeconds: 5
        # or
        readinessProbe:
          httpGet:
            path: "/_healthz"
            port: 8080
          periodSeconds: 5
  ```
- Configure `resources requests` for each container: CPU and RAM
  - k8s guarantees each container gets requested resources
  ```yaml
  spec:
      containers:
      - name: service
        image: gcr.io/google-samples/microservices-demo/emailservice:v0.8.0
        ports:
        - containerPort: 8080
        env:
        - name: PORT
          value: "8080"
        resources:
          requests: 
            cpu: 100m # milicores
            memory: 64Mi # Mebibytes
  ```
- Configure `resources limits` for each container: CPU and RAM
  - In some cases, a container can start consuming more than the requested resources, and consume all the node's resources
  - Set allowed consumed resource limits
  - When CPU Limit reached
    - Kubernetes uses CFS (Completely Fair Scheduler) **throttling** at the kernel level to control CPU usage. This allows limiting CPU **without terminating the container**.
  - When RAM Limit reached
    - The container is terminated (killed) by the Kubernetes runtime (e.g., OOMKilled—Out of Memory Killed). k8s tries to **restart the container** depending on the pod's restart policy.
  ```yaml
      spec:
      containers:
      - name: service
        image: gcr.io/google-samples/microservices-demo/emailservice:v0.8.0
        ports:
        - containerPort: 8080
        env:
        - name: PORT
          value: "8080"
        resources:
          requests: 
            cpu: 100m
            memory: 64Mi
          limits:
            cpu: 200m
            memory: 128Mi
  ```
- Do not expose `NodePort`
  - With NodePort we provide multiple entry points, which is bad from security perspective
  - Use single entry point with `LoadBalancer` or `IngressController` instead
- Use/Set more than 1 Replica for Deployment
  - Provides availability even when one pod is crashes
- Use more than 1 Worker Node
  - Each replica should run on different node
- Use Labels on all k8s resources
- Use namespaces to organize resources and set permissions
- Security
  - Ensure images free of vulnerabilities
     - Perform manual or pipeline buit scans
  - No containers with root privileges which get access to host-level resources
  - Update your k8s to the latest version
