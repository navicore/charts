# audio-latency-tracker

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: v0.1.0](https://img.shields.io/badge/AppVersion-v0.1.0-informational?style=flat-square)

eBPF-based audio latency tracking for Kubernetes

## Prerequisites

- Kubernetes 1.19+
- Helm 3.2.0+
- Prometheus Operator (optional, for ServiceMonitor)

## Installing the Chart

To install the chart with the release name `audio-latency-tracker`:

```bash
helm install audio-latency-tracker ./audio-latency-tracker \
  --namespace audio-latency-tracker \
  --create-namespace
```

## Uninstalling the Chart

```bash
helm uninstall audio-latency-tracker -n audio-latency-tracker
```

## Configuration

The following table lists the configurable parameters and their default values.

| Parameter | Description | Default |
|-----------|-------------|---------|
| `image.repository` | Image repository | `navicore/audio-latency-tracker` |
| `image.tag` | Image tag | `""` (uses appVersion) |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `config.interface` | Network interface to monitor | `eth0` |
| `config.logLevel` | Log level | `info` |
| `config.metricsPort` | Metrics port | `9090` |
| `config.audioPorts` | Comma-separated ports to monitor | `""` (all ports) |
| `config.signatureAlgorithm` | Signature algorithm | `xxhash` |
| `config.silenceThreshold` | PCM silence threshold | `256` |
| `config.signatureWindowSize` | Signature window size | `256` |
| `config.maxFlows` | Maximum concurrent flows | `10000` |
| `config.flowTimeoutMs` | Flow timeout in ms | `30000` |
| `config.perfBufferSize` | Per-CPU buffer size | `1024` |
| `serviceMonitor.enabled` | Create ServiceMonitor | `true` |
| `serviceMonitor.interval` | Scrape interval | `30s` |
| `resources.requests.cpu` | CPU request | `100m` |
| `resources.requests.memory` | Memory request | `128Mi` |
| `resources.limits.cpu` | CPU limit | `500m` |
| `resources.limits.memory` | Memory limit | `512Mi` |

### Example: Monitor Specific Ports

```bash
helm install audio-latency-tracker ./audio-latency-tracker \
  --set config.audioPorts="8080,8081,8082"
```

### Example: Use Different Container Runtime

```bash
helm install audio-latency-tracker ./audio-latency-tracker \
  --set containerRuntime=containerd
```

### Example: Increase Performance

```bash
helm install audio-latency-tracker ./audio-latency-tracker \
  --set config.perfBufferSize=4096 \
  --set config.maxFlows=50000 \
  --set resources.limits.memory=1Gi
```

## Metrics

The chart exposes the following Prometheus metrics on port 9090:

- `audio_latency_seconds` - Histogram of audio latency between components
- `audio_signatures_total` - Total audio signatures detected
- `audio_signature_collisions_total` - Hash collision counter

## Troubleshooting

### Check if the DaemonSet is running:

```bash
kubectl get daemonset -n audio-latency-tracker
kubectl logs -n audio-latency-tracker -l app.kubernetes.io/name=audio-latency-tracker
```

### Verify metrics endpoint:

```bash
kubectl port-forward -n audio-latency-tracker daemonset/audio-latency-tracker 9090:9090
curl http://localhost:9090/metrics
```

## Security Considerations

This chart requires privileged access to load eBPF programs. The DaemonSet runs with:
- `privileged: true`
- Host network access
- Host PID namespace access
- Capabilities: `BPF`, `NET_ADMIN`, `SYS_ADMIN`, `NET_RAW`

Ensure your cluster security policies allow these permissions.
