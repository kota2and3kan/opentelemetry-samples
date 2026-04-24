# Kubernetes OpenTelemetry Samples

> **Note:** This sample requires the LGTM Stack to be deployed as a monitoring backend. See the [README](../../README.md) of repository root for setup instructions.

1. Navigate to the `./samples/kubernetes/` directory.

    ```console
    cd ./samples/kubernetes/
    ```

1. Create a namespace.

    ```console
    kubectl create ns otel-k8s
    ```

1. Add a Helm repository of OpenTelemetry Collector.

    ```console
    helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
    ```

    ```console
    helm repo update open-telemetry
    ```

## How to deploy DaemonSet Collector

> **Note:** See [DaemonSet Collector](https://opentelemetry.io/docs/platforms/kubernetes/getting-started/#daemonset-collector) for more details.

1. Deploy OpenTelemetry Collector by using Helm.

    ```console
    helm install opentelemetry-collector-k8s-daemonset open-telemetry/opentelemetry-collector -f otel-collector-daemonset.yaml --version 0.152.0 -n otel-k8s
    ```

## How to deploy Deployment Collector

> **Note:** See [Deployment Collector](https://opentelemetry.io/docs/platforms/kubernetes/getting-started/#deployment-collector) for more details.

1. Deploy OpenTelemetry Collector by using Helm.

    ```console
    helm install opentelemetry-collector-k8s-deployment open-telemetry/opentelemetry-collector -f otel-collector-deployment.yaml --version 0.152.0 -n otel-k8s
    ```

## How to clean up OpenTelemetry Collectors

```console
helm uninstall opentelemetry-collector-k8s-daemonset opentelemetry-collector-k8s-deployment -n otel-k8s
```
```console
kubectl delete ns otel-k8s
```
