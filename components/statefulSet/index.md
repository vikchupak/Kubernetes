# Deployment vs StatefulSet

## Deployment
- Used for stateless applications
- **Replica pods are indentical and interchangeable**
- Each pod gets random hash
- Pods are created/deleted in any order, or simultaneously.
- 1 service that load balances to any pod
- Pods do not get their own DNS names

## StatefulSet
- Used for stateful applications
- **Replica pods are NOT identical and NOT interchangeable**
- Each pod gets unique sticky persistent `Pod Identifier` (as name part) [and thus state] on top of the common bluprint of the pod.
  - The `Pod Identifier` is fixed and ordered, like `mysql-0`, `mysql-1`, `mysql-2`. 
- Pods must be created/deleted in specific order one by one, NO simultaneous create or delete.
  - Create order `mysql-0`, `mysql-1`, `mysql-2`
  - Delete order `mysql-2`, `mysql-1`, `mysql-0`
- Pods must be re-scheduled on the same node to mount its storage again. Usually a remote storage is used to solve this problem.
- Each Pod gets its own DNS name/endpoint like `pod-name.service-name`. So when a Pod restars, the name and endpoint remain the same, IP changes.

## Scaling database applications (stateful)

- main/master database **reads and writes** and replicas/slaves databases **reads only**
- Each Pod uses its own physical storage, so the pods' storages are NOT shared
- Replicas sync data with the master continuously to be up-to-date
- We have to manage yourself the following (because there are no out-of-the-box solutions by kubernetes)
  - Configure cloning and data sync
  - Set remote storage
  - Manage backups
