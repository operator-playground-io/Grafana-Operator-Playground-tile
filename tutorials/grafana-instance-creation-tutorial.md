---
title: Grafana Instance Creation 
description: This tutorial explains how to create instances of your Grafana Operator
---

###  Create Grafana Instance.

**Step 1: First, create the yaml definition of the Custom Resource as below.**

```execute
cat <<'EOF' > GrafanaInstance.yaml
apiVersion: integreatly.org/v1alpha1
kind: Grafana
metadata:
  name: example-grafana
spec:
  ingress:
    enabled: true
  config:
    auth:
      disable_signout_menu: true
    auth.anonymous:
      enabled: true
    log:
      level: warn
      mode: console
    security:
      admin_password: secret
      admin_user: root
  dashboardLabelSelector:
    - matchExpressions:
        - key: app
          operator: In
          values:
            - grafana
EOF
```

**Step 2: Execute the command below to create your Grafana instance.

```execute
kubectl create -f GrafanaInstance.yaml -n my-grafana-operator
```
You will see the following resources created:

```output
grafana.integreatly.org/example-grafana created
```

**Step 3: Wait till Pod STATUS is "Running", then proceed.**


**Step 4: Check the pod status.**

```execute
kubectl get pods -n my-grafana-operator
```

You will see a similar output as below:

```
NAME                                  READY   STATUS    RESTARTS   AGE
grafana-deployment-549c685ddc-b6dq7   1/1     Running   0          83s
grafana-operator-7574bbdbc9-skdk8     1/1     Running   0          6m4s
```

###  Create Grafana Service of type NodePort 

**Step 1: First, create the yaml definition as below.

```execute
cat <<'EOF' > GrafanaService.yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana-svc
spec:
  type: NodePort
  ports:
  - name: grafana
    nodePort: 30200
    port: 3000
    protocol: TCP
    targetPort: grafana-http
  selector:
    app: grafana
EOF
```

**Step 2: Execute the command below to create your Grafana Service.**

```execute
kubectl create -f GrafanaService.yaml -n my-grafana-operator
```

**Step 3: Find the port for `NodePort` service using the following command.**

```execute
kubectl get svc -n my-grafana-operator
```

You will see a similar output as below:

```
NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
grafana-operator-metrics   ClusterIP   10.97.234.139    <none>        8080/TCP         50s
grafana-service            ClusterIP   10.103.93.116    <none>        3000/TCP         27s
grafana-svc                NodePort    10.101.178.187   <none>        3000:30200/TCP   3s
```

From above, we can see the output NodePort is `30200`.

**Step 4: Click on http://##DNS.ip##:30200 to access your Grafana dashboard. 


The Grafana page will load as shown below. 

![](_images/load.png)

**Step 5: Now click on the Sign In button as below.**


![](_images/signin.png)


**Step 6: Log into Grafana dashboard with the following credentials.**

```
user: root
password: secret
```

![](_images/login.png)

The dashboard will now appear as follows.

![](_images/dashboard.png)


