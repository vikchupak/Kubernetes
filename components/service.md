## Service types

- ClusterIP
  - Default. Acts as a load-balancer.

- NodePort
  - External via node-ip.
  - `spec.ports.nodePort` to make service accessible externally.
  - `http://<any-node-ip>:<node-port>`; See LoadBalancer. Exactly the same setting. 
  - **A service spans accross all nodes**
  - When NodePort service created, it also creates ClusterIP services and routes trafic from the NodePort to the ClusterIP services. ("Wraps" ClusterIP)
  - A NodePort provides a simple way to access a service **during development or debugging** without needing a more complex setup like Ingress or LoadBalancer.

- LoadBalancer
  - External.
  - Exposes a service externally by provisioning a cloud provider's load balancer (e.g., AWS ELB, GCP LB, or Azure LB).
    - LoadBalancer services in Kubernetes are primarily designed for use with cloud providers.
  - `http://<external-load-balancer-ip(unique)>:<node-port>` in produnction; after tunnel maps on `http://127.0.0.1:<tunnel-port>`
    - command for tunnel `minikube tunnel`; then `kubectl get services <service-name>` > EXTERNAL-IP
    - or `minikube service <service-name>` > see logs or `kubectl get services <service-name>` > EXTERNAL-IP
  - Each service with type LoadBalancer gets its own external load balancer.
  - The external load balancer forwards traffic to the NodePort on the nodes.
  - When LoadBalancer service created, it also creates NodePort services and routes trafic from the LoadBalancer to the NodePort services.  ("Wraps" NodePort)

- Headless
  - Doesn't have own static/stable service IP. Returns the service's pod IPs instead.
  - `spec.clusterIP: None` to make a service Headless. `It is still ClusterIP type`.

## Config files

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
      port: 8081 # internal service port
      targertPort: 8081 # container/pod port 
      nodePort: 30000 # external service port. Must be in range 30000-32767
  ```
  - LoadBalancer assigns in addition an external IP
  - In **Minikube**, the external IP is NOT assigned immediately. To "force" Minikube expose the external IP, we need to run the command additionally `minikube service <service-name>`. ***It will create a tunnel for our service to be accessible from the localhost.***
