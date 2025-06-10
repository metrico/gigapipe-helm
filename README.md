# Helm Chart for Gigapipe
<a href="https://gigapipe.com" target="_blank">
  <img src='https://github.com/user-attachments/assets/fc8c7ca9-7a18-403d-b2a6-17899a534d33' style="margin-left:-10px;width:200px;" height=200 >
</a>

# <img src='https://avatars.githubusercontent.com/u/76224143?s=400&u=4e207cf756a7146392f9f04c6beb3940a417369d&v=4' style="margin-left:-10px" width=28 /> [Gigapipe: All-In-One Polyglot Observability](https://gigapipe.com)

> formerly known as _qryn_

<img src="https://user-images.githubusercontent.com/1423657/232089970-c4536f16-5967-4051-85a5-8ad94fcde67c.png" height=50>&nbsp; <img src="https://github.com/metrico/qryn/assets/1423657/546faddb-fbc6-4af5-9e32-4db6da10915d" height=49>
## Overview
This Helm chart provides Kubernetes deployment configurations for [gigapipe](https://github.com/metrico/gigapipe) a polyglot, lighweight, multi-standard observability framework for Logs, Metrics , Traces and Profiling designed to be drop-in compatible with Loki, Prometheus, Tempo, Pyroscope + Opentelemetry.

---
## Important !!!
This project has been updated to provide <ins>**Gigapipe**</ins> instead of **qryn**
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
helm repo add gigapipe https://metrico.github.io/gigapipe-helm/
helm repo update
```

See [helm repository](https://helm.sh/docs/helm/helm_repo/) for command documentation.

Install Chart
To deploy [gigapipe](https://github.com/metrico/gigapipe) using this Helm chart, use the following command:

```bash
helm repo add gigapipe https://metrico.github.io/gigapipe-helm/
helm install [RELEASE_NAME] gigapipe/gigapipe --version [Helm chart version]
```

See [helm install](https://helm.sh/docs/helm/helm_install/) for command documentation.

For customization, you can provide a `values.yaml` file or use `--set` flags to override specific configurations during installation.

Feel free to modify the configurations based on your requirements and environment.

---

## Parameters

### Global Parameters
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `kubernetesClusterDomain`     | `cluster.local`           | Kubernetes cluster DNS domain.                       |
   
### Image Parameters   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `image.repository`            | `ghcr.io/metrico/gigapipe`| Gigapipe image repository.                           |
| `image.tag`                   | `""`                      | Gigapipe image tag (default: latest).                |
| `imagePullSecrets`            | `[]`                      | Secrets for pulling images.                          |
| `imageCredentials`            | `{}`                      | Custom image registry credentials.                   |
   
### Deployment Parameters   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `replicaCount`                | `1`                       | Number of replicas.                                  |
| `podAnnotations`              | `{}`                      | Annotations for pods.                                |
| `podLabels`                   | `{}`                      | Labels for pods.                                     |
| `resources`                   | *See `values.yaml`*       | Resource requests and limits.                        |
   
### Service Parameters   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `service.type`                | `ClusterIP`               | Service type (e.g., ClusterIP, NodePort, LoadBalancer)|
| `service.port`                | `3100`                    | Service port.                                        |
   
### Probes   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `livenessProbe`               | `{}`                      | Liveness probe config.                               |
| `readinessProbe`              | `{}`                      | Readness probe config.                      
### Ingress Parameters   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `ingress.enabled`             | `false`                   | Enable ingress.                                      |
| `ingress.className`           | `""`                      | Ingress class name.                                  |
| `ingress.annotations`         | `{}`                      | Annotations for ingress.                             |
| `ingress.hosts`               | *See `values.yaml`*       | Hosts configuration.                                 |
| `ingress.tls`                 | `[]`                      | TLS configuration.                                   |
   
### Autoscaling Parameters   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `autoscaling.enabled`         | `false`                   | Enable Horizontal Pod Autoscaler.                    |
| `autoscaling.minReplicas`     | `1`                       | Minimum number of replicas.                          |
| `autoscaling.maxReplicas`     | `5`                       | Maximum number of replicas.                          |
| `autoscaling.targetCPUUtilizationPercentage` | `80`       | Target CPU utilization percentage.                   |
| `autoscaling.targetMemoryUtilizationPercentage` | `80`    | Target memory utilization percentage.                |
   
### Environment Variables   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `env.CLICKHOUSE_SERVER`       | `localhost`               | ClickHouse server address.                           |
| `env.CLICKHOUSE_PORT`         | `8123`                    | ClickHouse server port.                              |
| `env.CLICKHOUSE_DB`           | `qryn`                    | ClickHouse database name.                            |
| `env.CLICKHOUSE_AUTH`         | `default:`                | ClickHouse authentication credentials.               |
   
### Security and Service Account   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `serviceAccount.create`       | `true`                    | Create a ServiceAccount.                             |
| `serviceAccount.automount`    | `false`                   | Automount API credentials.                           |
| `serviceAccount.name`         | `""`                      | Custom service account name.                         |
   
### Node Affinity and Tolerations   
| **Key**                       | **Default**               | **Description**                                        |
|-------------------------------|---------------------------|------------------------------------------------------|
| `nodeSelector`                | `{}`                      | Node selection constraints.                          |
| `tolerations`                 | `[]`                      | Toleration rules.                                    |
| `affinity`                    | `{}`                      | Affinity rules.                                      |
   

## Gigapipe image parameters 
https://gigapipe.com/docs/config.html#parameters

| ENV Variable | Default | Description |
|--------------|---------|-------------|
| `CLICKHOUSE_SERVER` | `localhost` | Clickhouse Server address |
| `CLICKHOUSE_PORT` | `9000` | Clickhouse Server port (binary) |
| `CLICKHOUSE_DB` | `gigapipe` | Clickhouse Database Name |
| `CLICKHOUSE_AUTH` | `default:` | Clickhouse Authentication (user:password) |
| `CLICKHOUSE_PROTO` | `tcp` | Clickhouse Protocol (tcp, tls) |
| `CLICKHOUSE_TIMEFIELD` | `record_datetime` | Clickhouse DateTime column for native queries |
| `CLUSTER_NAME` | `undefined` | Clickhouse Cluster name |
| `STORAGE_POLICY` | `undefined` | Clickhouse Cluster storage policy |
| `BULK_MAXAGE` | `2000` | Max Age for Bulk Inserts |
| `BULK_MAXSIZE` | `5000` | Max Size for Bulk Inserts |
| `BULK_MAXCACHE` | `50000` | Max Labels in Memory Cache |
| `LABELS_DAYS` | `7` | Max Days before Label rotation |
| `SAMPLES_DAYS` | `7` | Max Days before Timeseries rotation |
| `HOST` | `0.0.0.0` | HTTP API IP |
| `PORT` | `3100` | HTTP API PORT |
| `QRYN_LOGIN` | `undefined` | Basic HTTP Username |
| `QRYN_PASSWORD` | `undefined` | Basic HTTP Password |
| `READONLY` | `false` | Readonly Mode, no DB Init |
| `OMIT_CREATE_TABLES` | `false` | Omit database provisioning on startup. Dangerous. |
| `ADVANCED_PROMETHEUS_MAX_SAMPLES` | `5000000` | Max samples per a promql request |
| `CORS_ALLOW_ORIGIN.` | `*` | CORS Allow Origin, default to any |
| `TEMPO_SPAN` | `24` | Default span for Tempo queries in hours |
| `TEMPO_TAGTRACE` | `false` | Optional tagging of TraceID (expensive) |
| `DEBUG` | `false` | Debug Mode (for backwards compatibility) |
| `LOG_LEVEL` | `info` | Log Level |
| `HASH` | `xxhash64` | Hash function using for fingerprints. Currently supported short-hash and xxhash64 (xxhash64 function) |
| `ALERTMAN_URL` | `false` | Alertmanager API URL, ie: http://my_alertmanager_url:1234 |
| `ADVANCED_SAMPLES_ORDERING` | `timestamp_ns` | Specify the 'ORDER BY' your samples table should use (for multiple use comma-separated list fingerprint,timestamp_ns) |
| `BULK_MAX_SIZE_BYTE` | `` | max size of the bulk in bytes (empty = infinite) |
| `BULK_MAX_AGE_MS` | `100` | max age of the bulk in milliseconds. Minimum value 100ms |
| `MODE` | `all` | all, writer, reader, init_only (init db and exit) |
| `SELF_SIGNED_CERT` | `false` | Allow self-signed certificates |

---

#### Contributing

Whether it's code, documentation or grammar, we ❤️ all contributions. Not sure where to get started?

- Join our [Matrix Channel](https://matrix.to/#/#qryn:matrix.org), and ask us any questions.
- Have a PR or idea? Request a session / code walkthrough with our team for guidance.

<br>

---

#### License

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/0/06/AGPLv3_Logo.svg/2560px-AGPLv3_Logo.svg.png" width=200>

©️ HEPVEST BV, All Rights Reserves. Released under the GNU Affero General Public License v3.0

[^1]: Gigapipe is a 100% clear-room api implementation and does not fork, use or derivate from Grafana code or design concepts.[^1]

[^2]: Gigapipe is not affiliated or endorsed by Grafana Labs or ClickHouse Inc. All rights belong to their respective owners.[^2]

[^3]: Grafana®, Loki™ and Tempo® are a Trademark of Raintank, Grafana Labs. ClickHouse® is a trademark of ClickHouse Inc.[^3]

[^4]: Prometheus is a trademark of The Linux Foundation.[^4]
