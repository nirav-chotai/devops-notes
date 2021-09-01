# Identify the node

```shell script
(base) ➜  ~ kubectl -n rook-ceph get po -l app=rook-ceph-mon
NAME                               READY   STATUS    RESTARTS   AGE
rook-ceph-mon-b-86f9d4f6b-7r2cw    1/1     Running   0          47h
rook-ceph-mon-d-68fb564797-hcxl4   1/1     Running   0          7d
rook-ceph-mon-e-86874f78fc-rntkg   1/1     Running   0          47h

(base) ➜  ~ kubectl -n rook-ceph get po -l app=rook-ceph-mon -o wide
NAME                               READY   STATUS    RESTARTS   AGE   IP                NODE           NOMINATED NODE   READINESS GATES
rook-ceph-mon-b-86f9d4f6b-7r2cw    1/1     Running   0          47h   192.168.142.92    k8s-prod-23    <none>           <none>
rook-ceph-mon-d-68fb564797-hcxl4   1/1     Running   0          7d    192.168.181.61    k8s-prod-121   <none>           <none>
rook-ceph-mon-e-86874f78fc-rntkg   1/1     Running   0          47h   192.168.130.254   k8s-prod-22    <none>           <none>
```

# Get and describe Mon ConfigMap

```shell script
(base) ➜  ~ kubectl -n rook-ceph get cm rook-ceph-mon-endpoints
NAME                      DATA   AGE
rook-ceph-mon-endpoints   4      477d

(base) ➜  ~ kubectl -n rook-ceph describe cm rook-ceph-mon-endpoints
Name:         rook-ceph-mon-endpoints
Namespace:    rook-ceph
Labels:       <none>
Annotations:  <none>

Data
====
data:
----
d=10.X.X.X:6789,e=10.X.X.X:6789,b=10.X.X.X:6789
mapping:
----
{"node":{"b":{"Name":"k8s-prod-23","Hostname":"k8s-prod-23","Address":"100.99.168.11"},"d":{"Name":"k8s-prod-121","Hostname":"dps-jp2-k8s-prod-121","Address":"100.78.16.98"},"e":{"Name":"k8s-prod-22","Hostname":"k8s-prod-22","Address":"100.99.168.8"}}}
maxMonId:
----
4
csi-cluster-config-json:
----
[{"clusterID":"rook-ceph","monitors":["10.X.X.X:6789","10.X.X.X:6789","10.X.X.X:6789"]}]
Events:  <none> 
```

# Get the Mon Deployments
```shell script
(base) ➜  ~ kubectl -n rook-ceph get deploy -l app=rook-ceph-mon
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
rook-ceph-mon-b   1/1     1            1           47h
rook-ceph-mon-d   1/1     1            1           214d
rook-ceph-mon-e   1/1     1            1           47h
```

# Edit the specific Mon ConfigMap
```shell script
(base) ➜  ~ kubectl -n rook-ceph get cm rook-ceph-mon-endpoints -o yaml > rook-ceph-mon-endpoints-backup.yaml

(base) ➜  ~ kubectl -n rook-ceph edit cm rook-ceph-mon-endpoints  
```
Remove specific node entry from `mapping` section, and save the `ConfigMap`

# Delete the Deployment
```shell script
(base) ➜  ~ kubectl -n rook-ceph delete deploy rook-ceph-mon-e
```

# Verify the Mon Deployments
```shell script
(base) ➜  ~ kubectl -n rook-ceph get deploy -l app=rook-ceph-mon
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
rook-ceph-mon-b   1/1     1            1           47h
rook-ceph-mon-d   1/1     1            1           214d
rook-ceph-mon-e   1/1     1            1           47h
```

If older `Mon` is not deleted, and the new `Mon` is not created on another node, then restart the `rook operator`.
OR check the `rook operator` logs.

Reference: https://github.com/rook/rook/issues/2262#issuecomment-460898915