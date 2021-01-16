# Interacting With The Monitoring Stack

In this section, we will learn to run some queries on Prometheus to get some of the monitoring
data about our deployment. You can run the following queries in the Prometheus ui
which can be accessed on `http://MASTER_IP:9090`. You can check [this tutorial](https://opensource.com/article/19/11/introduction-monitoring-prometheus)
and [the official docs](https://prometheus.io/docs/prometheus/latest/querying/basics/)
for an introduction to PromQL, the querying language used by Prometheus.

**Table of Contents**
- TOC
{:toc}

## Get a List of Deployments

You will need to perform autoscaling actions on several microservices and `deployment` on our deployed application.
To get a list of deployments in our cluster, you can run the following command on the master node:

```console
$ kubectl get deploy
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
redis-cart              1/1     1            1           107m
shippingservice         1/1     1            1           107m
paymentservice          1/1     1            1           107m
productcatalogservice   1/1     1            1           107m
emailservice            1/1     1            1           107m
checkoutservice         1/1     1            1           107m
recommendationservice   1/1     1            1           107m
currencyservice         1/1     1            1           107m
frontend                1/1     1            1           107m
cartservice             1/1     1            1           107m
adservice               1/1     1            1           107m
loadgenerator           1/1     1            1           107m
```

Many queries in this section will query specific information about a single deployment, but you can query similar information
about other deployments by change the name of deployment.

## Prometheus Client Libraries

The queries listed in this section are example of what is possible using Prometheus. You can automate any of
these queries using the [prometheus client library](https://prometheus.io/docs/instrumenting/clientlibs/)
for your language of choice. You may also look at a list of metrics available on prometheus to see what other
metrics are available to you.

## Get List of Pods

You can use the following to query a list of pods running in a `deployment` called `frontend`:

```promql
sum(
    kube_pod_container_resource_requests_cpu_cores{cluster="", namespace="default"}
  * on(namespace,pod)
    group_left(workload, workload_type) namespace_workload_pod:kube_pod_owner:relabel{cluster="", namespace="default", workload="frontend", workload_type="deployment"}
) by (pod)
```

## Get The CPU Usage of Pods

To get the CPU usage for pods running in a `deployment` called `frontend` (for each pod):

```promql
sum(
    rate(container_cpu_usage_seconds_total{cluster="", namespace="default"}[2m])
  * on(namespace,pod)
    group_left(workload, workload_type) namespace_workload_pod:kube_pod_owner:relabel{cluster="", namespace="default", workload="frontend", workload_type="deployment"}
) by (pod)
```

You can also see the result of this query in time going to the `graph` tab:

[![Screenshot of load generator](./img/prometheus-01.png)](./img/prometheus-01.png)

## Get The Memory Usage of Pods

To get the memory usage for pods running in a `deployment` called `frontend` (for each pod):

```promql
sum(
    (container_memory_usage_bytes{cluster="", namespace="default"})
  * on(namespace,pod)
    group_left(workload, workload_type) namespace_workload_pod:kube_pod_owner:relabel{cluster="", namespace="default", workload="frontend", workload_type="deployment"}
) by (pod)
```

## Get CPU Usage of All Deployments

To get the total CPU usage of all `deployments` in the namespace `default`:

```promql
sum(
  rate(container_cpu_usage_seconds_total{cluster="", namespace="default"}[2m])
* on(namespace,pod)
  group_left(workload, workload_type) namespace_workload_pod:kube_pod_owner:relabel{cluster="", namespace="default", workload_type="deployment"}
) by (workload, workload_type)
```

`Note:` This query returns the total CPU usage for each deployment, not the CPU usage per pod.

The graph for this query would be similar to the following image:

[![Screenshot of load generator](./img/prometheus-02.png)](./img/prometheus-02.png)

## Get Memory Usage of All Deployments

To get the total memory usage of all `deployments` in the namespace `default`:

```promql
sum(
  (container_memory_usage_bytes{cluster="", namespace="default"})
* on(namespace,pod)
  group_left(workload, workload_type) namespace_workload_pod:kube_pod_owner:relabel{cluster="", namespace="default"}
) by (workload, workload_type)
```

`Note:` This query returns the total memory usage for each deployment, not the memory usage per pod.
