---
title: Grafana Operator Cleanup
description: Learn how to cleanup the Operator
---


### Operator Cleanup



**Step 1: Delete the operator's Custom Resources by using `kubectl delete` commands as below :**

 
 ```execute
 kubectl delete -f GrafanaInstance.yaml -n my-grafana-operator
 kubectl delete -f GrafanaService.yaml -n my-grafana-operator
 kubectl delete -f MariaDBserver.yaml -n my-mariadb-operator-app
 kubectl delete -f prometheusInstance.yaml -n operators
 kubectl delete -f prometheus_service.yaml -n operators
 kubectl delete -f MariaDBmonitoring.yaml -n my-mariadb-operator-app 
 kubectl delete -f ServiceMonitor.yaml -n operators
 kubectl delete -f prometheus-datasources.yaml -n my-grafana-operator
 ```

 


**Step 2: Delete the operator by using `kubectl delete` command followed by target link as below :***
 
 
 Example:
 
 ```execute
 kubectl delete -f https://operatorhub.io/install/grafana-operator.yaml
 kubectl delete -f https://operatorhub.io/install/prometheus.yaml
 kubectl delete -f https://operatorhub.io/install/mariadb-operator-app.yaml
 ```
 
 **Step 3: Delete the CSV resource.**
 

                  
   **Step 3.1: Find the Prometheus CSV in the namespace "Operators" using below command.**

  
  ```
   kubectl get csv -n operators
  ```

 **Step 3.2: Delete the CSV.**


  ```
   kubectl delete csv/prometheusoperator.0.37.0 -n operators
  ```

Note: The csv value may be different from above value. In the above `delete csv` command, use the csv retrieved from `kubectl get csv` command. 
 
**Step 4: Delete all the yaml files.**
 
 
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

**Conclusion: We have now successfully cleaned up the operator from cluster namespace.**

