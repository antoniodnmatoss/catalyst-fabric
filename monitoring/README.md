# Logging and Monitoring

Hyperledger Fabric provides options to monitor deployed solutions. You can find a list of exportable metrics in the official [Hyperledger Fabric docs.](https://hyperledger-fabric.readthedocs.io/en/release-2.2/metrics\_reference.html)

Each node (ordering and peer) in Catalyst Blockchain Platform has automatically configured the Prometheus endpoint for collecting metrics. This endpoint is available with no authorization inside a cluster and can be accessed by _`<servicename>:8443.`_

For example, you can use [Prometheus-operator](https://github.com/prometheus-operator/prometheus-operator) and configure endpoints using ServiceMonitor:

```
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
 name: fabric-servicemonitor
spec:
 endpoints:
 - interval: 15s
 port: metrics
 selector:
 matchLabels:
 monitoring: true #these labels are provisioned automatically for each service that supports monitoring endpoint
```

{% hint style="info" %}
A built-in monitoring tool based on Prometheus and Grafana will be available in future releases of Catalyst Blockchain Platform.
{% endhint %}
