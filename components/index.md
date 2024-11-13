- **Node**. Virtual or physical machine

- **Pod**. Smallest unit in Kubernetes. **Abstration** over container.
  - Layer on top of the container.
  - Allows to replace the containers themselves inside pods.
  - You interact with the Kubernetes layer, not directly with container technologies.
  - Usually 1 pod per 1 container. But it allows to run multiple containers inside a pod.
  - Kubernetes creates virtual network out-of-the-box. In Kubernetes, each pod gets its own **internal** IP address, NOT containers. **Direct Pod-to-Pod** communication - all pods in a cluster can communicate with each other using these IPs **regardless of they belong to different services**.
  - Pods are **ephemeral(the term comes from the Greek word ephemeros, which means "lasting only a day.")**, which means they can die very easily. In general, something ephemeral is designed to be temporary, only existing for as long as it's needed, and then discarded.
      - For, example when a container/application crashes, the pod will die and a new pod will be created in its place. **New IP will be assigned on re-creation**

- **Service**. **Wrapper or abstraction layer** over a **group of the same Pods**
  - A service has **static/permanent IP and DNS**
  - Lifecycle of Pod and Service are **NOT connected**. So pods can be re-created, but the service still persists.
  - **Load Balancing**. The Service balances traffic across the group of Pods it represents, ensuring that requests are distributed evenly.
  - **Service/Label Selector**. Services often use labels and selectors to match a set of Pods (e.g., all Pods with the label `app=web`). By using label selectors, the Service can dynamically associate with any Pods that match the defined labels. If a Pod fails and a new one replaces it with the same labels, the Service automatically includes it.
  - Possible communications:
    - direct service-to-service
    - direct pod-to-pod
    - pod-to-pod through service
  - External and Internal services.
    - External can be accessed from external sources (http://<**node-ip**>:<**node-port-that-maps-on-service-port**>)
    - Internal is NOT accessible/visible for external sources

- **Ingress**
  - Provides HTTP and HTTPS access to services within a cluster, acting as an entry point for external traffic to reach internal Services.
  - Allows you to define rules for routing requests based on hostnames, paths, and ports.
  - Supports SSL/TLS enabling secure HTTPS connections
  - **Ingress Controller**. An application responsible for implementing Ingress rules, usually deployed as a Pod in the cluster. Popular Ingress Controllers include **NGINX**, Traefik, HAProxy, and cloud provider-specific controllers.
