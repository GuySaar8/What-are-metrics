# Metrics

## Questions

### Q1 A - what is the cpu request and limit that was set for each pod, group results by pod, namesapce
look at this Docs for more info and help: \
https://prometheus.io/docs/prometheus/latest/querying/basics/ \
(request and limit are metrics values)

### Q2 - what is the cpu usage seconds in the last 5 min, group by pod 
try and use rate function: \
https://prometheus.io/docs/prometheus/latest/querying/functions/#rate \
look at this Docs for more info and help:
https://prometheus.io/docs/prometheus/latest/querying/functions/

### Q2 B - what does the returned value in Q2 means?

### Q3 - get all 2xx types of response code, for each type get the amount of requests that were recived by the microservice, filter out pods on namespace test, group by response code, namespace and pod
try and use match expressions: \
https://prometheus.io/docs/prometheus/latest/querying/basics/#:~:text=!%3D%3A%20Select%20labels%20that,match%20the%20provided%20string.

### Q4 - get the % of error rate (response codes: 5XX 404 and 400), group by pod
try and use operators: \
https://prometheus.io/docs/prometheus/latest/querying/operators/

### Q5 - get the % of error rate (response codes: 5XX 404 and 400), in the last 5 min, group by pod
try and use rate function: \
https://prometheus.io/docs/prometheus/latest/querying/functions/#rate

### Q6 - get memory limits of pod in MiB

### Q7 - get % of memory usage of pod in the last 10 min
