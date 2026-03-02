# MyProject Helm Chart

Deploys a Kubernetes Deployment, Service, and HPA. No default `values.yaml`—provide values via `--set` or external YAML.

## Chart Values

| Parameter | Description | Default | Example |
|-----------|-------------|---------|---------|
| `replicaCount` | Desired replicas | `1` | `2` |
| `image.repository` | Container image repository | - | `nginx` |
| `image.tag` | Image tag | `latest` | `1.21` |
| `service.type` | Service type | `ClusterIP` | `LoadBalancer` |
| `service.port` | Service port | `80` | `8080` |
| `service.targetPort` | Target pod port | `80` | `3000` |
| `hpa.minReplicas` | HPA minimum replicas | `1` | `2` |
| `hpa.maxReplicas` | HPA maximum replicas | `10` | `20` |
| `hpa.targetCPUUtilizationPercentage` | CPU target % | `50` | `70` |
| `livenessProbe.enabled` | Enable liveness probe | `false` | `true` |
| `livenessProbe.path` | Health check path | `/health` | `/actuator/health` |
| `livenessProbe.initialDelaySeconds` | Initial delay | `30` | `60` |
| `readinessProbe.enabled` | Enable readiness probe | `false` | `true` |
| `readinessProbe.path` | Ready check path | `/ready` | `/actuator/ready` |
| `readinessProbe.initialDelaySeconds` | Initial delay | `5` | `10` |
| `nodeAffinity.enabled` | Enable node affinity on the pod | `false` | `true` |
| `nodeAffinity.config`  | Raw Kubernetes `nodeAffinity` spec rendered under `spec.template.spec.affinity.nodeAffinity` | - | See example below |


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
