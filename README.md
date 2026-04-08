# OpenTelemetry Samples

## Deploy LGTM Stack as Monitoring Backend

1. Create a namespace.

    ```console
    kubectl create ns lgtm
    ```

1. Add helm repository.

    ```console
    helm repo add grafana-community https://grafana-community.github.io/helm-charts
    ```

    ```console
    helm repo update
    ```

1. Deploy Loki.

    ```console
    helm install loki grafana-community/loki -f ./lgtm-stack/loki.yaml --version 10.2.1 -n lgtm
    ```

1. Deploy Tempo.

    ```console
    helm install tempo grafana-community/tempo -f ./lgtm-stack/tempo.yaml --version 2.0.0 -n lgtm
    ```

1. Deploy Mimir.

    ```console
    kubectl apply -f ./lgtm-stack/mimir.yaml -n lgtm
    ```

    > **Note:** The official Mimir Helm chart (`grafana/mimir-distributed`) is designed for production use and does not support the monolithic mode (`-target=all`). Since this setup is intended for local minikube testing, Mimir is deployed directly using a Kubernetes manifest instead of Helm.

1. Deploy Grafana.

    ```console
    helm install grafana grafana-community/grafana -f ./lgtm-stack/grafana.yaml --version 11.3.7 -n lgtm
    ```

    > **Note:** The `username / password` is `admin / lgtm`.

## [Optional] How to deploy OpenTelemetry Collector with general configurations

> **Note:** If you use specific samples, you can deploy OpenTelemetry Collector in each sample directory. In other words, you don't need to deploy OpenTelemetry Collector here.

1. Deploy OpenTelemetry Collector by using Helm.

    ```console
    helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
    ```

    ```console
    helm repo update
    ```

    ```console
    helm install opentelemetry-collector open-telemetry/opentelemetry-collector -f ./lgtm-stack/otel-collector.yaml --version 0.147.1 -n lgtm
    ```

## How to run samples

### CockroachDB OpenTelemetry Samples

See [README](./samples/cockroachdb/README.md) in the `./samples/cockroachdb/` directory.

### Kubernetes OpenTelemetry Samples

See [README](./samples/kubernetes/README.md) in the `./samples/kubernetes/` directory.

## How to clean up LGTM Stack

```console
helm uninstall loki tempo grafana -n lgtm
```
```console
kubectl delete -f ./lgtm-stack/mimir.yaml -n lgtm
```

If you deployed the OpenTelemetry Collector, also run:

```console
helm uninstall opentelemetry-collector -n lgtm
```
