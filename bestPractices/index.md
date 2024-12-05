***Note: k8s knows a pod's state, but it does't know application/container's state inside the pod***

## Best practices

- Pinned image version tag
- Liveness probe on each container
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
  ```
- Readiness probe on each container
  - 
