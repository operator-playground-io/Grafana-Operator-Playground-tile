---
title: Grafana Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator



***Delete the operator's Custom Resources by kubectl delete commands :***

 
Example:
 
 ```execute
 kubectl delete -f GrafanaInstance.yaml -n my-grafana-operator
 kubectl delete -f GrafanaService.yaml -n my-grafana-operator
 kubectl delete -f MariaDBserver.yaml -n my-mariadb-operator-app
 kubectl delete -f prometheusInstance.yaml -n operators
 kubectl delete -f prometheus_service.yaml -n operators
 kubectl delete -f MariaDBmonitoring.yaml -n my-mariadb-operator-app 
 kubectl delete -f ServiceMonitor.yaml -n operator
 kubectl delete -f prometheus-datasources.yaml -n my-grafana-operator
 ```

 
Note: Here GrafanaInstance.yaml is the Custom Resource  of the Grafana Server Instance.

 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```execute
 kubectl delete -f https://operatorhub.io/install/grafana-operator.yaml
 kubectl delete -f https://operatorhub.io/install/prometheus.yaml
 kubectl delete -f https://operatorhub.io/install/mariadb-operator-app.yaml
 ```
 

 
***Delete all the yaml files:***
 
 Example:
 
 ```execute
 rm -rf GrafanaInstance.yaml
 rm -rf GrafanaService.yaml 
 rm -rf MariaDBserver.yaml 
 rm -rf prometheusInstance.yaml 
 rm -rf prometheus_service.yaml
 rm -rf MariaDBmonitoring.yaml 
 rm -rf ServiceMonitor.yaml 
 rm -rf prometheus-datasources.yaml
```
  

