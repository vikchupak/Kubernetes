- https://gitlab.com/twn-devops-bootcamp/latest/10-kubernetes/helm-chart-microservices/-/tree/main?ref_type=heads

```bash
helm create <chart-name>
```

The `helm create` command is used in Helm to scaffold a new Helm chart. A Helm chart is a package that contains Kubernetes resource definitions and metadata files used to deploy applications to a Kubernetes cluster.

```
chart-name/
├── charts/          # Directory to store dependency charts
├── templates/       # Kubernetes resource templates (e.g., Deployment, Service)
│   ├── _helpers.tpl # Helper template for reusable snippets
│   └── *.yaml       # Templates of k8s manifest/config files, `{{}}` - value placeholders in the files
├── Chart.yaml       # Chart metadata (name, version, description, etc.)
├── values.yaml      # Default values for the chart/templates
└── .helmignore      # Files and directories to ignore in the chart package
```

All files in templates folder are passed to template engine. The engine fills the placeholders with values. Then these files are sent to k8s to create/deploy the file resources (with `helm install` command).

## Values files can use flat or nested values definition

```yaml
appName: my-app
imageRepository: my-app-repo
imageTag: "1.0.0"
replicas: 2
serviceType: ClusterIP
servicePort: 80
ingressEnabled: true
ingressHost: my-app.example.com
ingressPath: /
```

```yaml
app:
  name: my-app
  image:
    repository: my-app-repo
    tag: "1.0.0"
  replicas: 2

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: true
  host: my-app.example.com
  path: /
```

## Values priority application

The next overwrites the privious
- Values used from default `values.yaml` file
- Values used from `-f custom-values-file.yaml` file
- Values used from `--set key1=value1` option

## Commands

Preview templetified manifests
```bash
# when in the same folder as `chart-name`
helm template -f values-file.yaml chart-name --set key1=value1,key2=value2
```

Validate template files
```bash
helm lint -f values-file.yaml chart-name
```

Validate template files 2
```bash
# Sends files to k8s, but withot deploying
helm install --dry-run -f values-file.yaml helm-release-name chart-name
```

Create/deploy resources in k8s via helm
```bash
helm install -f values-file.yaml helm-release-name chart-name
```

List helm releases
```bash
helm ls
```
