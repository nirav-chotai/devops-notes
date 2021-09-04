# Alert: daemons have recently crashed

```shell script
ceph -s
ceph health detail
ceph crash ls-new
ceph crash info <crash-id>
ceph crash archive <crash-id>
ceph crash archive-all
```

Reference: https://docs.ceph.com/en/latest/rados/operations/health-checks/#recent-crash

# Removing the Cluster CRD

Ceph Cluster deletion is stuck.

> When a Cluster CRD is created, a finalizer is added automatically by the Rook operator. The finalizer will allow the operator to ensure that before the cluster CRD is deleted, all block and file mounts will be cleaned up. Without proper cleanup, pods consuming the storage will be hung indefinitely until a system reboot.

The operator is responsible for removing the finalizer after the mounts have been cleaned up. If for some reason the operator is not able to remove the finalizer (ie. the operator is not running anymore), you can delete the finalizer manually with the following command:
```shell script
for CRD in $(kubectl get crd -n rook-ceph | awk '/ceph.rook.io/ {print $1}'); do
    kubectl get -n rook-ceph "$CRD" -o name | \
    xargs -I {} kubectl patch -n rook-ceph {} --type merge -p '{"metadata":{"finalizers": [null]}}'
done
```

```shell script
$ kubectl patch crd cephblockpools.ceph.rook.io -p '{"metadata":{"finalizers": []}}' --type=merge
customresourcedefinition.apiextensions.k8s.io/cephblockpools.ceph.rook.io patched
```

Within a few seconds you should see that the cluster CRD has been deleted and will no longer block other cleanup

Deleting the `rook-ceph` namespace is stuck.

> If the namespace is still stuck in Terminating state, you can check which resources are holding up the deletion and remove the finalizers and delete those

```shell script
kubectl api-resources --verbs=list --namespaced -o name \
  | xargs -n 1 kubectl get --show-kind --ignore-not-found -n rook-ceph
```

# Disk Cleanup

```shell script
# Disk Cleanup

#!/usr/bin/env bash
DISK="/dev/sdr"
sgdisk --zap-all $DISK
dd if=/dev/zero of="$DISK" bs=1M count=100 oflag=direct,dsync
ls /dev/mapper/ceph--33bcb3d3--6c3e--4fd5--875d--eb612c864526-osd--data--d59a2a29--e412--4716--a82b--87c5c09b5a52 | xargs -I% -- dmsetup remove %
rm -rf /dev/ceph-33bcb3d3-6c3e-4fd5-875d-eb612c864526
```