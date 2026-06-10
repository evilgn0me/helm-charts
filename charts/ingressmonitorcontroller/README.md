# IngressMonitorController Helm Chart

Helm chart for IngressMonitorController — a Kubernetes operator that watches resources and creates uptime monitors in external monitoring providers (StatusCake, UptimeRobot, Pingdom, and others).

Source code: [github.com/evilgn0me/IngressMonitorController](https://github.com/evilgn0me/IngressMonitorController)

> Forked from [stakater/IngressMonitorController](https://github.com/stakater/IngressMonitorController), licensed under Apache 2.0.

## Installation

```sh
helm repo add evilgn0me https://evilgn0me.github.io/helm-charts

helm repo update

helm install ingress-monitor-controller evilgn0me/ingressmonitorcontroller \
  --namespace tools \
  --create-namespace
```

## Configuration

The controller reads its provider configuration from a Kubernetes secret named `imc-config` (configurable via `configSecretName`). The secret must contain a `config.yaml` key.

Example `config.yaml`:

```yaml
providers:
  - name: StatusCake
    apiKey: <your-api-key>
    apiURL: https://api.statuscake.com/v1/
    alertContacts: "<contact-group-id>"
enableMonitorDeletion: true
monitorNameTemplate: "{{.Name}}-{{.Namespace}}"
```

Create the secret before installing the chart:

```sh
kubectl create secret generic imc-config \
  --namespace tools \
  --from-file=config.yaml=./config.yaml
```

## Chart Values

| Key                          | Default                                        | Description                                                                                    |
|------------------------------|------------------------------------------------|------------------------------------------------------------------------------------------------|
| global.labels                | `{}`                                           | Labels added to all components                                                                 |
| replicaCount                 | `1`                                            | Number of controller replicas                                                                  |
| image.repository             | `ghcr.io/evilgn0me/ingressmonitorcontroller`  | Image repository                                                                               |
| image.tag                    | `v0.0.4`                                       | Image tag                                                                                      |
| image.pullPolicy             | `IfNotPresent`                                 | Image pull policy                                                                              |
| imagePullSecrets             | `[]`                                           | List of secrets used to pull images                                                            |
| nameOverride                 | `""`                                           | Partial override for the fullname template                                                     |
| fullnameOverride             | `""`                                           | Full override for the fullname template                                                        |
| watchNamespaces              | `""`                                           | Comma-separated namespaces to watch; empty watches all                                         |
| maxConcurrentReconciles      | `1`                                            | Number of concurrent reconcile workers                                                         |
| configSecretName             | `"imc-config"`                                 | Name of the secret containing provider configuration                                           |
| rbac.create                  | `true`                                         | Create RBAC resources                                                                          |
| rbac.allowProxyRole          | `true`                                         | Create RBAC for metrics proxy                                                                  |
| rbac.allowMetricsReaderRole  | `true`                                         | Create RBAC for metrics reader                                                                 |
| rbac.allowLeaderElectionRole | `true`                                         | Create RBAC for leader election                                                                |
| rbac.secretViewerRole        | `true`                                         | Create RBAC to read the imc-config secret                                                      |
| serviceAccount.create        | `true`                                         | Create a ServiceAccount                                                                        |
| serviceAccount.name          | `""`                                           | ServiceAccount name; auto-generated if empty                                                   |
| serviceAccount.labels        | `{}`                                           | Additional labels on the ServiceAccount                                                        |
| serviceAccount.annotations   | `{}`                                           | Additional annotations on the ServiceAccount                                                   |
| serviceMonitor.enabled       | `false`                                        | Create a Prometheus ServiceMonitor                                                             |
| podAnnotations               | `{}`                                           | Additional annotations on the Pod                                                              |
| podLabels                    | `{}`                                           | Additional labels on the Pod                                                                   |
| resources                    | `{}`                                           | CPU/memory requests and limits                                                                 |
| securityContext              | `{}`                                           | Container security context                                                                     |
| podSecurityContext           | `{}`                                           | Pod security context                                                                           |
| nodeSelector                 | `{}`                                           | Node selector                                                                                  |
| tolerations                  | `[]`                                           | Tolerations                                                                                    |
| affinity                     | `{}`                                           | Affinity rules                                                                                 |
