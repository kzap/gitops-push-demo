# Echo Service

This directory contains both Helm and Kustomize templates for deploying the `hashicorp/http-echo:latest` container.

## Helm Chart

The Helm chart is located in the `charts/echo-service` directory.

### Directory Structure
```
charts/echo-service/
├── Chart.yaml
├── values.yaml
├── values/
│   ├── dev/
│   │   └── values.yaml
│   ├── stage/
│   │   └── values.yaml
│   └── production/
│       └── values.yaml
└── templates/
    ├── deployment.yaml
    └── service.yaml
```

### Usage

Install the chart with default values:
```bash
helm install echo-service ./charts/echo-service
```

Install for specific environments:
```bash
# Dev environment
helm install echo-service ./charts/echo-service -f ./charts/echo-service/values/dev/values.yaml

# Stage environment
helm install echo-service ./charts/echo-service -f ./charts/echo-service/values/stage/values.yaml

# Production environment
helm install echo-service ./charts/echo-service -f ./charts/echo-service/values/production/values.yaml
```

### Environment Configurations

- **Dev**: 1 replica, 100m CPU limit, 128Mi memory limit
- **Stage**: 2 replicas, 200m CPU limit, 256Mi memory limit
- **Production**: 3 replicas, 500m CPU limit, 512Mi memory limit

## Kustomize

The Kustomize templates are located in the `kustomize` directory.

### Directory Structure
```
kustomize/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── kustomization.yaml
└── overlays/
    ├── dev/
    │   └── kustomization.yaml
    ├── stage/
    │   └── kustomization.yaml
    └── prod/
        └── kustomization.yaml
```

### Usage

Build and apply for specific environments:
```bash
# Dev environment
kubectl apply -k ./kustomize/overlays/dev

# Stage environment
kubectl apply -k ./kustomize/overlays/stage

# Production environment
kubectl apply -k ./kustomize/overlays/prod
```

Preview the generated manifests:
```bash
# Dev environment
kubectl kustomize ./kustomize/overlays/dev

# Stage environment
kubectl kustomize ./kustomize/overlays/stage

# Production environment
kubectl kustomize ./kustomize/overlays/prod
```

### Environment Configurations

- **Dev**: 1 replica, `dev` namespace, `dev-` name prefix
- **Stage**: 2 replicas, `stage` namespace, `stage-` name prefix
- **Production**: 3 replicas, `production` namespace, `prod-` name prefix

## Docker Image

Both templates deploy the `hashicorp/http-echo:latest` image, which is a simple HTTP echo server that responds with a configurable text message.

The service listens on port 5678 and responds to HTTP requests with the configured message.

