# Important Notes:
- You must customise the `CephCluster` resource to met your situation.
- Each `CephCluster` must be deployed to its own `namespace`.
- The helm chart includes all the RBAC required to create a `CephCluster` CRD in the same namespace.
- Any disk devices you add to the cluster in the `CephCluster` must be empty (no filesystem and no partitions).
- In the `CephCluster` you must refer to disk devices by their `/dev/something` name, e.g. 'sdb' or 'xvde' or 'nvme'.

# Installation

```shell script
# PROD
+ helm upgrade --install --atomic --timeout 15m --namespace rook-cephfs --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-cephfs-cluster/values/prod.yaml rook-cephfs-cluster devops-helm-repository/rook-cephfs-cluster
Release "rook-cephfs-cluster" has been upgraded. Happy Helming!
```

## Kubeval

```shell script
# PROD Validation

+ Setting KUBEVAL_SCHEMA_LOCATION
+ export 'KUBEVAL_SCHEMA_LOCATION=/usr/local/kubeval/schemas'
+ Validating Helm Chart for PROD
+ helm template /home/jenkins/agent/workspace/K8s-OnPrem/rook-cephfs-cluster/helm --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-cephfs-cluster/values/prod.yaml
+ kubeval --kubernetes-version 1.18.1 --strict --force-color --ignore-missing-schemas /usr/local/kubeval/schemas/prod.yaml
WARN - Set to ignore missing schemas
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-cephfs-plugin-sa)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-cephfs-provisioner-sa)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-rbd-plugin-sa)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-rbd-provisioner-sa)
[32mPASS[0m - rook-cephfs-cluster/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-admission-controller)
[32mPASS[0m - rook-cephfs-cluster/templates/storageclass.yaml contains a valid StorageClass (rook-cephfs)
[32mPASS[0m - rook-cephfs-cluster/templates/role.yaml contains a valid Role (jenkins.rook-ceph-system)
[32mPASS[0m - rook-cephfs-cluster/templates/role.yaml contains a valid Role (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-cephfs-cluster/templates/role.yaml contains a valid Role (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-cephfs-cluster/templates/role.yaml contains a valid Role (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-cephfs-cluster/templates/role.yaml contains a valid Role (jenkins.cephfs-external-provisioner-cfg)
[32mPASS[0m - rook-cephfs-cluster/templates/role.yaml contains a valid Role (jenkins.rbd-external-provisioner-cfg)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-default-psp)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-osd-psp)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr-psp)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-cmd-reporter-psp)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-system)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-cluster-mgmt)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr-system)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.cephfs-csi-provisioner-role-cfg)
[32mPASS[0m - rook-cephfs-cluster/templates/rolebindings.yaml contains a valid RoleBinding (jenkins.rbd-csi-provisioner-role-cfg)
[32mPASS[0m - rook-cephfs-cluster/templates/toolbox.yaml contains a valid Deployment (jenkins.rook-ceph-tools)
[33mWARN[0m - rook-cephfs-cluster/templates/cluster.yaml containing a CephCluster (jenkins.rook-cephfs) was not validated against a schema
[33mWARN[0m - rook-cephfs-cluster/templates/filesystem.yaml containing a CephFilesystem (rook-cephfs.myfs) was not validated against a schema
```

# Note
- DEV has CephRBD and CephFS in the single namespace `rook-ceph`
- PROD has CephRBD in `rook-ceph` namespace, while CephFS in `rook-cephfs` namespace
