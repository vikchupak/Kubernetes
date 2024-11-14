## Files structure

Each config file has 3 parts:
- `metadata` of a component being created
- `spec` (specification) - congigs to apply to that component
  - Deployment includes `template` section - it is a nested config file - blueprint for pods within the deployment - applies to pods
    - has own `metadata`
    - has own `spec`
    
  ![image](https://github.com/user-attachments/assets/bf3fafc8-3b6d-40ac-bb30-94921179d66f)
- `status` (automatically generated and added by Kubernetes). Kubernetes continuously compares `specification - desired state/status` vs `status - actual state/status` and syncs the states/statuses.
  - status data gets from `etcd`

## Labels and Selectors

Labels(key-value pairs) and selectors(key-value pairs) are used to establish connection between components.

- metadata part declares labels associated with a component
- spec part declares selectors

***Note: pods get labels through `template` section.***

### Connecting Pods to Deployment

- via `selectors in a deployment` and `matched labels in the deployment template section`, the pods know which deployment they belong to.

<img width="439" alt="29" src="https://github.com/user-attachments/assets/286a8d6d-cd1f-47d0-a809-fd81058dc9d0">

### Connecting Deployment and Pods to Services

<img width="727" alt="30" src="https://github.com/user-attachments/assets/ec6dfab1-2008-4294-95ae-fcc1868cc30d">

### Map Pod ports to Service ports

<img width="812" alt="31" src="https://github.com/user-attachments/assets/69869816-c2a9-4b93-9e4a-75d5b8977254">

*****

We can put multiple documents to the same yaml file.

Usually, deployment and service are put into the same file because they usually belong together.

We can execute the file, and `kubectl` will create/update all the components described in the file.

***Note: to address to another service we can use service name alias like `http://<service-name>:[<internal-service-port>]`***

