## Control loop

A **control loop** is a foundational design pattern responsible for maintaining the desired state of cluster objects. It's implemented by controllers, which continuously monitor the state of the system and take corrective actions to ensure the cluster's actual state matches the desired state.

k8s has own native *control loop* to manage stateless apps.

## Operators

Operators are mainly used for stateful apps. An operator is an extension.

- k8s can'not automate the process natively for stateful apps.
- Stateful apps like MySQL or Postgress or ElasticSearch don't have a standard solution to automate the whole process of managing complete lifecycle of the apps (like replica sync, for each database there is different process of sync. Etc). So, they require manual intervention(people to "operate" these apps).
- Operator replaces human operator with software operator (program to automate/replace "manual intervention").
- You can think of operator as a custom control loop.
- Uses custom resource definition (CRD), can include/build on top of standard k8s resources/components.
- Operator combines custom control loop with CRD taking into account domain/app-specific knowledge to operate the stateful app.

Example of operators:
- prometheus-operator
- elastic-operator
- mysql-operator
- postgres-operator

### Who creates operators

- Operators are created by those who are business/domain exports in installing, running, and updating those specific apps.
- Operators repository https://operatorhub.io/
- SDK to create an own operator https://sdk.operatorframework.io/
