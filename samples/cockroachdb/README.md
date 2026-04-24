# CockroachDB OpenTelemetry Samples

> **Note:** This sample requires the LGTM Stack to be deployed as a monitoring backend. See the [README](../../README.md) of repository root for setup instructions.

1. Navigate to the `./samples/cockroachdb/` directory.

    ```console
    cd ./samples/cockroachdb/
    ```

1. Create a namespace.

    ```console
    kubectl create ns crdb
    ```

## How to deploy OpenTelemetry Collector

1. Deploy OpenTelemetry Collector by using Helm.

    ```console
    helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
    ```

    ```console
    helm repo update open-telemetry
    ```

    ```console
    helm install opentelemetry-collector open-telemetry/opentelemetry-collector -f otel-collector.yaml --version 0.152.0 -n crdb
    ```

## How to deploy Grafana dashboards for CockroachDB

1. Create a ConfigMap from the dashboard JSON files.

    ```console
    kubectl create configmap cockroachdb-dashboards --from-file=./dashboard/ -n crdb
    ```

1. Add a label to the ConfigMap for Grafana sidecar detection.

    ```console
    kubectl label configmap cockroachdb-dashboards grafana_dashboard=1 -n crdb
    ```

## How to set up CockroachDB

1. Deploy a CockroachDB cluster by using Helm.

    ```console
    helm repo add cockroachdb https://charts.cockroachdb.com/
    ```

    ```console
    helm repo update cockroachdb
    ```

    ```console
    helm install cockroachdb cockroachdb/cockroachdb -f ./cockroachdb.yaml --version 20.0.2 -n crdb
    ```

1. Deploy a client pod to run SQL CLI.

    ```console
    kubectl apply -f ./client.yaml -n crdb
    ```

1. Set up CockroachDB.

    > **Note:** This script inserts 300,000 rows of sample data (approximately 60MB). It may take a few minutes to complete.

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach sql --certs-dir=./cockroach-certs --host=cockroachdb-public --file /setup/setup-otel.sql
    ```

1. Run a sample query to generate spans.

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach sql --certs-dir=./cockroach-certs --host=cockroachdb-public --database=otel_test -e "SELECT * FROM otel_test.foo WHERE index = 3"
    ```

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach sql --certs-dir=./cockroach-certs --host=cockroachdb-public --database=otel_test -e "SELECT * FROM otel_test.foo WHERE index = 33"
    ```

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach sql --certs-dir=./cockroach-certs --host=cockroachdb-public --database=otel_test -e "SELECT * FROM otel_test.foo WHERE index = 333"
    ```

1. [Optional] Access (run SQL commands in) CockroachDB by using SQL CLI.

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach sql --certs-dir=./cockroach-certs --host=cockroachdb-public --database=otel_test
    ```

## How to clean up CockroachDB Cluster and client

```console
helm uninstall opentelemetry-collector cockroachdb -n crdb
```
```console
kubectl delete -f ./client.yaml -n crdb
```
```console
kubectl delete configmap cockroachdb-dashboards -n crdb
```
```console
kubectl delete ns crdb
```
