# Metrics

## Answers

### Q1 A - what is the cpu request and limit that was set for each pod, group results by pod, namesapce
look at this Docs for more info and help: \
https://prometheus.io/docs/prometheus/latest/querying/basics/ \
(request and limit are metrics values)

**A**: `sum(kube_pod_container_resource_requests{resource="cpu"}) by (pod, namesapce) , sum(kube_pod_container_resource_limits{resource="cpu"}) by (pod, namesapce)`

### Q2 - what is the cpu usage seconds in the last 5 min, group by pod 
try and use rate function: \
https://prometheus.io/docs/prometheus/latest/querying/functions/#rate \
look at this Docs for more info and help:
https://prometheus.io/docs/prometheus/latest/querying/functions/

**A**: `sum(rate(container_cpu_usage_seconds_total{}[5m])) by (pod)`

### Q2 B - what does the returned value in Q2 means?

https://itnext.io/k8s-monitor-pod-cpu-and-memory-usage-with-prometheus-28eec6d84729

- **container_cpu_usage_seconds_total** - CPU usage time in seconds of a specific container, as the name suggests. A rate on top of this will show how many CPU seconds a container uses per second. - 500m = 500millicore = 0.5 core
- **container_spec_cpu_period** - Denotes the period in which container CPU utilization is tracked. I understood this as the duration of a CPU "cycle". Typically 100000 microseconds for docker containers.
- **container_spec_cpu_quota** - How much CPU time your container has for each cpu_period in microseconds—results from multiplying a "CPU unit" by the `container_spec_cpu_period`. You only have it if you define a limit for your container.

### Q3 - get all 2xx types of response code, for each type get the amount of requests that were recived by the microservice, filter out pods on namespace test, group by response code, namespace and pod
try and use match expressions: \
https://prometheus.io/docs/prometheus/latest/querying/basics/#:~:text=!%3D%3A%20Select%20labels%20that,match%20the%20provided%20string.

**A**: `sum(http_requests_total{namespace != "test" ,status=~"2.*"}) by (namespace, status, pod)`

### Q4 - get the % of error rate (response codes: 5XX 404 and 400), group by pod
try and use operators: \
https://prometheus.io/docs/prometheus/latest/querying/operators/

**A**: `sum(http_requests_total{status=~"5.*|400|404"}) by (pod)/sum(http_requests_total{}) by (pod) * 100`

### Q5 - get the % of error rate (response codes: 5XX 404 and 400), in the last 5 min, group by pod
try and use rate function: \
https://prometheus.io/docs/prometheus/latest/querying/functions/#rate

**A**: `sum(rate(http_requests_total{status=~"5.*|400|404"}[5m])) by (pod)/ sum(rate(http_requests_total{}[5m])) by (pod) * 100`

### Q6 - get memory limits of pod in MiB

**A**: `sum(kube_pod_container_resource_limits{resource="memory"}) by (pod) / 1000000 / 1.048576`

### Q7 - get % of memory usage of pod in the last 10 min

**A**: (sum(rate(container_memory_usage_bytes[10m])) by (pod) -  sum(rate(container_memory_cache[10m])) by (pod)) /  sum(container_spec_memory_limit_bytes!=0) by (pod) * 100

or

sum(rate(container_memory_working_set_bytes[10m])) by (pod) /  sum(container_spec_memory_limit_bytes!=0) by (pod) * 100

https://blog.freshtracks.io/a-deep-dive-into-kubernetes-metrics-part-3-container-resource-metrics-361c5ee46e66

container_memory_cache -- Number of bytes of page cache memory.
container_memory_rss -- Size of RSS in bytes.
container_memory_swap -- Container swap usage in bytes.
container_memory_usage_bytes -- Current memory usage in bytes,       
                                including all memory regardless of
                                when it was accessed.
container_memory_max_usage_bytes -- Maximum memory usage recorded 
                                    in bytes.
container_memory_working_set_bytes -- Current working set in bytes.
container_memory_failcnt -- Number of memory usage hits limits.
container_memory_failures_total -- Cumulative count of memory 
                                   allocation failures.

You might think that memory utilization is easily tracked with container_memory_usage_bytes, however, this metric also includes cached (think filesystem cache) items that can be evicted under memory pressure. The better metric is container_memory_working_set_bytes as this is what the OOM killer is watching for.
