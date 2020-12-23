---
title: Prometheus Monitoring
description: This tutorial explains how Prometheus monitors targets/endpoints
---

### Monitoring using Prometheus and Grafana 


Prometheus is designed to monitor targets. Servers, databases, standalone virtual machines etc can be monitored with Prometheus.

Prometheus monitoring feature :

- Prometheus is a pull based monitoring system.

- Prometheus actively screens targets in order to retrieve metrics from them.

- In order to monitor systems, Prometheus will periodically scrap them.

- Prometheus expects to retrieve metrics via HTTP calls done to endpoints that are defined in Prometheus configuration.


Lets take the example of MariaDB Server. Here, we will explain the monitoring of MariaDB Sever using Prometheus and how we can check these metrics on Grafana dashboard. 

### How to monitor MariaDB server using Prometheus 

Step 1:  Install the MariaDB operator by running the following command:


```execute
kubectl create -f https://operatorhub.io/install/mariadb-operator-app.yaml             
```

- After installation, verify that your operator got successfully installed by executing the below command.


```execute
kubectl get csv -n my-mariadb-operator-app
```

Output:

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
mariadb-operator.v0.0.4   Mariadb Operator   0.0.4     mariadb-operator.v0.0.3   Succeeded
```

Note: Once operator is successfully installed, Output PHASE should be as "Succeeded".



- Check the Pods status using below command:


```execute
kubectl get pods -n my-mariadb-operator-app
```

Output:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-d5vgr   1/1     Running   0          100s
```

Note: In above output, STATUS as "Running" shows the pods are up and running.


Step 2: Create below yaml definition of the Custom Resource to create MariaDB Server Instance and database called test-db along with user credentials.

```execute
cat <<'EOF' > MariaDBserver.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: MariaDB
metadata:
  name: mariadb
spec:
  # Keep this parameter value unchanged.
  size: 1
  
  # Root user password
  rootpwd: password

  # New Database name
  database: test-db
  # Database additional user details (base64 encoded)
  username: db-user 
  password: db-user 

  # Image name with version
  image: "mariadb/server:10.3"

  # Database storage Path
  dataStoragePath: "/mnt/data" 

  # Database storage Size (Ex. 1Gi, 100Mi)
  dataStorageSize: "1Gi"

  # Port number exposed for Database service
  port: 30685
EOF
```

- Execute below command to create an instance of MariaDBserver using the above yaml definition:

```execute
kubectl create -f MariaDBserver.yaml -n my-mariadb-operator-app 
```

Output:

```
mariadb.mariadb.persistentsys/mariadb created
```

- Check pods status :

```execute
kubectl get pods -n my-mariadb-operator-app
```

Output:

```
NAME                               READY   STATUS    RESTARTS   AGE
mariadb-operator-f96ddc69f-l44r4   1/1     Running   0          10m
mariadb-server-5dccfb7b59-rwzqp    1/1     Running   0          10m
```


Step 3 : Access MariaDB Database. 

- Connect to MariaDB Server pod.

 - Copy below command to the terminal,add the podname of MariaDB Server Instance.
    
```copycommand
 kubectl exec -it <podname> bash -n my-mariadb-operator-app
 ```


 - Connect to the database using username **db-user** and password **db-user**


 ```execute
 mysql -h ##DNS.ip## -P 30685 -u db-user -pdb-user
 ```


- list database

```execute
show databases;
```


- exit the database.


```execute
exit
```


- To login through root user use below command:


```execute
mysql -h ##DNS.ip## -P 30685 -u root -ppassword
```

- Create database testdb

```execute
create database testdb;
```


- Use the testdb to create some table 

```execute
use testdb;
```


- Create table 

```execute
create table Population(year numeric,population numeric);
```

- Insert few datas into the table so that we can check MariaDB mySQL metrics from Grafana Dashboard.

```execute
insert into Population values(2017,1380004385 );
```

```execute
insert into Population values(2018,1366417754 );
```

```execute
insert into Population values(2019,1352642280 );
```

```execute
insert into Population values(2020,1338676785 );
```


- Retrieve the data from table

```execute
select * from Population;
```

- Exit from testdb :

```execute
exit
```

- Exit from pod :

```execute
exit
```


Step 4: Enable monitoring service for MariaDB Server.


- Execute below command to get services of MariadB:
  
  ```execute
  kubectl get svc -n my-mariadb-operator-app
  ```

 Output:
 ```
      NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)             AGE
 mariadb-operator-metrics   ClusterIP   10.111.158.91    <none>        8383/TCP,8686/TCP   3d4h
 mariadb-service            NodePort    10.106.178.202   <none>        80:30685/TCP        3d4h
 ```
 
- Get the port of mariadb-service :
  
  From above command output, mariadb-service port is 30685 

- To enable monitoring using Prometheus exporter pod and service, create the below yaml definition of the Custom Resource.

```execute
cat <<'EOF'> MariaDBmonitoring.yaml
apiVersion: mariadb.persistentsys/v1alpha1
kind: Monitor
metadata:
  name: mariadb-monitor
spec:
  # Add fields here
  size: 1
  # Database source to connect with for colleting metrics
  # Format: "<db-user>:<db-password>@(<dbhost>:<dbport>)/<dbname>">
  # Make approprite changes 
  dataSourceName: "root:password@(##DNS.ip##:30685)/test-db"
  # Image name with version
  # Refer https://registry.hub.docker.com/r/prom/mysqld-exporter for more details
  image: "prom/mysqld-exporter"
EOF
```

Note: The database host and port should be correct for metrics to work. Host will be cluster IP and the port will be mariadb-service port(see Step 4).


- Execute below command to Create Instance of Monitoring 

```execute
kubectl create -f MariaDBmonitoring.yaml -n my-mariadb-operator-app
```

Output:


```
monitor.mariadb.persistentsys/mariadb-monitor created
```

This will start Prometheus exporter pod and service. 



- Check pods status :


```execute
kubectl get pods -n my-mariadb-operator-app
```

Output:

```
NAME                                          READY   STATUS    RESTARTS   AGE
mariadb-monitor-deployment-7dd85cfbbd-kbbjb   1/1     Running   0          9s
mariadb-operator-f96ddc69f-l44r4              1/1     Running   0          16m
mariadb-server-5dccfb7b59-rwzqp               1/1     Running   0          16m
```


### Install Prometheus Operator and Create Instance of Prometheus Server and ServiceMonitor

Step 5: 

- Install the Prometheus operator by running the following command:

```execute
kubectl create -f https://operatorhub.io/install/prometheus.yaml
```

Output:

```
subscription.operators.coreos.com/my-prometheus created
```

This Operator will be installed in the "operators" namespace and will be usable from all namespaces in the cluster.


- After Operator installation, verify that your operator got successfully installed by executing the below command :

```execute
kubectl get csv -n operators
```

Output:

```
NAME                        DISPLAY               VERSION   REPLACES                    PHASE
prometheusoperator.0.37.0   Prometheus Operator   0.37.0    prometheusoperator.0.32.0   Succeeded
```

From above output, once operator is successfully installed, **PHASE** will be as "Succeeded" 

- Check the Pods status using below command:

```execute
kubectl get pods -n operators
```

Output:

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          8m35s
```

In above output, STATUS as "Running" shows the pods are up and running.


If you are installing from operatorhub, then by default it installs the operator in operators namespace.
Below steps assumes that its deployed in operators namespace.



- Create below yaml definition of the Custom Resource to create a Prometheus Instance along with a ServiceAccount and a Service of Type NodePort :


```execute
cat <<'EOF' > prometheusInstance.yaml
apiVersion: monitoring.coreos.com/v1
kind: Prometheus
metadata:
  name: server
  labels:
    prometheus: k8s
  namespace: operators
spec:
  replicas: 1
  serviceAccountName: prometheus-k8s
  securityContext: {}
  serviceMonitorSelector:
    matchLabels:
      app: playground  
  alerting:
    alertmanagers:
      - namespace: operators
        name: alertmanager-main
        port: web  
EOF
```


- Execute below command to create Prometheus instance



```execute
kubectl create -f prometheusInstance.yaml -n operators
```

Output:

```
prometheus.monitoring.coreos.com/server created
```

- Get the associated Pods:

```execute
kubectl get pods -n operators
```

Output:

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          14m
prometheus-server-0                    3/3     Running   1          40s
```

- Create below yaml definition of the Custom Resource to create the service NodePort to access prometheus server


```execute
cat <<'EOF' > prometheus_service.yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  type: NodePort
  ports:
  - name: web
    nodePort: 30100
    port: 9090
    protocol: TCP
    targetPort: web
  selector:
    app: prometheus
EOF
```

- Execute below command to create Prometheus Service:

```execute
kubectl create -f prometheus_service.yaml -n operators
```

Output:

```
service/prometheus created
```


- Create below yaml definition of the Custom Resource to create Instance of ServiceMonitor:


```execute
cat <<'EOF' > ServiceMonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: mariadb-monitor 
  labels:
    app: playground
  namespace: operators
spec:
  namespaceSelector:
    any: true
  selector:
    matchLabels:
      tier: monitor-app 
  endpoints:
   - interval: 20s
     port: monitor    
EOF
```


- Execute below command to create ServiceMonitor instance :


```execute
kubectl create -f ServiceMonitor.yaml -n operators
```

Output:

```
servicemonitor.monitoring.coreos.com/mariadb-monitor created
```


- Get the associated Pods:

```execute
kubectl get pods -n operators
```

Output:

```
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-6f7589ff7f-wq9zd   1/1     Running   0          19m
prometheus-server-0                    3/3     Running   1          5m40s
```



Step 6 : Access the Prometheus dashboard using below link. 

http://##DNS.ip##:30100

![](_images/prom.png)


- On the prometheus UI, Go to Status -> Targets to see endpoints.


 ![](_images/targetsmariadb.PNG)



- From the dropdown you can select the query and click on "Execute" to see MariaDB Metrics. See below snapshot :


![](_images/queryexecution.PNG)



### Install Grafana Operator and create Grafana Instance and Grafana NodePort Service


Step 7 : If you already install with Grafana Operator and created Grafana Instance and Service you can skip this steps, else refer the Grafana Create Instance and Service tutorial tile in this tutorial.




Step 8: Create the below yaml definition of the Custom Resource to create Instance of Grafana Datasources :

```execute
cat <<'EOF' > prometheus-datasources.yaml
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDataSource
metadata:
  name: prometheus-grafanadatasource
spec:
  datasources:
    - access: proxy
      editable: true
      isDefault: true
      jsonData:
        timeInterval: 5s
      name: Prometheus
      type: prometheus
      url: 'http://prometheus-operated.operators:9090'
      version: 1
  name: prometheus-datasources.yaml  
EOF
```

Here we are choosing Prometheus as our datasourse.


Execute below command to create an instance of Grafana datasourse using the above yaml definition::


```execute
kubectl create -f prometheus-datasources.yaml -n my-grafana-operator
```

Output:

```
grafanadatasource.integreatly.org/prometheus-grafanadatasource created
```

Step 9 :Access and Configure Grafana dashboard via Grafana UI


- Execute below command to get all services in "my-grafana-operator" namespace.


```execute
kubectl get svc -n my-grafana-operator
```


Output :

```
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
grafana-operator-metrics   ClusterIP   10.96.188.27     <none>        8080/TCP         83s
grafana-service            ClusterIP   10.105.85.60     <none>        3000/TCP         47s
grafana-svc                NodePort    10.109.242.171   <none>        3000:30200/TCP   7s
```

Port value of "grafana-svc" Service NodePort is : 30200


We can access the Grafana dashboard on the nodePort : 30200 using below url:


Click on the <a href="http://##DNS.ip##:30200" target="_blank">http://##DNS.ip##:30200</a> to access Grafana Dashboard from your browser.


You will see the Grafana page loading as below :


![](_images/load.png)


Now click on the `Sign In` button as below :

![](_images/signin.png)

You will now need to log in to Grafana Dashboard with the following credentials in the page below:


```
user: root
password: secret
```
![](_images/login.png)


Now you will be able to see the Dashboard like below:


![](_images/dashboard.png)



