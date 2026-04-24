# Kubernetes Operator Auto-Instrumentation Samples

> **Note:** This sample requires the LGTM Stack to be deployed as a monitoring backend. See the [README](../../README.md) of repository root for setup instructions.

1. Navigate to the `./samples/k8s-auto-instrumentation/` directory.

    ```console
    cd ./samples/k8s-auto-instrumentation/
    ```

1. Create a namespace.

    ```console
    kubectl create ns otel-k8s-auto-inst
    ```

## How to deploy OpenTelemetry Operator for Kubernetes

1. Deploy OpenTelemetry Operator.

    ```console
    helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
    ```

    ```console
    helm repo update open-telemetry
    ```

    ```console
    helm install opentelemetry-operator open-telemetry/opentelemetry-operator -f ./opentelemetry-operator.yaml --version 0.110.0 -n otel-k8s-auto-inst
    ```

## How to deploy OpenTelemetry Collector through OpenTelemetry Operator

1. Deploy OpenTelemetry Collector.

    ```console
    kubectl apply -f ./opentelemetry-collector.yaml -n otel-k8s-auto-inst
    ```

## How to enable auto-instrumentation for Java app

1. Deploy an Instrumentation resource.

    ```console
    kubectl apply -f ./instrumentation-java.yaml -n otel-k8s-auto-inst
    ```

## How to deploy sample workload

1. Run Keycloak as a sample Java application workload.

    ```console
    kubectl apply -f ./keycloak.yaml -n otel-k8s-auto-inst
    ```

    > **Note:**
    > - The Keycloak Service listens on port `8888` instead of the default `8080` to avoid conflicts with other services.
    > - The Keycloak admin credentials are `admin / admin`. The PostgreSQL credentials are `keycloak / keycloak`.

## How to clean up resources

```console
kubectl delete -f ./keycloak.yaml -n otel-k8s-auto-inst
```
```console
kubectl delete -f ./instrumentation-java.yaml -n otel-k8s-auto-inst
```
```console
kubectl delete -f ./opentelemetry-collector.yaml -n otel-k8s-auto-inst
```
```console
helm uninstall opentelemetry-operator -n otel-k8s-auto-inst
```
```console
kubectl delete ns otel-k8s-auto-inst
```
