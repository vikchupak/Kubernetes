## Nodes autoscaling

Kubernetes does not provide **node autoscaling out-of-the-box** by itself.

### Kubernetes and Node Autoscaling

1. **Kubernetes Native Autoscaling Features:**
   - **Pod Autoscaling:** Kubernetes natively supports **Horizontal Pod Autoscaler (HPA)** and **Vertical Pod Autoscaler (VPA)** to scale the number of pods or their resource usage.
   - **Cluster Autoscaler:** This is a separate component provided by Kubernetes but **not part of the default installation**. It works to scale the number of nodes by interacting with your infrastructure layer.

2. **Dependency on Infrastructure:**
   - Kubernetes relies on the underlying infrastructure to add or remove nodes, etc:
     - Cloud APIs for virtual machines
     - Custom scripts for on-premises environments
   - Without a supporting mechanism to provision new nodes, Kubernetes alone cannot manage node autoscaling.

### Cloud vs On-promises cluster

- **Cloud. Out-of-the-Box Node Autoscaling in Cloud-Managed Kubernetes**
  - If you're using a **cloud provider-managed Kubernetes service**, node autoscaling often **feels** "out-of-the-box" because:
    - Services like GKE, EKS, or AKS automatically integrate with Kubernetes' Cluster Autoscaler and cloud APIs for provisioning nodes.

- **On-Premises or Custom Environments**
  - For non-cloud environments:
    - In on-premises Kubernetes cluster, the number of nodes is usually fixed unless you manually add or remove them.
    - Node autoscaling is **NOT out-of-the-box** but can be implemented using **Kubernetes' Cluster Autoscaler** and **infrastructure automation tools like Terraform, Ansible, or custom scripts**.

### Summary
Node autoscaling in Kubernetes is **NOT inherently built into the core system** but can be achieved with:
- **Cluster Autoscaler** (for logic).
- **Cloud or infrastructure integration** (for provisioning).
