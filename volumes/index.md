Kubernetes doesn't provide data persistance out-of-the-box/

- 1\. **We need a storrage that doesn't depend on the pod lifecycle**
- 2\. **Storage must be available across all nodes**
- 3\. **Storage must survive cluster crash**

## Local vs Remote Volumes

Local violates 2 and 3 requirements

**Local storage** in Kubernetes refers to storage that is **tied to a specific node** in the cluster, rather than being abstracted or distributed across multiple nodes (as with persistent volumes from cloud providers or networked storage solutions). It is typically used for scenarios where data locality is important or performance is critical, such as caching or temporary storage for a single pod.

### **Key Features of Local Storage**
1. **Node Affinity**: Pods using local storage must run on the node where the local storage is physically located. Kubernetes ensures this by scheduling the pod on the appropriate node.
2. **Performance**: Since it uses local disks, it can provide faster read/write speeds and lower latency compared to remote storage.
3. **Non-Portable**: The data cannot be easily moved between nodes. If the node fails, the data may become unavailable unless a backup mechanism is in place.

---

### **Types of Local Storage in Kubernetes**

1. **EmptyDir**:
   - **Tied to the pod** on a specific node and exists on the **node's local storage(node's local filesystem)** (e.g., /var/lib/kubelet).
     - The **node's local filesystem** refers to the physical or virtual disk storage **on the host machine where the Kubernetes node is running**.
     - An `EmptyDir` volume creates temporary storage for a pod on the **node's local filesystem**, usually under `/var/lib/kubelet/pods/<pod-id>/volumes/`
     - Even though the data resides on the **host machine**, Kubernetes abstracts it away, so pods interact with it as if it were their own **local storage**.
     - It's managed by Kubernetes.
     - The data exists on the node, but Kubernetes manages it and isolates it for pod use.
   - A temporary storage volume that is created for a pod.
   - It exists only as long as the pod is running.
   - `EmptyDir` provides a shared and persistent directory for **all containers within a pod**.
   - The directory is created on the **node's local disk** (or memory if configured with medium: Memory).
   - Even if one container in the pod restarts, the data in the EmptyDir volume remains intact and accessible by other containers in the same pod.
   - **Persistence Within the Pod**: Even if a container restarts, the EmptyDir data persists as long as the pod exists.
   - **Shared Storage for Containers**: All containers within the same pod can read/write data to the EmptyDir volume, enabling communication and data sharing.
   - The volume is dedicated to the pod that created it, meaning **other pods cannot access it**, even if they are running on the same node.

   Example:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: emptydir-example
   spec:
     containers:
     - name: app
       image: nginx
       volumeMounts:
       - name: data-volume
         mountPath: /data
     volumes:
     - name: data-volume
       emptyDir: {}
   ```

3. **HostPath**:
   - Tied to a specific directory or file on the **host's filesystem**.
   - A volume that mounts a file or directory from **the host node's** filesystem into the pod.
   - Useful for debugging or accessing host resources but requires careful handling for security.

   Example:
   ```yaml
   apiVersion: v1
   kind: Pod
   metadata:
     name: hostpath-example
   spec:
     containers:
     - name: app
       image: nginx
       volumeMounts:
       - name: host-volume
         mountPath: /data
     volumes:
     - name: host-volume
       hostPath:
         path: /data-on-host
         type: DirectoryOrCreate
   ```

5. **Local Persistent Volumes**:
   - A more advanced and persistent form of local storage.
   - Unlike `emptyDir` or `hostPath`, it integrates with Kubernetes' Persistent Volume (PV) and Persistent Volume Claim (PVC) system.
   - It provides higher durability and is suitable for stateful workloads like databases.

   Example:
   ```yaml
   apiVersion: v1
   kind: PersistentVolume
   metadata:
     name: local-pv
   spec:
     capacity:
       storage: 10Gi
     accessModes:
     - ReadWriteOnce
     persistentVolumeReclaimPolicy: Retain
     storageClassName: local-storage
     local:
       path: /mnt/disks/ssd1
     nodeAffinity:
       required:
         nodeSelectorTerms:
         - matchExpressions:
           - key: kubernetes.io/hostname
             operator: In
             values:
             - node1
   ```

   ```yaml
   apiVersion: v1
   kind: PersistentVolumeClaim
   metadata:
     name: local-pvc
   spec:
     accessModes:
     - ReadWriteOnce
     resources:
       requests:
         storage: 10Gi
     storageClassName: local-storage
   ```

---

### **Use Cases for Local Storage**
1. **High-Performance Workloads**: Applications needing low latency and high IOPS.
2. **Ephemeral Data**: Data that does not need to persist beyond the lifecycle of a pod.
3. **Caching or Temporary Data**: Data that can be reconstructed or regenerated.

---

### **Limitations of Local Storage**
1. **Non-Resilient**: Local storage is tied to a node, so if the node fails, the data may be lost.
2. **Scheduling Constraints**: Kubernetes must schedule the pod on the same node as the storage.
3. **Not Suitable for Multi-Node Access**: Unlike network-attached storage, local storage cannot be shared across multiple nodes.

Local storage is a powerful option in Kubernetes when used with the appropriate workloads and data durability considerations.


## Components
- Persistent Volume
  - **cluster** resource
  - Can be created via yaml file
  - Abstraction that needs real physical storage, like
    - local hard drive/disk
    - nfs server
    - cloud storage
  - **Interface to the actual storage**
  - The storage is like external plugin to the cluster(storage backend)
  - NOT namespaced, accessible to the whole cluster
- Persistent Volume Claim
- Storage Class
