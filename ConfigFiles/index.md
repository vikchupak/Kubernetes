## Files structure

Each config file has 3 parts:
- Metadata of a component being created
- Specification - congigs to apply to that component
  - Includes `template` section - it is a nested config file - blueprint for pods - applies to pods
- Status(automatically generated and added by Kubernetes). Kubernetes continuously compares `specification - desired state/status` vs `status - actual state/status` and syncs the states/statuses.
  - Status data gets from `etcd`

## Labels && selectors

Labels and selectors are used to establish connection between components.

- metadata part contains labels used for a component
