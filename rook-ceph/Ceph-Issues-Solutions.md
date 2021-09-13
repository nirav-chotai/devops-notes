# Alert: Ceph pgs not deep-scrubbed in time

```shell script
# Issue
HEALTH_WARN 2 pgs not deep-scrubbed in time
[WRN] PG_NOT_DEEP_SCRUBBED: 2 pgs not deep-scrubbed in time
    pg 1.1c not deep-scrubbed since 2020-11-23T04:49:37.391387+0000
    pg 1.c not deep-scrubbed since 2020-11-23T14:04:10.666403+0000
 
# Cause
The following settings osd_scrub_max_interval and osd_deep_scrub_interval are used by both OSDs and MONs.
The OSDs use them to determine when to run scrub, and the MONs use them to check if they need to show the warning.
Warnings can be disabled: "mon_warn_not_deep_scrubbed": "0", "mon_warn_not_scrubbed": "0"
If it appears frequently, then adjust "osd_scrub_max_interval" and "osd_deep_scrub_interval" accordingly.

# Solution
ceph -v
ceph -s
ceph health detail
 
ceph health detail | grep -i not | awk '{print $2}' | while read i; do ceph pg deep-scrub ${i}; done
OR
ceph pg dump | grep -i active+clean | awk '{print $1}' | while read i; do ceph pg deep-scrub ${i}; done
ceph df
ceph -w
 
# Automation
#!/bin/bash
 
# gently deep scrub all pgs which failed to deep scrub in set period
 
ceph health detail |
    awk '$1=="pg" { print $2 } ' |
    while read -r pg
    do
        ceph pg deep-scrub "$pg"
        sleep 60
    done
```

# Alert: Ceph 1 daemons have recently crashed

```shell script
# Issue
HEALTH_WARN 1 daemons have recently crashed
[WRN] RECENT_CRASH: 1 daemons have recently crashed
    mon.a crashed on host rook-ceph-mon-a-7bf9f8d7fb-bk896 at 2020-12-07T03:52:09.884281Z
 
# Solution
ceph -s
ceph health detail
ceph crash ls
ceph crash ls-new
ceph crash stat
ceph crash info <crashid>
ceph crash prune <keep>
ceph crash archive <crashid>
ceph crash archive-all
```

Reference: https://docs.ceph.com/en/latest/rados/operations/health-checks/#recent-crash

# Alert: Ceph scrub error, Possible data damage: 1 pg inconsistent

```shell script
# Issue
  cluster:
    id:     c40d82d5-3193-457d-a628-a3db67839a37
    health: HEALTH_ERR
            1 scrub errors
            Possible data damage: 1 pg inconsistent

[root@rook-ceph-tools-b5765bff4-ljw7f /]# ceph health detail
HEALTH_ERR 1 scrub errors; Possible data damage: 1 pg inconsistent
[ERR] OSD_SCRUB_ERRORS: 1 scrub errors
[ERR] PG_DAMAGED: Possible data damage: 1 pg inconsistent
    pg 3.f is active+clean+inconsistent, acting [5,4,1]

[root@rook-ceph-tools-b5765bff4-ljw7f /]# ceph pg repair 3.f
instructing pg 3.f on osd.5 to repair
[root@rook-ceph-tools-b5765bff4-ljw7f /]#

[root@rook-ceph-tools-b5765bff4-ljw7f /]# rados list-inconsistent-obj 3.f --format=json-pretty
{
    "epoch": 67946,
    "inconsistents": []
}
```

# Alert: Ceph clock skew detected
> If you are running large Ceph Cluster to provide block, object or file based Storage on top of Kubernetes, then you might run into an issue where Ceph Monitors are not in quorum or even you see an error related to clock skew. This health alert is raised if the cluster detects a clock skew greater than mon_clock_drift_allowed. Let's see how to troubleshoot and fix this issue in Kubernetes running Rook Operator to manage Ceph Clusters.

```shell script
# Issue
cluster:
  id:     c40d82d5-3193-457d-a628-a3db67839a37
  health: HEALTH_WARN
          clock skew detected on mon.d, mon.e

[root@rook-ceph-tools-b5765bff4-fprsr /]# ceph health detail
HEALTH_WARN clock skew detected on mon.d, mon.e
[WRN] MON_CLOCK_SKEW: clock skew detected on mon.d, mon.e
    mon.d clock skew 0.0629245s > max 0.05s (latency 0.00144504s)
    mon.e clock skew 0.0633409s > max 0.05s (latency 0.000750585s)
[root@rook-ceph-tools-b5765bff4-fprsr /]#

[root@rook-ceph-mon-d-68fb564797-sr96l /]# ceph daemon mon.d config show | grep mon_clock
    "mon_clock_drift_allowed": "0.050000",
    "mon_clock_drift_warn_backoff": "5.000000",
[root@rook-ceph-mon-d-68fb564797-sr96l /]#
 
 
[root@rook-ceph-mon-e-6c9f7d9785-7x8xh /]# ceph daemon mon.e config show | grep mon_clock
    "mon_clock_drift_allowed": "0.050000",
    "mon_clock_drift_warn_backoff": "5.000000",
[root@rook-ceph-mon-e-6c9f7d9785-7x8xh /]#
 
 
[root@rook-ceph-mon-b-7f776899f6-2wr8q /]# ceph daemon mon.b config show | grep mon_clock
    "mon_clock_drift_allowed": "0.050000",
    "mon_clock_drift_warn_backoff": "5.000000",
[root@rook-ceph-mon-b-7f776899f6-2wr8q /]#
```
Root Cause:

The clock skew error message indicates that Ceph Monitors' clocks are not synchronized. Clock synchronization is important because Ceph Monitors depend on time precision and behave unpredictably if their clocks are not synchronized.

The `mon_clock_drift_allowed` parameter determines what disparity between the clocks is tolerated. By default, this parameter is set to 0.05 seconds.

> Do not change the default value of mon_clock_drift_allowed without previous testing. Changing this value might affect the stability of the Ceph Monitors and the Ceph Storage Cluster in general.

Possible causes of the clock skew error include network problems or problems with Network Time Protocol (NTP) synchronization if that is configured. In addition, time synchronization does not work properly on Ceph Monitors deployed on virtual machines.

Solution:

We checked date and time on all the servers where Ceph monitors are running, they are in sync and showing equal time. For us, the value of 0.05 drift is too aggressive. We need to adjust it.

In order to set configurations before monitors are available or to set problematic configuration settings, the rook-config-override ConfigMap exists, and the config field can be set with the contents of a ceph.conf file. The contents will be propagated to all mon, mgr, OSD, MDS, and RGW daemons as an /etc/ceph/ceph.conf file.

Take the backup and update the ConfigMap

```shell script
(base) ➜  ~ kubectl -n rook-ceph describe cm rook-config-override
Name:         rook-config-override
Namespace:    rook-ceph
Labels:       <none>
Annotations: 
Data
====
config:
----
[global]
mon clock drift allowed = 0.1
 
Events:  <none>
```

After updating ConfigMap, kill the Ceph Monitor pods and check the settings on the new pods
```shell script
[root@rook-ceph-mon-e-6c9f7d9785-qd659 /]# ceph daemon mon.e config show | grep mon_clock
    "mon_clock_drift_allowed": "0.100000",
    "mon_clock_drift_warn_backoff": "5.000000",
[root@rook-ceph-mon-e-6c9f7d9785-qd659 /]#
 
 
[root@rook-ceph-mon-d-68fb564797-lq2cp /]# ceph daemon mon.d config show | grep mon_clock
    "mon_clock_drift_allowed": "0.100000",
    "mon_clock_drift_warn_backoff": "5.000000",
[root@rook-ceph-mon-d-68fb564797-lq2cp /]#
 
 
[root@rook-ceph-mon-b-7f776899f6-fkrlb /]# ceph daemon mon.b config show | grep mon_clock
    "mon_clock_drift_allowed": "0.100000",
    "mon_clock_drift_warn_backoff": "5.000000",
[root@rook-ceph-mon-b-7f776899f6-fkrlb /]#
```
> Ceph evaluates time synchronization every five minutes only so there will be a delay between fixing the problem and clearing the clock skew messages.

References
- https://docs.ceph.com/en/latest/rados/operations/health-checks/#mon-clock-skew
- https://github.com/rook/rook/issues/2286
- https://github.com/anguslees/k8s-home/blob/2d04b8480e4f37d3b15aa9180e12e72c49758a64/rook-ceph.jsonnet#L16
- https://access.redhat.com/documentation/en-us/red_hat_ceph_storage/4/html-single/troubleshooting_guide/index#clock-skew_diag
- https://www.programmersought.com/article/76897703597/
- https://github.com/rook/rook/blob/master/Documentation/ceph-advanced-configuration.md#custom-cephconf-settings
- https://documentation.suse.com/ses/7/html/ses-all/bp-troubleshooting-status.html

# Alert: Ceph mon c is low on available space 

```shell script
# Issue
  cluster:
    id:     ad42764d-aa28-4da5-a828-2d87205aff08
    health: HEALTH_WARN
            mon c is low on available space

# Solution
ceph tell mon.`hostname -s` compact
OR
[mon]
 mon compact on start = true
```

Reference: 
- [Compacting the Monitor Store](https://access.redhat.com/documentation/en-us/red_hat_ceph_storage/3/html/troubleshooting_guide/troubleshooting-monitors)
- [Rook Ceph cluster : mon c is low on available space message](https://stackoverflow.com/questions/63602827/rookio-ceph-cluster-mon-c-is-low-on-available-space-message)

# Rook Ceph Skipping Device

```shell script
# Issue
cephosd: skipping device "nvme1n1p1" because it contains a filesystem "ext4"

2021-04-01 06:30:51.248754 D | exec: Running command: lsblk /dev/nvme0n1 --bytes --nodeps --pairs --paths --output SIZE,ROTA,RO,TYPE,PKNAME,NAME,KNAME
2021-04-01 06:30:51.249897 D | exec: Running command: sgdisk --print /dev/nvme0n1
2021-04-01 06:30:51.291992 W | inventory: skipping device "nvme0n1". exit status 2

# Cause
root@k8s-dev21:/home/ubuntu# sgdisk --print /dev/nvme1n1
Caution: invalid main GPT header, but valid backup; regenerating main header
from backup!
 
 
Caution! After loading partitions, the CRC doesn't check out!
Warning! Main partition table CRC mismatch! Loaded backup partition table
instead of main partition table!
 
Warning! One or more CRCs don't match. You should repair the disk!
 
Invalid partition data!

# Solution
sgdisk --print /dev/nvme1n1
sgdisk --zap-all /dev/nvme1n1
sgdisk --print /dev/nvme1n1
```

# Rook keeps crashing

References:
- https://github.com/rook/rook/issues/4274
- https://github.com/kubernetes/kubernetes/issues/99303
- https://github.com/rook/rook/issues/4427
- https://github.com/kubernetes-csi/node-driver-registrar/pull/61

The rook-ceph-csi-config cm disappeared
- https://github.com/rook/rook/issues/6162
- https://github.com/kubernetes/kubernetes/issues/88097
- https://github.com/kubernetes/kubernetes/issues/74340

Solution:
```shell script
# CronJob
*/30 * * * * /usr/local/k8s/backup/scripts/rook_health.sh >> /usr/local/rook_health.txt

#!/bin/bash
echo "$(date)"
csi_config_count="$(kubectl -n rook-ceph get cm rook-ceph-csi-config | wc -l)"
csi_count="$(kubectl -n rook-ceph get po | grep csi | wc -l)"
if [ $csi_count -eq 288 ] && [ $csi_config_count -eq 2 ]
then
  echo "Ceph Cluster is healthy"
else
  echo "Ceph Cluster is not healthy"
  rook_operator="$(kubectl -n rook-ceph get pod -l "app=rook-ceph-operator" -o jsonpath='{.items[0].metadata.name}')"
  echo "kubectl -n rook-ceph delete po $rook_operator"
  kubectl -n rook-ceph delete po $rook_operator
fi
```

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

# CSI pods intermittently disappear

> The CSI driver was disappearing in some clusters after certain events such as restarting the operator or master nodes being restarted. This is due to an incorrect owner reference on the csi driver back to the configmap. The owner reference must specify apps/v1 instead of v1 for the APIVersion of the configmap. This issue is only hit when the k8s garbage collector rebuilds its cache, which would happen at indeterminate times from Rook's perspective.

References:
- https://github.com/rook/rook/issues/4590
- https://github.com/rook/rook/pull/4788

# Helm linting issues with CRDs
- https://github.com/helm/helm/issues/8596
- https://github.com/helm/helm/issues/8642
- https://github.com/helm/helm/pull/8608

# ReadOnlyMany Access Mode
- Kubernetes issue -> https://github.com/kubernetes/community/issues/2539 leads to 
- Kubernetes issue -> https://github.com/kubernetes/kubernetes/issues/63183 leads to 
- Kubernetes issue -> https://github.com/kubernetes/kubernetes/issues/85803 leads to 
- Rook issue -> https://github.com/rook/rook/issues/4402 leads to 
- Ceph issue -> https://github.com/ceph/ceph-csi/issues/725 leads to 
- Ceph PR -> https://github.com/ceph/ceph-csi/pull/949 merged on 22 Jun
- It concludes, "ROX is only supported for restore and cloned PVC, not for a New(Fresh) PVC"