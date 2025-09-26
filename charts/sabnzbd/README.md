# SABnzbd Helm Chart

A Helm chart for deploying SABnzbd, a free and easy binary newsreader, on Kubernetes.

## Overview

SABnzbd is an Open Source Binary Newsreader written in Python. It's totally free, incredibly easy to use, and works practically everywhere. SABnzbd makes Usenet as simple and streamlined as possible by automating everything we can.

This Helm chart deploys SABnzbd on a Kubernetes cluster using the Helm package manager.

## Prerequisites

- Kubernetes 1.19+
- Helm 3.0+
- A persistent volume provisioner (if using persistent storage)

## Installation

### Add the Helm Repository

```bash
helm repo add someadmin https://someadmin-org.github.io/helm-charts
helm repo update
```

### Install the Chart

```bash
helm install sabnzbd someadmin/sabnzbd
```

To install with custom values:

```bash
helm install sabnzbd someadmin/sabnzbd -f values.yaml
```

## Configuration

The following table lists the configurable parameters and their default values:

### Image Configuration

| Parameter          | Description              | Default                       |
| ------------------ | ------------------------ | ----------------------------- |
| `image.repository` | SABnzbd image repository | `lscr.io/linuxserver/sabnzbd` |
| `image.pullPolicy` | Image pull policy        | `IfNotPresent`                |
| `image.tag`        | Image tag                | `latest`                      |
| `imagePullSecrets` | Image pull secrets       | `[]`                          |

### Service Account

| Parameter                    | Description                                | Default |
| ---------------------------- | ------------------------------------------ | ------- |
| `serviceAccount.create`      | Create a service account                   | `false` |
| `serviceAccount.automount`   | Auto-mount service account API credentials | `true`  |
| `serviceAccount.annotations` | Service account annotations                | `{}`    |
| `serviceAccount.name`        | Service account name                       | `""`    |

### Pod Configuration

| Parameter            | Description                | Default |
| -------------------- | -------------------------- | ------- |
| `podAnnotations`     | Pod annotations            | `{}`    |
| `podLabels`          | Pod labels                 | `{}`    |
| `podSecurityContext` | Pod security context       | `{}`    |
| `securityContext`    | Container security context | `{}`    |

### Service Configuration

| Parameter             | Description         | Default     |
| --------------------- | ------------------- | ----------- |
| `service.type`        | Service type        | `ClusterIP` |
| `service.port`        | Service port        | `8080`      |
| `service.annotations` | Service annotations | `{}`        |
| `service.labels`      | Service labels      | `{}`        |

### Ingress Configuration

| Parameter             | Description                 | Default         |
| --------------------- | --------------------------- | --------------- |
| `ingress.enabled`     | Enable ingress              | `false`         |
| `ingress.className`   | Ingress class name          | `""`            |
| `ingress.annotations` | Ingress annotations         | `{}`            |
| `ingress.hosts`       | Ingress hosts configuration | See values.yaml |
| `ingress.tls`         | Ingress TLS configuration   | `[]`            |

### Health Checks

| Parameter        | Description                   | Default |
| ---------------- | ----------------------------- | ------- |
| `livenessProbe`  | Liveness probe configuration  | `{}`    |
| `readinessProbe` | Readiness probe configuration | `{}`    |

### Resources

| Parameter   | Description                         | Default |
| ----------- | ----------------------------------- | ------- |
| `resources` | CPU/Memory resource requests/limits | `{}`    |

### Environment Variables

| Parameter | Description                                              | Default         |
| --------- | -------------------------------------------------------- | --------------- |
| `env`     | Additional environment variables                         | See values.yaml |
| `envFrom` | Additional environment variables from ConfigMaps/Secrets | `[]`            |

### Storage

| Parameter                 | Description                    | Default             |
| ------------------------- | ------------------------------ | ------------------- |
| `persistence.accessModes` | Persistent volume access modes | `["ReadWriteOnce"]` |
| `persistence.size`        | Persistent volume size         | `1Gi`               |
| `persistence.annotations` | Persistent volume annotations  | `{}`                |
| `persistence.labels`      | Persistent volume labels       | `{}`                |

### Additional Volumes

| Parameter      | Description              | Default |
| -------------- | ------------------------ | ------- |
| `volumes`      | Additional volumes       | `[]`    |
| `volumeMounts` | Additional volume mounts | `[]`    |

### Scheduling

| Parameter      | Description   | Default |
| -------------- | ------------- | ------- |
| `nodeSelector` | Node selector | `{}`    |
| `tolerations`  | Tolerations   | `[]`    |
| `affinity`     | Affinity      | `{}`    |

## Usage Examples

### Basic Installation

```bash
helm install sabnzbd someadmin/sabnzbd
```

### Installation with Ingress

```yaml
# values.yaml
ingress:
  enabled: true
  className: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
  hosts:
    - host: sabnzbd.example.com
      paths:
        - path: /
          pathType: Prefix
  tls:
    - secretName: sabnzbd-tls
      hosts:
        - sabnzbd.example.com
```

```bash
helm install sabnzbd someadmin/sabnzbd -f values.yaml
```

### Installation with Custom Storage

```yaml
# values.yaml
persistence:
  size: 50Gi
  storageClass: "fast-ssd"

resources:
  requests:
    memory: "512Mi"
    cpu: "200m"
  limits:
    memory: "2Gi"
    cpu: "1000m"
```

### Installation with Custom Environment

```yaml
# values.yaml
env:
  - name: PUID
    value: "1001"
  - name: PGID
    value: "1001"
  - name: TZ
    value: "America/New_York"
  - name: UMASK_SET
    value: "022"
```

## Accessing SABnzbd

After installation, you can access SABnzbd using one of the following methods:

### Port Forward (for testing)

```bash
kubectl port-forward service/sabnzbd 8080:8080
```

Then open your browser to `http://localhost:8080`

### Ingress (recommended for production)

Configure the ingress as shown in the examples above, then access via your configured hostname.

### LoadBalancer or NodePort

Set the service type to `LoadBalancer` or `NodePort` and access via the external IP or node IP.

## Upgrading

To upgrade the chart:

```bash
helm upgrade sabnzbd someadmin/sabnzbd
```

## Uninstalling

To uninstall the chart:

```bash
helm uninstall my-sabnzbd
```

## Troubleshooting

### Common Issues

1. **Pod not starting**: Check if the persistent volume is available and properly configured
2. **Permission issues**: Ensure PUID and PGID environment variables are set correctly
3. **Ingress not working**: Verify ingress controller is installed and configured properly

### Debug Commands

```bash
# Check pod status
kubectl get pods -l app.kubernetes.io/name=sabnzbd

# Check pod logs
kubectl logs -l app.kubernetes.io/name=sabnzbd

# Check persistent volume claims
kubectl get pvc

# Describe pod for detailed information
kubectl describe pod <pod-name>
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This chart is licensed under the MIT License. See the LICENSE file for details.

## Links

- [SABnzbd Official Website](https://sabnzbd.org/)
- [LinuxServer SABnzbd Docker Image](https://docs.linuxserver.io/images/docker-sabnzbd)
- [Chart Repository](https://github.com/someadmin-org/helm-charts)
