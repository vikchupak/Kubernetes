## What is Managed Kubernetes services

Managed Kubernetes (K8s) services are cloud-based or on-premises offerings that simplify the deployment, operation, and maintenance of Kubernetes clusters. These services handle the complexities of Kubernetes management, such as scaling, updates, and backups, allowing users to focus on deploying and managing their applications rather than the underlying infrastructure.

- Cloud provider `Storage Class` is an example of managed k8s service that dynamically provides storage by cloud provider.
- Cloud provider Load Balancer as `Ingress Controller` is an example of managed k8s service.

## Cluster on On-promises (typically unmanaged)
- **Spin up**
  - Create a cluster from a scratch. We need to create and manage everithing yourselves
    - Create and manage **master** and worker nodes.
- **Data persistance**
  - We need to create physical storage and make it available for the cluster
    - Create Persistent Volume
    - Attach the volumes to pods
- **Load balancing**
  - We need to install and run Ingress Controller to manage routing of incomming requests
    - Configure your routing rules
    - We can set up Ingress Controller as Load Balancer
      - Nginx inside controller can be used as load bancer

## Cluster on Cloud (typically managed)
- **Spin up**
  - Most setup is automated and done by cloud provider
  - We select number of worker nodes and resouces on them with everything pre-installed
  - Master nodes are managed and created by cloud provider. So we only pay for worker nodes.
  - Select region/data center to decrease network latency
- **Data persistance**
  - We can use `Storage Class` to dynamicly provide storage by cloud provider
- **Load balancing**
  - Cloud providers provide their own Load Balancer implementations
    - **It stands in front of the Ingress controller**. In this scenario Ingress controller works as a "router".
    - The cloud load balancer routes external traffic to the Ingress Controller.
  - We can configure a session stickiness
  - We can set SSL/TLS Certification
- **Migration to another cloud**
  - Not easy at all as cloud providers provide a lof of cloud-specific features, though very similar.
    - Your apps get highly tied to a cloud provider
    - Re-programming and reconfiguring necessary
- **Automation**
  - Cloud providers provide access to their resouces via automation tools (Terraform, Ansible), which allows to automate your infrastructure/devops work
