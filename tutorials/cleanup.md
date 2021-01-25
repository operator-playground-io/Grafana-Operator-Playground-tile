---
title: Grafana Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator



***Delete the operator's Custom Resources by kubectl delete commands :***

 
Example:
 
 ```
 kubectl delete -f GrafanaInstance.yaml -n my-grafana-operator
 ```

Note: Here GrafanaInstance.yaml is the Custom Resource  of the Grafana Server Instance.
Similarly,delete all the Custom Resources.
 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```
 kubectl delete -f https://operatorhub.io/install/grafana-operator.yaml
 ```
 

***Deleting the CSV resource:***


- Find the CSV in the namespace

Example:

```
kubectl get csv -n my-grafana-operator
```

- Delete that CSV :

Example:

```
kubectl delete csv/grafana-operator.v3.2.0 -n my-grafana-operator
```

 
***Delete all the yaml files:***
 
 Example:
 
  ```
  rm -rf GrafanaInstance.yaml
  ```
  

