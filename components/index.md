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

- **ConfigMap**. External configuration of your application connected to a Pod/Deployment(so pod gets the data). An API object used to store **non-confidential** data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume. https://kubernetes.io/docs/concepts/configuration/configmap/
  - Some env vars can be **hardcoded to container IMANGE**. Example below.
    ```Dockerfile
    # Start with a base image
    FROM python:3.8
    
    # Set environment variable for the database URL (baked into the image)
    ENV DATABASE_URL="mysql://db.example.com:3306/mydb"
    
    # Copy the application code into the container
    COPY app.py /app/app.py
    
    # Set the working directory
    WORKDIR /app
    
    # Run the application(the app uses DATABASE_URL to connect to database)
    CMD ["python", "app.py"]
    ```
    To make `DATABASE_URL` configurable, we can remove it from image and set the env var when starting a pod/container(simalar to docker compose).
    **This allows to avoid unnecessary image rebuilds**.

- **Secret**. Just like ConfigMap, but to store secret data(like user name and password)
  - Data is stored in base64 encoded format.
  - Connected to a Pod/Deployment.
