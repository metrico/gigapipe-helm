# <img src='https://avatars.githubusercontent.com/u/76224143?s=400&u=4e207cf756a7146392f9f04c6beb3940a417369d&v=4' style="margin-left:-10px" width=28 /> [Helm Chart for Gigapipe](https://gigapipe.com)
<a href="https://gigapipe.com" target="_blank">
<img src='https://github.com/user-attachments/assets/fc8c7ca9-7a18-403d-b2a6-17899a534d33' style="margin-left:-10px;width:200px;" width=200/>
</a>

## Overview
This Helm chart provides Kubernetes deployment configurations for [gigapipe](https://gigapipe.com) a polyglot, lighweight, multi-standard observability framework for Logs, Metrics and Traces, designed to be drop-in compatible with Loki, Prometheus, Tempo and Opentelemetry.

---

## Table of Contents
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Parameters](#parameters)
- [Contributing](#contributing)
- [License](#license)

---

## Prerequisites
- Kubernetes 1.19+
- Helm 3.7+
- A running ClickHouse server (optional but recommended)


---

## Installation

Get Repository Info

```bash
helm repo add qryn-helm https://metrico.github.io/qryn-helm/
helm repo update
```

See [helm repository](https://helm.sh/docs/helm/helm_repo/) for command documentation.

Install Chart
To deploy [gigapipe](https://github.com/metrico/qryn) using this Helm chart, use the following command:

```bash
helm repo add qryn-helm https://metrico.github.io/qryn-helm/
helm install [RELEASE_NAME] qryn-helm/qryn-helm --version [Helm chart version]
```

See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation.

For customization, you can provide a `values.yaml` file or use `--set` flags to override specific configurations during installation.

Feel free to modify the configurations based on your requirements and environment.

---

## Parameters

### Global Parameters
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `kubernetesClusterDomain`     | `cluster.local`        | Kubernetes cluster DNS domain.                       |

### Image Parameters
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `image.repository`            | `ghcr.io/metrico/gigapipe` | Gigapipe image repository.                               |
| `image.tag`                   | `""`                   | Gigapipe image tag (default: latest).                    |
| `imagePullSecrets`            | `[]`                   | Secrets for pulling images.                          |
| `imageCredentials`            | `{}`                   | Custom image registry credentials.                   |

### Deployment Parameters
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `replicaCount`                | `1`                    | Number of replicas.                                  |
| `podAnnotations`              | `{}`                   | Annotations for pods.                                |
| `podLabels`                   | `{}`                   | Labels for pods.                                     |
| `resources`                   | *See `values.yaml`*    | Resource requests and limits.                        |

### Service Parameters
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `service.type`                | `ClusterIP`            | Service type (e.g., ClusterIP, NodePort, LoadBalancer).|
| `service.port`                | `3100`                 | Service port.                                        |

### Probes
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `livenessProbe`               | `{}`                   | Liveness probe config.                               |
| `readinessProbe`              | `{}`                   | Readness probe config.                      
### Ingress Parameters
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `ingress.enabled`             | `false`                | Enable ingress.                                      |
| `ingress.className`           | `""`                   | Ingress class name.                                  |
| `ingress.annotations`         | `{}`                   | Annotations for ingress.                             |
| `ingress.hosts`               | *See `values.yaml`*    | Hosts configuration.                                |
| `ingress.tls`                 | `[]`                   | TLS configuration.                                   |

### Autoscaling Parameters
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `autoscaling.enabled`         | `false`                | Enable Horizontal Pod Autoscaler.                    |
| `autoscaling.minReplicas`     | `1`                    | Minimum number of replicas.                          |
| `autoscaling.maxReplicas`     | `5`                    | Maximum number of replicas.                          |
| `autoscaling.targetCPUUtilizationPercentage` | `80`    | Target CPU utilization percentage.                   |
| `autoscaling.targetMemoryUtilizationPercentage` | `80` | Target memory utilization percentage.               |

### Environment Variables
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `env.CLICKHOUSE_SERVER`       | `localhost`            | ClickHouse server address.                           |
| `env.CLICKHOUSE_PORT`         | `9000`                 | ClickHouse server port.                              |
| `env.CLICKHOUSE_PROTO`        | `tcp`                  | ClickHouse server protocol (tcp/tls).                |
| `env.CLICKHOUSE_DB`           | `qryn`                 | ClickHouse database name.                            |
| `env.CLICKHOUSE_AUTH`         | `default:`             | ClickHouse authentication credentials.               |

### Security and Service Account
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `serviceAccount.create`       | `true`                 | Create a ServiceAccount.                             |
| `serviceAccount.automount`    | `false`                | Automount API credentials.                           |
| `serviceAccount.name`         | `""`                   | Custom service account name.                         |

### Node Affinity and Tolerations
| **Key**                       | **Default**            | **Description**                                        |
|-------------------------------|------------------------|------------------------------------------------------|
| `nodeSelector`                | `{}`                   | Node selection constraints.                          |
| `tolerations`                 | `[]`                   | Toleration rules.                                    |
| `affinity`                    | `{}`                   | Affinity rules.                                      |


## Qryn image parameters

| Environment Variable             | Default        | Usage                                     |
|---------------------------------|----------------|-------------------------------------------|
| CLICKHOUSE_SERVER               | localhost      | Clickhouse Server address                |
| CLICKHOUSE_PORT                 | 8123           | Clickhouse Server port                   |
| CLICKHOUSE_DB                   | qryn           | Clickhouse Database Name                 |
| CLICKHOUSE_AUTH                 | default:       | Clickhouse Authentication (user:password)|
| CLICKHOUSE_PROTO                | http           | Clickhouse Protocol (http, https)        |
| CLICKHOUSE_TIMEFIELD            | record_datetime| Clickhouse DateTime column for native queries|
| CLUSTER_NAME                    | undefined      | Clickhouse Cluster name                  |
| BULK_MAXAGE                     | 2000           | Max Age for Bulk Inserts                 |
| BULK_MAXSIZE                    | 5000           | Max Size for Bulk Inserts                |
| BULK_MAXCACHE                   | 50000          | Max Labels in Memory Cache               |
| LABELS_DAYS                     | 7              | Max Days before Label rotation           |
| SAMPLES_DAYS                    | 7              | Max Days before Timeseries rotation      |
| HOST                            | 0.0.0.0        | HTTP API IP                               |
| PORT                            | 3100           | HTTP API PORT                             |
| QRYN_LOGIN                      | undefined      | Basic HTTP Username                       |
| QRYN_PASSWORD                   | undefined      | Basic HTTP Password                       |
| READONLY                        | false          | Readonly Mode, no DB Init                 |
| OMIT_CREATE_TABLES              | false          | Omit database provisioning on startup. Dangerous.|
| FASTIFY_BODYLIMIT               | 5242880        | API Maximum payload size in bytes        |
| FASTIFY_REQUESTTIMEOUT          | 0              | API Maximum Request Timeout in ms        |
| FASTIFY_MAXREQUESTS             | 0              | API Maximum Requests per socket           |
| FASTIFY_METRICS                 | false          | API /metrics exporter                     |
| ADVANCED_PROMETHEUS_MAX_SAMPLES | 5000000        | Max samples per a promql request         |
| CORS_ALLOW_ORIGIN               | *              | CORS Allow Origin, default to any         |
| TEMPO_SPAN                      | 24             | Default span for Tempo queries in hours  |
| TEMPO_TAGTRACE                  | false          | Optional tagging of TraceID (expensive)  |
| DEBUG                           | false          | Debug Mode (for backwards compatibility) |
| LOG_LEVEL                       | info           | Log Level                                 |
| HASH                            | xxhash64       | Hash function using for fingerprints. Currently supported short-hash and xxhash64 (xxhash64 function)|
| ALERTMAN_URL                    | false          | Alertmanager API URL, i.e., http://my_alertmanager_url:1234|
| ADVANCED_SAMPLES_ORDERING       | timestamp_ns   | Specify the 'ORDER BY' your samples table should use (for multiple use comma-separated list fingerprint,timestamp_ns)|

## ENV Settings
For more information about Gigapipe environment variables, visit [qryn Environments](https://qryn.metrico.in/#/env).



---

#### Contributing

Whether it's code, documentation or grammar, we ❤️ all contributions. Not sure where to get started?

- Join our [Matrix Channel](https://matrix.to/#/#qryn:matrix.org), and ask us any questions.
- Have a PR or idea? Request a session / code walkthrough with our team for guidance.

<br>

---
#### License

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/06/AGPLv3_Logo.svg/2560px-AGPLv3_Logo.svg.png" width=200>

©️ QXIP BV, released under the GNU Affero General Public License v3.0. See [LICENSE](LICENSE) for details.
