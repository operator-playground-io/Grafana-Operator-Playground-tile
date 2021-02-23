---
title: Grafana Operator Installation Verification Tutorial
description: This tutorial explains how to verify that the Grafana Operator has been properly installed in the namespace
---

### Check the Grafana Operator 

After installation, verify that your operator got successfully installed by executing the below command.

```execute
kubectl get csv -n my-grafana-operator
```

You should see a similar output as below.

```output
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
grafana-operator.v3.2.0   Grafana Operator   3.2.0     grafana-operator.v3.0.2   Succeeded
```

**Please wait till `PHASE` status will be `Succeeded` and then proceed further.**

After the installation is successful , you can check your operator pods by executing the below command.

```execute
kubectl get pods -n my-grafana-operator
```

You should see a pod starting with 'grafana-operator' with Ready value '1/1' and Status 'Running' like the output below.

```output
NAME                                READY   STATUS    RESTARTS   AGE
grafana-operator-7574bbdbc9-skdk8   1/1     Running   0          45s
```

