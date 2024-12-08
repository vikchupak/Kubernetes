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
