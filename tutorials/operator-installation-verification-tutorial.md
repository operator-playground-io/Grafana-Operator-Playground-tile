---
title: Grafana Operator Installation Verification
description: This tutorial explains how to verify that the Grafana Operator has been properly installed in the namespace.
---

### Grafana Operator status verification 

**Step 1: After installation, verify that your operator has been installed successfully by executing below command.**

```execute
kubectl get csv -n my-grafana-operator
```

You will see a similar output as below.

Output:

```
NAME                      DISPLAY            VERSION   REPLACES                  PHASE
grafana-operator.v3.2.0   Grafana Operator   3.2.0     grafana-operator.v3.0.2   Succeeded
```

Note: Please wait for the PHASE status to be "Succeeded", then proceed.

**Step 2: Check the pod status.**

```execute
kubectl get pods -n my-grafana-operator
```

You will see a similar output as below.

Output:

```
NAME                                READY   STATUS    RESTARTS   AGE
grafana-operator-7574bbdbc9-skdk8   1/1     Running   0          45s
```

