- `Helm` is a package manager for K8s. Packages config/manifest yaml files and distributes them to public/private repositories.
- `Helm chart` is a bundle of manifest yaml files. We can create and push them to Helm repositories(a collection of Helm charts).
  - https://artifacthub.io/
- `Helm` includes a `templating engine` to process template files

```bash
sudo snap install helm --classic
```


Helm is a powerful **package manager** for Kubernetes, used to streamline the deployment and management of applications within a Kubernetes cluster. It simplifies the process of defining, installing, and upgrading complex Kubernetes applications.

---

### **Key Features of Helm**
1. **Package Management:**
   - Helm packages Kubernetes configurations into **charts**.
   - Charts define a set of Kubernetes resources, such as deployments, services, and config maps.

2. **Template Engine:**
   - Helm templates YAML files, allowing you to use variables and conditionals in your configurations.

3. **Version Control:**
   - Helm allows rollbacks to previous versions of your application.

4. **Dependency Management:**
   - Charts can define dependencies on other charts, automatically resolving them.

5. **Community Ecosystem:**
   - Access to thousands of pre-built charts for popular software via repositories like [Artifact Hub](https://artifacthub.io).

---

### **Core Concepts**
1. **Charts:**
   - A Helm chart is a package that contains the following:
     - Templates for Kubernetes resources.
     - A `values.yaml` file for configuration.
     - Metadata (`Chart.yaml`).
     - Optional dependencies.

2. **Releases:**
   - A **release** is a deployed instance of a Helm chart. You can have multiple releases of the same chart with different configurations.

3. **Repositories:**
   - Helm uses repositories to store and retrieve charts. Example: `https://charts.bitnami.com/bitnami`.
