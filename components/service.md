- Internal service. It also acts as a load-balancer despite the name.
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mongodb-service
  spec:
    selector:
      app: mongodb
    ports:
      - protocol: TCP
      port: 27017
      targertPort: 27017
  ```
- External service
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mongodb-express-service
  spec:
    selector:
      app: mongo-express
    # This makes the service external, NOT the best type name
    type: LoadBalancer
    ports:
      - protocol: TCP
      port: 8081 # container/pod port
      targertPort: 8081 # internal service port
      nodePort: 30000 # external service port. Must be in range 30000-32767
  ```
