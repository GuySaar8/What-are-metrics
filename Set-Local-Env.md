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
