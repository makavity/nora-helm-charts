# NORA Helm Chart

A Helm chart for deploying [NORA](https://github.com/getnora-io/nora) — a container registry proxy with caching and garbage collection.

## Installation

```bash
helm repo add nora https://getnora-io.github.io/helm-charts
helm repo update
helm install nora nora/nora
```

Or from source:

```bash
git clone https://github.com/getnora-io/helm-charts
helm install nora ./helm-charts/charts/nora/
```

## Configuration

### Image

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Container image | `ghcr.io/getnora-io/nora` |
| `image.tag` | Image tag (defaults to `appVersion`) | `""` |
| `image.pullPolicy` | Pull policy | `IfNotPresent` |

### Service

| Parameter | Description | Default |
|-----------|-------------|---------|
| `service.type` | Service type | `ClusterIP` |
| `service.port` | Service port | `4000` |

### Ingress

| Parameter | Description | Default |
|-----------|-------------|---------|
| `ingress.enabled` | Enable Ingress | `false` |
| `ingress.className` | Ingress class | `""` |
| `ingress.hosts` | Ingress hosts | see values.yaml |
| `ingress.tls` | TLS configuration | `[]` |

### Persistence

| Parameter | Description | Default |
|-----------|-------------|---------|
| `persistence.enabled` | Enable PVC for local storage | `true` |
| `persistence.size` | PVC size | `10Gi` |
| `persistence.storageClass` | Storage class | `""` |
| `persistence.accessModes` | Access modes | `[ReadWriteOnce]` |

Set `persistence.enabled: false` when using S3 storage.

### NORA Configuration

The `config` section maps directly to NORA's `config.toml`:

```yaml
config:
  server:
    host: "0.0.0.0"
    port: 4000
  storage:
    mode: local       # or "s3"
    path: /data/storage
  gc:
    enabled: true
    interval: 86400
  retention:
    enabled: false
    interval: 86400
    rules: []
```

### Environment Variables and Secrets

```yaml
env:
  NORA_AUTH_ENABLED: "true"

secrets:
  NORA_STORAGE_S3_ACCESS_KEY: "your-key"
  NORA_STORAGE_S3_SECRET_KEY: "your-secret"
```

Values in `secrets` are stored in a Kubernetes Secret and injected as env vars.

### Resources

```yaml
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    memory: 512Mi
```

## S3 Storage Example

```yaml
persistence:
  enabled: false

config:
  storage:
    mode: s3

secrets:
  NORA_STORAGE_S3_ACCESS_KEY: "AKIA..."
  NORA_STORAGE_S3_SECRET_KEY: "wJal..."

env:
  NORA_STORAGE_S3_BUCKET: "my-registry"
  NORA_STORAGE_S3_REGION: "eu-west-1"
```

## Testing

```bash
helm test nora
```
