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

1. Set labels to Kubernetes nodes.

    ```console
    kubectl label nodes --all topology.kubernetes.io/region=local topology.kubernetes.io/zone=local
    ```

1. Deploy a CockroachDB Operator by using Helm.

    ```console
    helm repo add cockroachdb https://charts.cockroachdb.com/v2
    ```

    ```console
    helm repo update cockroachdb
    ```

    ```console
    helm install cockroachdb-operator cockroachdb/cockroachdb-operator-chart -f ./cockroachdb-operator.yaml -n crdb --version 1.1.0
    ```

1. Deploy a CockroachDB cluster by using Helm.

    ```console
    helm install cockroachdb cockroachdb/cockroachdb-chart -f ./cockroachdb.yaml -n crdb --version 26.4.0
    ```

## Run benchmark tool (TPC-C)

1. Deploy a client pod to run SQL CLI.

    ```console
    kubectl apply -f ./client.yaml -n crdb
    ```

1. Initialize the TPC-C tables.

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach workload init tpcc --warehouses 1 'postgresql://root@cockroachdb-public:26257?sslcert=cockroach-certs%2Fclient.root.crt&sslkey=cockroach-certs%2Fclient.root.key&sslmode=verify-full&sslrootcert=cockroach-certs%2Fca.crt'
    ```

1. Run TPC-C queries.

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach workload run tpcc --warehouses 1 --duration 300s 'postgresql://root@cockroachdb-public:26257?sslcert=cockroach-certs%2Fclient.root.crt&sslkey=cockroach-certs%2Fclient.root.key&sslmode=verify-full&sslrootcert=cockroach-certs%2Fca.crt'
    ```

1. [Optional] Access (run SQL commands in) CockroachDB by using SQL CLI.

    ```console
    kubectl exec -it cockroachdb-client -n crdb -- ./cockroach sql --certs-dir=./cockroach-certs --host=cockroachdb-public --database=defaultdb
    ```

## [Optional] Access DB Console through https://localhost:8080.

```console
kubectl port-forward svc/cockroachdb-public 8080:8080 -n crdb
```

> **Note:** The `username / password` is `goki / buri`.

## How to clean up CockroachDB Cluster and client

```console
helm uninstall opentelemetry-collector -n crdb
```
```console
kubectl delete -f ./client.yaml -n crdb
```
```console
helm uninstall cockroachdb -n crdb
```
```console
helm uninstall cockroachdb-operator -n crdb
```
```console
kubectl delete configmap cockroachdb-dashboards -n crdb
```
```console
kubectl delete ns crdb
```
