- Internal service. It also acts as a load-balancer despite the name.
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mongodb-service
  spec:
    selector:
      app: mongodb
    # type: ClusterIP
    ports:
      - protocol: TCP
      port: 27017
      targertPort: 27017
  ```
  - ClusterIP is an implicit default type
  - ClusterIP exposes the service on a cluster-internal IP
- External service
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mongodb-express-service
  spec:
    selector:
      app: mongo-express
    type: LoadBalancer # This makes the service external, NOT the best type name
    ports:
      - protocol: TCP
      port: 8081 # container/pod port
      targertPort: 8081 # internal service port
      nodePort: 30000 # external service port. Must be in range 30000-32767
  ```
