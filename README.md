# Helm Chart for Gigapipe
<a href="https://gigapipe.com" target="_blank">
  <img src='https://github.com/user-attachments/assets/fc8c7ca9-7a18-403d-b2a6-17899a534d33' style="margin-left:-10px;width:200px;" height=200 >
</a>

# <img src='https://avatars.githubusercontent.com/u/76224143?s=400&u=4e207cf756a7146392f9f04c6beb3940a417369d&v=4' style="margin-left:-10px" width=28 /> [Gigapipe: All-In-One Polyglot Observability](https://gigapipe.com)

> formerly known as _qryn_

<img src="https://user-images.githubusercontent.com/1423657/232089970-c4536f16-5967-4051-85a5-8ad94fcde67c.png" height=50>&nbsp; <img src="https://github.com/metrico/qryn/assets/1423657/546faddb-fbc6-4af5-9e32-4db6da10915d" height=49>
## Overview
This Helm chart provides Kubernetes deployment configurations for [gigapipe](https://github.com/metrico/gigapipe) a polyglot, lighweight, multi-standard observability framework for Logs, Metrics , Traces and Profiling designed to be drop-in compatible with Loki, Prometheus, Tempo, Pyroscope + Opentelemetry

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
- A running ClickHouse server _(optional but recommended)_


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
## Configuration (Values)

Below are commonly tuned values. Refer to `values.yaml` for the full list.

### Image

| Key                 | Type   | Default                      | Description          |
|---------------------|--------|------------------------------|----------------------|
| `image.repository`  | string | `ghcr.io/metrico/gigapipe`   | Application image    |
| `image.tag`         | string | `""`                         | Image tag (pin in prod) |
| `image.pullPolicy`  | string | `IfNotPresent`               | Pull policy          |
| `imagePullSecrets`  | list   | `[]`                         | Image pull secrets   |

### Deployment & Pod

| Key              | Type | Default | Description                           |
|------------------|------|---------|---------------------------------------|
| `replicaCount`   | int  | `1`     | Number of replicas                    |
| `resources`      | map  | `{}`    | CPU/memory requests & limits          |
| `nodeSelector`   | map  | `{}`    | Node selector                         |
| `tolerations`    | list | `[]`    | Tolerations                           |
| `affinity`       | map  | `{}`    | Affinity                              |
| `podAnnotations` | map  | `{}`    | Pod annotations                       |

### Service & Ingress

| Key                 | Type   | Default     | Description        |
|---------------------|--------|-------------|--------------------|
| `service.type`      | string | `ClusterIP` | Service type       |
| `service.port`      | int    | `80`        | Service port       |
| `ingress.enabled`   | bool   | `false`     | Enable Ingress     |
| `ingress.className` | string | `""`        | Ingress class name |
| `ingress.hosts`     | list   | `[]`        | Hosts & paths      |
| `ingress.tls`       | list   | `[]`        | TLS secrets        |

### Environment variables

| Key                    | Type  | Default | Description |
|------------------------|-------|---------|-------------|
| `env`                  | map   | `{}`    | Plain environment variables injected directly into the container. Use this for non‑sensitive config. |
| `envRenderSecret`      | map   | `{}`    | Map of `ENV_NAME: value` pairs that the chart renders into a **Secret** and injects as env vars. Values support Helm templating via `tpl`. See [Rendered Secret](#rendered-secret-envrendersecret). |

### ClickHouse credentials (`auth.clickhouse`)

| Key                              | Type   | Default    | Description |
|----------------------------------|--------|------------|-------------|
| `auth.clickhouse.existingSecret` | string | `""`       | Name of an existing Secret containing username/password keys. |
| `auth.clickhouse.userKey`        | string | `username` | Key in the Secret for the username. |
| `auth.clickhouse.passwordKey`    | string | `password` | Key in the Secret for the password. |

> The application expects an env var **`CLICKHOUSE_AUTH`** with the value **`user:password`**. You can set it directly in `env` or let the chart compose it from an existing Secret (see below).

---

## Rendered Secret: `envRenderSecret`

Use `envRenderSecret` when you want the chart to **create a Secret for sensitive env vars**. Each map entry becomes a Secret key; the Deployment injects them as env vars.

**Example**

```yaml
# values.yaml
envRenderSecret:
  OAUTH_CLIENT_SECRET: "{{ .Values.oauth.clientSecret }}"  # templated at render time
  API_TOKEN: "s3cr3t-token"
```

This produces a Secret (name is chart‑derived) and sets `OAUTH_CLIENT_SECRET` and `API_TOKEN` in the container environment.

> Values are passed through `tpl` internally — you can reference other Helm values like `{{ .Release.Namespace }}`.

---

## ClickHouse credentials (`CLICKHOUSE_AUTH`)

`CLICKHOUSE_AUTH` must be in the form `user:password`. Choose one of the following approaches:

### Option A — Set `CLICKHOUSE_AUTH` directly (simple)

```yaml
# values.yaml
env:
  CLICKHOUSE_SERVER: clickhouse.default.svc
  CLICKHOUSE_PORT: "8123"
  CLICKHOUSE_DB: gigapipe
  CLICKHOUSE_AUTH: "admin:s3cr3t"
```

### Option B — Compose `CLICKHOUSE_AUTH` from an existing Secret (recommended)

1) Create (or reference) a Secret with **two keys**, e.g. `username` and `password`:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: some-secret
  namespace: gigapipe
type: Opaque
stringData:
  username: admin
  password: s3cr3t
```

Or via kubectl:

```bash
kubectl -n gigapipe create secret generic some-secret \
  --from-literal=username=admin \
  --from-literal=password='s3cr3t'
```

2) Point the values to that Secret and **omit** `env.CLICKHOUSE_AUTH`. The chart will set `CLICKHOUSE_USER`/`CLICKHOUSE_PASSWORD` from the Secret and compose `CLICKHOUSE_AUTH` as `$(CLICKHOUSE_USER):$(CLICKHOUSE_PASSWORD)`.

```yaml
# values.yaml
auth:
  clickhouse:
    existingSecret: some-secret
    userKey: username
    passwordKey: password

env:
  CLICKHOUSE_SERVER: clickhouse.default.svc
  CLICKHOUSE_PORT: "8123"
  CLICKHOUSE_DB: gigapipe
```

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

[^1]: Gigapipe is a 100% clear-room api implementation and does not fork, use or derivate from Grafana code or design concepts.[^1]

[^2]: Gigapipe is not affiliated or endorsed by Grafana Labs or ClickHouse Inc. All rights belong to their respective owners.[^2]

[^3]: Grafana®, Loki™ and Tempo® are a Trademark of Raintank, Grafana Labs. ClickHouse® is a trademark of ClickHouse Inc.[^3]

[^4]: Prometheus is a trademark of The Linux Foundation.[^4]