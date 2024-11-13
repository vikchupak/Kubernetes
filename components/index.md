- **Cluster**. Is a set of nodes (physical or virtual machines) that work together to run containerized applications in a distributed and scalable manner.
  - The cluster is the highest level of abstraction in Kubernetes and provides the environment for deploying, managing, and scaling applications.

- **Node**. Virtual or physical machine
  - Master Node (Control Plane). While technically a node, it is responsible for orchestrating and managing the worker nodes and the workloads running on them.
  - Worker Node is where Pods (and the containers inside them) actually run. Worker nodes are managed by the control plane (master node) and execute the workloads.

- **Pod**. Smallest unit in Kubernetes. [**Abstration** over/of container].
  - Layer on top of the container.
  - Allows to replace the containers themselves inside pods.
  - You interact with the Kubernetes layer, not directly with container technologies.
  - Usually 1 pod per 1 container. But it allows to run multiple containers inside a pod.
  - Kubernetes creates virtual network out-of-the-box. In Kubernetes, each pod gets its own **internal** IP address, NOT containers. **Direct Pod-to-Pod** communication - all pods in a cluster can communicate with each other using these IPs **regardless of they belong to different services**.
  - Pods are **ephemeral(the term comes from the Greek word ephemeros, which means "lasting only a day.")**, which means they can die very easily. In general, something ephemeral is designed to be temporary, only existing for as long as it's needed, and then discarded.
      - For, example when a container/application crashes, the pod will die and a new pod will be created in its place. **New IP will be assigned on re-creation**

- **Service**. **Wrapper or abstraction layer** over a **group of the same Pods** [Communucation between services/pods]
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

- **Ingress** (Cluster gateway?) [Route traffic to into cluster]
  - Provides HTTP and HTTPS access to services within a cluster, acting as an entry point for external traffic to reach internal Services.
  - Allows you to define rules for routing requests based on hostnames, paths, and ports.
  - Supports SSL/TLS enabling secure HTTPS connections
  - **Ingress Controller**. An application responsible for implementing Ingress rules, usually deployed as a Pod in the cluster. Popular Ingress Controllers include **NGINX**, Traefik, HAProxy, and cloud provider-specific controllers.

- **ConfigMap**. [External configuration] of your application connected to a Pod/Deployment(so pod gets the data). An API object used to store **non-confidential** data in key-value pairs. Pods can consume ConfigMaps as environment variables, command-line arguments, or as configuration files in a volume. https://kubernetes.io/docs/concepts/configuration/configmap/
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

- **Secret**. [External configuration]. Just like ConfigMap, but to store secret data(like user name and password)
  - Data is stored in base64 encoded format.
  - Connected to a Pod/Deployment.

- **Volume** [Data persistance]
  - Kubernetes cluster doesn't manage data persistance
  - Attaches physical storage to a Pod
    - Storage on local machine (Node)
    - Remote storage outside k8s cluster (cloud storage or on-promises storage)

- **Deploment**. [Replication]. A bluprint for pods. A Deployment is a higher-level abstraction that builds on top of **ReplicaSet**.
  - Abstraction on top of pods
  - For **StateLESS** apps
  - Sets number of pod replicas
  - In practice, we don't work with Pods(though we can), but with deployments as it is more convenient way to deal with Pods.
 
- **StatefulSet**. [Replication]. The same as Deployment, but for **StateFUL** apps
  - Abstraction on top of pods
  - For **StateFUL** apps. Apps like DB has a state - its data via volume. The pods of the DB share the same data storage and this requres sync between pods who write/reads data to avoid data inconsistency. https://github.com/VIK2395/JWT_auth/blob/main/jwt/jwt.vs.session.md
  - Sets number of pod replicas
  - Deplying to StatefulSet is **NOT easy**. This is why it common practice to host DB apps outside of the k8s cluster

- **DeamonSet**. [Replication]
  - A term `daemon` means a background process that runs continuously and provides essential services or performs tasks without user intervention.
  - Type of controller that ensures a specific Pod runs on **all NODEs** (or a subset of nodes) in a cluster. DaemonSets are typically used for tasks that need to be performed on every node, such as running log collectors, monitoring agents, or network plugins.

## Replication

In Kubernetes, **nodes do not have replicas** in the same way that Pods or Deployments do. The concept of **replicas** applies primarily to **Pods** or other resources that run workloads, not to nodes themselves.

However, you might be thinking about **replication** in the context of Kubernetes ensuring high availability or fault tolerance for workloads, which can involve **replicating Pods** across different nodes. Let me explain the concepts more clearly.

### 1. **Replicas in Kubernetes:**
- **Replicas** are typically used with **Pods** or **Deployments** to ensure that multiple copies of a Pod (or an application) are running across different nodes for high availability.
  - A **Deployment** or **ReplicaSet** allows you to specify how many replicas (copies) of a Pod should be running at any given time.
  - The Kubernetes **scheduler** then ensures that these replicas are spread across available nodes in the cluster to prevent a single point of failure.

   Example of a Deployment with replicas:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: my-app
   spec:
     replicas: 3  # 3 replicas of the Pod should run
     selector:
       matchLabels:
         app: my-app
     template:
       metadata:
         labels:
           app: my-app
       spec:
         containers:
           - name: my-app-container
             image: my-app-image
   ```

   In this example, Kubernetes will try to maintain 3 replicas of the `my-app` Pod, and it will schedule them across different worker nodes in the cluster, if possible, for high availability.

### 2. **How Nodes Work with Replicas:**
- **Nodes** themselves are not replicated in Kubernetes; rather, they are individual machines (either physical or virtual) in the cluster. However, you can think of **nodes** as the "hosts" on which **Pods** with replicas are scheduled and run.
- **Kubernetes will schedule Pods** (which can be replicas) on nodes based on available resources and other factors like affinity, taints, and tolerations.
- **Pod Distribution**: When you specify a number of replicas in a Deployment or ReplicaSet, the Kubernetes **scheduler** will try to spread the Pods evenly across the nodes in the cluster, assuming there are sufficient resources available. This helps achieve **high availability** by running replicas on multiple nodes, so if one node fails, the Pods on other nodes will continue to run.

### 3. **Node Availability and Scaling:**
While nodes do not have replicas, Kubernetes **scales nodes** to handle workload requirements, but this is done by adding more nodes to the cluster rather than replicating them. If you need more capacity, you add more nodes to the cluster, and Kubernetes will schedule Pods onto those new nodes as needed.

### Key Points:
- **Replicas** refer to the number of copies of a Pod running in a cluster, not nodes.
- Pods with replicas can be scheduled across different nodes, providing fault tolerance and high availability.
- **Nodes** are individual machines and do not have replicas. You can add more nodes to the cluster for scaling.
- **Kubernetes ensures Pods are spread across multiple nodes** for fault tolerance. If one node goes down, the Pods scheduled on that node will be rescheduled onto other available nodes.

### Example: Scaling Pods and Adding Nodes
1. If you have a **Deployment** with 3 replicas and 2 nodes in your cluster, Kubernetes will attempt to place 2 replicas on one node and 1 replica on the other, depending on available resources.
2. If you add a **third node** to the cluster, Kubernetes might decide to move some Pods to the new node to balance the load and maintain high availability.

In summary, **nodes** do not have replicas in Kubernetes, but **Pods** can have replicas, which Kubernetes schedules across the available nodes.
