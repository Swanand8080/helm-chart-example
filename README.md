# MyProject Helm Chart

Deploys a Kubernetes Deployment, Service, and HPA. No default `values.yaml`—provide values via `--set` or external YAML.

# Helm Chart Parameters

## General

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `timezone` | Timezone for all pods | `Asia/Kolkata` | `UTC` |
| `extraEnv` | Extra environment variables injected into pods | `[]` | See below |

## Image

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `image.repository` | Container image repository | - | `nginx` |
| `image.tag` | Image tag | `latest` | `1.21` |

## Deployment

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `replicaCount` | Number of desired pod replicas | `1` | `3` |
| `nodeAffinity.enabled` | Enable node affinity rules | `false` | `true` |
| `nodeAffinity.config` | Node affinity configuration block | - | See below |
| `livenessProbe.enabled` | Enable liveness probe | `false` | `true` |
| `livenessProbe.path` | HTTP path for liveness probe | `/health` | `/ping` |
| `livenessProbe.port` | Port for liveness probe | `service.port` | `8080` |
| `livenessProbe.initialDelaySeconds` | Delay before first liveness probe | `30` | `60` |
| `readinessProbe.enabled` | Enable readiness probe | `false` | `true` |
| `readinessProbe.path` | HTTP path for readiness probe | `/ready` | `/healthz` |
| `readinessProbe.port` | Port for readiness probe | `service.port` | `8080` |
| `readinessProbe.initialDelaySeconds` | Delay before first readiness probe | `5` | `10` |

## Service

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `service.type` | Kubernetes service type | `ClusterIP` | `LoadBalancer` |
| `service.port` | Port exposed by the service | `80` | `8080` |
| `service.targetPort` | Port on the pod to forward traffic to | `80` | `3000` |

## HPA (Horizontal Pod Autoscaler)

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `hpa.minReplicas` | Minimum number of replicas | `1` | `2` |
| `hpa.maxReplicas` | Maximum number of replicas | `10` | `20` |
| `hpa.targetCPUUtilizationPercentage` | Target CPU utilization to trigger scaling | `50` | `70` |

## Ingress

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `ingress.enabled` | Enable ingress resource | `false` | `true` |
| `ingress.ingressClassName` | Ingress class name | `nginx` | `alb` |
| `ingress.host` | Hostname for the ingress rule | - | `app.example.com` |
| `ingress.tlsSecretName` | Name of the TLS secret | - | `app-tls-secret` |
| `ingress.annotations` | Annotations to add to the ingress | `{}` | See below |

## CronJob
 
| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `cronJob.enabled` | Enable the CronJob resource | `false` | `true` |
| `cronJob.schedule` | Cron schedule expression | `*/2 * * * *` | `0 2 * * *` |
| `cronJob.suspend` | Suspend the CronJob without deleting it | `false` | `true` |
| `cronJob.concurrencyPolicy` | How to handle concurrent job runs | `Forbid` | `Allow` |
| `cronJob.successfulJobsHistoryLimit` | Number of successful jobs to retain | `3` | `5` |
| `cronJob.failedJobsHistoryLimit` | Number of failed jobs to retain | `3` | `5` |
| `cronJob.backoffLimit` | Number of retries before marking job as failed | `2` | `1` |
| `cronJob.restartPolicy` | Pod restart policy | `OnFailure` | `Never` |
| `cronJob.image.repository` | CronJob specific image repository. If not set, falls back to `image.repository` | `""` | `my-company/billing-job` |
| `cronJob.image.tag` | CronJob specific image tag. If not set, falls back to `image.tag` | `""` | `v1.4` |
| `cronJob.command` | Command to run inside the container. Overrides the image ENTRYPOINT | `[]` | `["node", "billing.js"]` |
| `cronJob.args` | Arguments passed to the command. Overrides the image CMD | `[]` | `["--env", "production"]` |
## Installation

### Using `--set` (inline)
```bash
helm install myapp ./myproject \
  --namespace myns \
  --create-namespace \
  --set replicaCount=2 \
  --set image.repository=nginx \
  --set image.tag=1.21 \
  --set service.type=LoadBalancer \
  --set service.port=80 \
  --set hpa.maxReplicas=5

### Using Values File
Create myapp-values.yaml:

replicaCount: 2
image:
  repository: nginx
  tag: "1.21"
service:
  type: LoadBalancer
  port: 80
hpa:
  minReplicas: 2
  maxReplicas: 5
  targetCPUUtilizationPercentage: 70
livenessProbe:
  enabled: true
  path: /health
  initialDelaySeconds: 60
readinessProbe:
  enabled: true
  path: /ready
  initialDelaySeconds: 10
nodeAffinity:
  enabled: true
  config:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
              operator: In
              values:
                - ssd
