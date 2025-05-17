# Extra Objects Helm Chart

A utility Helm chart that allows you to define and deploy arbitrary Kubernetes resources.

## Introduction

The `extra-objects` chart provides a simple way to deploy any Kubernetes resource type without having to create a dedicated chart for each resource type. This is particularly useful for:

- Deploying resources that don't have a dedicated chart
- Adding auxiliary resources to your Kubernetes cluster
- Creating resources that are not covered by your existing charts
- Testing new resource configurations before integrating them into your main charts

## Installation

```bash
# Install chart with release name "my-extra-objects"
helm install my-extra-objects ./charts/extra-objects -f my-values.yaml
```

## Configuration

The chart is configured through the `values.yaml` file. There are two main sections:

### extraObjectArgs

Common variables that can be referenced across multiple resources. This makes it easy to maintain consistency across objects and simplifies updates.

```yaml
extraObjectArgs:
  appName: my-application
  namespace: my-namespace
  # Add any other common variables here
```

### extraObjects

A map of Kubernetes resources to be created. Each key in the map represents a unique identifier for the resource (used for reference only), and the value is the complete resource definition.

```yaml
extraObjects:
  # Resource identifier (for reference only)
  myDeployment:
    # Complete Kubernetes resource definition
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: "{{ .Values.extraObjectArgs.appName }}"
      namespace: "{{ .Values.extraObjectArgs.appName }}"
    spec:
      # ... rest of the deployment spec
```

## Examples

### Basic Example

Creating a Deployment and Service:

```yaml
extraObjectArgs:
  appName: example-app

extraObjects:
  deployment:
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: "{{ .Values.extraObjectArgs.appName }}"
      namespace: "{{ .Values.extraObjectArgs.appName }}"
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: "{{ .Values.extraObjectArgs.appName }}"
      template:
        metadata:
          labels:
            app: "{{ .Values.extraObjectArgs.appName }}"
        spec:
          containers:
            - name: "{{ .Values.extraObjectArgs.appName }}"
              image: "{{ .Values.extraObjectArgs.appName }}:latest"
              ports:
                - containerPort: 80
  
  service:
    apiVersion: v1
    kind: Service
    metadata:
      name: "{{ .Values.extraObjectArgs.appName }}"
      namespace: "{{ .Values.extraObjectArgs.appName }}"
    spec:
      selector:
        app: "{{ .Values.extraObjectArgs.appName }}"
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
```

## Notes

- All resources are rendered using the Helm `tpl` function, allowing you to use template functions in your resource definitions
- You can use any Kubernetes resource type supported by your cluster
- The `extraObjectArgs` section is optional but recommended for maintaining consistency across resources
- Each resource in `extraObjects` must be a valid Kubernetes resource definition
