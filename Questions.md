# Metrics

## Set local env for the hands-on exercise

```bash
 k3d cluster create
 helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
 helm install prometheus prometheus-community/prometheus
 kubectl create ns test
 kubectl apply -f https://raw.githubusercontent.com/stefanprodan/podinfo/master/kustomize/deployment.yaml
 kubectl apply -f https://raw.githubusercontent.com/stefanprodan/podinfo/master/kustomize/service.yaml
 PROMETHEUS_POD_NAME=$(k get po -ALL | grep prometheus-server | awk '{print $2}')
 kubectl port-forward pod/$PROMETHEUS_POD_NAME 9090:9090
```

Lets generate some traffic
```bash
 kubectl apply -f https://raw.githubusercontent.com/fluxcd/flagger/main/kustomize/tester/deployment.yaml
 kubectl apply -f https://raw.githubusercontent.com/fluxcd/flagger/main/kustomize/tester/service.yaml
```

login to flagger-loadtester pod and run the following while loop to create some traffic
```bash
 LOADTESTER_POD_NAME=$(k get po -ALL | grep flagger-loadtester | awk '{print $2}')
 kubectl exec -it $LOADTESTER_POD_NAME bash
```
in side the container run the following command to start generate some traffic
```bash
while true; do curl http://podinfo:9898/status/500 && sleep 1 && curl http://podinfo:9898/status/400 && curl http://podinfo:9898/status/404 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/202 && curl http://podinfo:9898/status/202 && curl http://podinfo:9898/status/202 && curl http://podinfo:9898/status/201 && curl http://podinfo:9898/status/200 && curl http://podinfo:9898/status/201 && curl http://podinfo:9898/status/202 ; done
```

open your browser and connect to promethues ui:
```bash
http://localhost:9090
```

## Questions

### Q1 A - what is the cpu request and limit that was set for each pod, group results by pod, namesapce
(request and limit are metrics values)
look at this Docs for more info and help:
https://prometheus.io/docs/prometheus/latest/querying/basics/

### Q2 - what is the cpu usage seconds in the last 5 min, group by pod 
try and use rate function:
https://prometheus.io/docs/prometheus/latest/querying/functions/#rate \
look at this Docs for more info and help:
https://prometheus.io/docs/prometheus/latest/querying/functions/

### Q2 B - what does the returned value in Q2 means?

### Q3 - get all 2xx types of response code, for each type get the amount of requests that were recived by the microservice, filter out pods on namespace test, group by response code, namespace and pod
try and use match expressions:
https://prometheus.io/docs/prometheus/latest/querying/basics/#:~:text=!%3D%3A%20Select%20labels%20that,match%20the%20provided%20string.

### Q4 - get the % of error rate (response codes: 5XX 404 and 400), group by pod
try and use operators
https://prometheus.io/docs/prometheus/latest/querying/operators/

### Q5 - get the % of error rate (response codes: 5XX 404 and 400), in the last 5 min, group by pod
try and use rate function:
https://prometheus.io/docs/prometheus/latest/querying/functions/#rate

### Q6 - get memory limits of pod in MiB

### Q7 - get % of memory usage of pod in the last 10 min
