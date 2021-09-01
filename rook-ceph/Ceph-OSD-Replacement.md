# How to add/remove/replace OSDs in K8s Ceph Cluster

> Ceph OSDs (min 3): Object Storage Daemon, stores the data, maintain replication, recovery, rebalancing

> BlueStore is the engine used by the OSD to store data.

## Add OSD

To add more OSDs, Rook will automatically watch for new nodes and devices being added to the Ceph cluster. If they match the filters or other settings in the `storage` section of the cluster CR, the operator will create new OSDs.

For more details, check out 
- [Node Settings](https://rook.io/docs/rook/v1.7/ceph-cluster-crd.html#node-settings)
- [Storage Selection Settings](https://rook.io/docs/rook/v1.7/ceph-cluster-crd.html#storage-selection-settings)
- [OSD Configuration Settings](https://rook.io/docs/rook/v1.7/ceph-cluster-crd.html#osd-configuration-settings)

Example:
```shell script
  storage:
    useAllNodes: {{ .Values.storage.useAllNodes }}
    useAllDevices: {{ .Values.storage.useAllDevices }}
    {{- if eq .Values.storage.env "dev" }}
    nodes:
      - name: "k8s-dev05"
        devices:
          - name: "nvme0n1"
      - name: "k8s-dev06"
        devices:
          - name: "nvme1n1"
      - name: "k8s-dev07"
        devices:
          - name: "nvme1n1"
    {{- end }}
    {{- if eq .Values.storage.env "prod" }}
    nodes:
    - name: "k8s-prod-13"
      devices:
      - name: "sdb"
      - name: "sdd"
    - name: "k8s-prod-14"
      devices:
      - name: "sdd"
      - name: "sde"
    - name: "k8s-prod-16"
      devices:
      - name: "sdb"
      - name: "sdd"
    {{- end }}
```
`dev.yaml`
```shell script
storage:
  useAllNodes: false
  useAllDevices: false
  env: dev
```
`prod.yaml`
```shell script
storage:
  useAllNodes: false
  useAllDevices: false
  env: prod
```

## Remove OSD

The removal of OSDs is intentionally not automated. Rook’s charter is to keep your data safe, not to delete it. If you are sure you need to remove OSDs, it can be done. We just want you to be in control of this action.

- Confirm you will have enough space on your cluster after removing your OSDs to properly handle the deletion 
- Confirm the remaining OSDs and their placement groups (PGs) are healthy in order to handle the rebalancing of the data 
- Do not remove too many OSDs at once 
- Wait for a rebalancing between removing multiple OSDs
- If all the PGs are active+clean and there are no warnings about being low on space, this means the data is fully replicated and it is safe to proceed.
- All pods should be up and running, not in `CrashLoopBackoff` or any other state

> IMPORTANT: All of the below steps should be performed for each OSDs to be removed. After performing each step for each OSDs, wait for Cluster to be HEALTHY again as given in the instructions.

Step 1: Determine the OSD ID for the OSD to be removed. For example, ID 246 should be removed.

```shell script
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph osd tree
ID   CLASS  WEIGHT      TYPE NAME                     STATUS  REWEIGHT  PRI-AFF
...
246    hdd    12.73340          osd.246                   up   1.00000  1.00000
...
```

Step 2: Mark the OSD `out`

```shell script
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph osd out osd.246
...
 
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph status
  cluster:
    id:     c40d82d5-3193-457d-a628-a3db67839a37
    health: HEALTH_OK
  ...
  data:       
    ...
    pgs:     33 active+clean
  ...
```
- This signals Ceph to start moving (backfilling) the data that was on that OSD to another OSD.
- `ceph osd out osd.<ID>` (for example, if the OSD ID is 246 this would be `ceph osd out osd.246`)
- Wait for the data to finish backfilling to other OSDs.
- `ceph status` will indicate the backfilling is done when all of the PGs are `active+clean`. If desired, it’s safe to remove the disk after that.
- Ceph health must be `HEALTH_OK` and all pgs must be `active+clean`

Step 3: Update your CephCluster CR such that the operator won’t create an OSD on the device anymore.

```shell script
(base) ➜  ~ kubectl get cephcluster -n rook-ceph
NAME        DATADIRHOSTPATH   MONCOUNT   AGE    PHASE   MESSAGE                        HEALTH
rook-ceph   /var/lib/rook     3          478d   Ready   Cluster created successfully   HEALTH_OK
(base) ➜  ~ kubectl get cephcluster -n rook-cephfs
NAME          DATADIRHOSTPATH        MONCOUNT   AGE    PHASE   MESSAGE                        HEALTH
rook-cephfs   /var/lib/rook-cephfs   3          267d   Ready   Cluster created successfully   HEALTH_OK
(base) ➜  ~ kubectl edit cephcluster -n rook-ceph
(base) ➜  ~ kubectl edit cephcluster -n rook-cephfs
```

Step 4: Delete or Update specific Kubernetes Objects for the OSD. 

`Deployments`

Delete the specific OSD Deployment
```shell script
kubectl -n rook-ceph delete deploy rook-ceph-osd-<ID>
# Example: kubectl -n rook-ceph delete deploy rook-ceph-osd-246
```
If removing an entire node, then delete crashcollector deployment
```shell script
kubectl -n rook-ceph delete deploy rook-ceph-crashcollector-<NODE>
# Example: kubectl -n rook-ceph delete deploy rook-ceph-crashcollector-k8s-prod-06
```

`Secrets`

Delete the specific OSD Secret
```shell script
kubectl -n rook-ceph delete secret rook-ceph-osd-<ID>-keyring
# Example: kubectl -n rook-ceph delete secret rook-ceph-osd-246-keyring
```

`ConfigMaps`

If removing an entire node, then ConfigMap should be deleted
```shell script
kubectl -n rook-ceph delete cm local-device-<NODE>
# Example: kubectl -n rook-ceph delete cm local-device-k8s-prod-04
```
If removing an OSD only from the node, then ConfigMaps should be updated.
```shell script
# If removing an OSD only from the node, then first identify the OSD details from the host
# Connect to the host, and running command like `lsblk`, gives more information about OSD device
kubectl -n rook-ceph edit cm local-device-<NODE>
# Example: kubectl -n rook-ceph edit cm local-device-k8s-prod-04
# Remove an entry of the OSD which is being removed.
```

`Jobs`

If removing an entire node, then Job should be deleted
```shell script
kubectl -n rook-ceph delete job rook-ceph-osd-prepare-<NODE>
# Example: kubectl -n rook-ceph delete job rook-ceph-osd-prepare-k8s-prod-04
```

Step 5: Wait for OSD status to change it to down, then delete `auth`

```shell script
# Before
 
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph osd tree
 
ID  CLASS  WEIGHT    TYPE NAME                     STATUS  REWEIGHT  PRI-AFF
...
 1    hdd  12.73340          osd.246                     up   1.00000  1.00000
 
# After
 
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph osd tree
 
ID  CLASS  WEIGHT    TYPE NAME                     STATUS  REWEIGHT  PRI-AFF
...
 1    hdd  12.73340          osd.246                     down   0  1.00000
```
Delete `auth` from the `toolbox`
```shell script
ceph auth list
ceph auth del osd.246
```

Step 6: Remove the OSD from the Ceph cluster

```shell script
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph osd purge 246 --yes-i-really-mean-it
...
 
# Verify the OSD is removed from the node in the CRUSH map
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph osd tree
...
 
# Check the Ceph Status
[root@rook-ceph-tools-75b55dbcb9-k7tmm /]# ceph status
  cluster:
    id:     c40d82d5-3193-457d-a628-a3db67839a37
    health: HEALTH_OK
  ...
  data:       
    ...
    pgs:     33 active+clean
  ...
```

Step 7: Wait for Ceph Cluster to be healthy again
```shell script
ceph status
ceph osd tree
ceph osd status
ceph health detail
```

Step 8: Delete the underlying data

> IMPORTANT: The final cleanup step requires deleting files on each host in the cluster. All files under the dataDirHostPath property specified in the cluster CRD will need to be deleted. Otherwise, inconsistent state will remain when a new cluster is started.

Identify the disk and run below script for each disk cleanup:
```shell script
#!/usr/bin/env bash
DISK="/dev/sdb"
# Zap the disk to a fresh, usable state (zap-all is important, b/c MBR has to be clean)
# You will have to run this step for all disks.
sgdisk --zap-all $DISK
# Clean hdds with dd
dd if=/dev/zero of="$DISK" bs=1M count=100 oflag=direct,dsync
# Clean disks such as ssd with blkdiscard instead of dd
# blkdiscard $DISK
```

If removing only an OSD from the node, then execute the following:
```shell script
# Identify the mapper from `lsblk` command
ls /dev/mapper/ceph--2950f769--797d--49c2--9681--fcc28bb4354f-osd--data--ceb3bed2--1531--43cf--a8d7--2c1e91b1e789 | xargs -I% -- dmsetup remove %
 
 
# Identify the directory from `ls /dev/ceph*` command
rm -rf /dev/ceph-2950f769-797d-49c2-9681-fcc28bb4354f
```

If removing an entire node, then execute the following:
```shell script
# These steps only have to be run once on each node
# If rook sets up osds using ceph-volume, teardown leaves some devices mapped that lock the disks.
ls /dev/mapper/ceph-* | xargs -I% -- dmsetup remove %
# ceph-volume setup can leave ceph-<UUID> directories in /dev (unnecessary clutter)
rm -rf /dev/ceph-*
```

Inform the OS of partition table changes
```shell script
partprobe $DISK
```

# Automation

OSD removal can be automated with below caveats:
- This job only works for `down` OSDs.
- This job doesn't wait for backfilling to be completed.

`osd-purge.yaml:`

```shell script
#################################################################################################################
# We need many operations to remove OSDs as written in Documentation/ceph-osd-mgmt.md.
# This job can automate some of that operations: mark OSDs as `out`, purge these OSDs,
# and delete the corresponding resources like OSD deployments, OSD prepare jobs, and PVCs.
#
# Please note the following.
#
# - This job only works for `down` OSDs.
# - This job doesn't wait for backfilling to be completed.
#
# If you want to remove `up` OSDs and/or want to wait for backfilling to be completed between each OSD removal,
# please do it by hand.
#################################################################################################################

apiVersion: batch/v1
kind: Job
metadata:
  name: rook-ceph-purge-osd
  namespace: rook-ceph # namespace:operator
  labels:
    app: rook-ceph-purge-osd
spec:
  template:
    spec:
      serviceAccountName: rook-ceph-purge-osd
      containers:
        - name: osd-removal
          image: rook/ceph:v1.7.2
          # TODO: Insert the OSD ID in the last parameter that is to be removed
          # The OSD IDs are a comma-separated list. For example: "0" or "0,2".
          # If you want to preserve the OSD PVCs, set `--preserve-pvc true`.
          args: ["ceph", "osd", "remove", "--preserve-pvc", "false", "--osd-ids", "<OSD-IDs>"]
          env:
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
            - name: ROOK_MON_ENDPOINTS
              valueFrom:
                configMapKeyRef:
                  key: data
                  name: rook-ceph-mon-endpoints
            - name: ROOK_CEPH_USERNAME
              valueFrom:
                secretKeyRef:
                  key: ceph-username
                  name: rook-ceph-mon
            - name: ROOK_CEPH_SECRET
              valueFrom:
                secretKeyRef:
                  key: ceph-secret
                  name: rook-ceph-mon
            - name: ROOK_CONFIG_DIR
              value: /var/lib/rook
            - name: ROOK_CEPH_CONFIG_OVERRIDE
              value: /etc/rook/config/override.conf
            - name: ROOK_FSID
              valueFrom:
                secretKeyRef:
                  key: fsid
                  name: rook-ceph-mon
            - name: ROOK_LOG_LEVEL
              value: DEBUG
          volumeMounts:
            - mountPath: /etc/ceph
              name: ceph-conf-emptydir
            - mountPath: /var/lib/rook
              name: rook-config
      volumes:
        - emptyDir: {}
          name: ceph-conf-emptydir
        - emptyDir: {}
          name: rook-config
      restartPolicy: Never
```

References: 
- https://rook.io/docs/rook/v1.7/ceph-osd-mgmt.html#purge-the-osd-from-the-ceph-cluster
- https://github.com/rook/rook/blob/release-1.7/cluster/examples/kubernetes/ceph/osd-purge.yaml