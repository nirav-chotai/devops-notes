# Important Notes:
- You must customise the `CephCluster` resource to met your situation.
- Each `CephCluster` must be deployed to its own `namespace`.
- The helm chart includes all the RBAC required to create a `CephCluster` CRD in the same namespace.
- Any disk devices you add to the cluster in the `CephCluster` must be empty (no filesystem and no partitions).
- In the `CephCluster` you must refer to disk devices by their `/dev/something` name, e.g. 'sdb' or 'xvde' or 'nvme'.

# Installation

```shell script
# DEV
+ helm upgrade --install --atomic --timeout 5m0s --namespace rook-ceph --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-cluster/values/dev.yaml rook-ceph-cluster devops-helm-repository/rook-ceph-cluster
Release "rook-ceph-cluster" has been upgraded. Happy Helming!

# PROD
+ helm upgrade --install --atomic --timeout 5m0s --namespace rook-ceph --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-cluster/values/prod.yaml rook-ceph-cluster devops-helm-repository/rook-ceph-cluster
Release "rook-ceph-cluster" has been upgraded. Happy Helming!
```

## Kubeval

```shell script
# DEV Validation

+ Setting KUBEVAL_SCHEMA_LOCATION
+ export 'KUBEVAL_SCHEMA_LOCATION=/usr/local/kubeval/schemas'
+ Validating Helm Chart for DEV
+ helm template /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-cluster/helm --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-cluster/values/dev.yaml
+ kubeval --kubernetes-version 1.18.1 --strict --force-color --ignore-missing-schemas /usr/local/kubeval/schemas/dev.yaml
WARN - Set to ignore missing schemas
[32mPASS[0m - rook-ceph-cluster/templates/storageclass.yaml contains a valid StorageClass (rook-ceph-block)
[32mPASS[0m - rook-ceph-cluster/templates/storageclass.yaml contains a valid StorageClass (rook-cephfs)
[32mPASS[0m - rook-ceph-cluster/templates/toolbox.yaml contains a valid Deployment (jenkins.rook-ceph-tools)
[33mWARN[0m - rook-ceph-cluster/templates/storageclass.yaml containing a CephBlockPool (jenkins.replicapool) was not validated against a schema
[33mWARN[0m - rook-ceph-cluster/templates/cluster.yaml containing a CephCluster (jenkins.rook-ceph) was not validated against a schema
[33mWARN[0m - rook-ceph-cluster/templates/filesystem.yaml containing a CephFilesystem (rook-ceph.myfs) was not validated against a schema
```

```shell script
# PROD Validation

+ Setting KUBEVAL_SCHEMA_LOCATION
+ export 'KUBEVAL_SCHEMA_LOCATION=/usr/local/kubeval/schemas'
+ Validating Helm Chart for PROD
+ helm template /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-cluster/helm --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-cluster/values/prod.yaml
+ kubeval --kubernetes-version 1.18.1 --strict --force-color --ignore-missing-schemas /usr/local/kubeval/schemas/prod.yaml
WARN - Set to ignore missing schemas
[32mPASS[0m - rook-ceph-cluster/templates/storageclass.yaml contains a valid StorageClass (rook-ceph-block)
[32mPASS[0m - rook-ceph-cluster/templates/toolbox.yaml contains a valid Deployment (jenkins.rook-ceph-tools)
[33mWARN[0m - rook-ceph-cluster/templates/storageclass.yaml containing a CephBlockPool (jenkins.replicapool) was not validated against a schema
[33mWARN[0m - rook-ceph-cluster/templates/cluster.yaml containing a CephCluster (jenkins.rook-ceph) was not validated against a schema
```

# Note
- DEV has CephRBD and CephFS in the single namespace `rook-ceph`
- PROD has CephRBD in `rook-ceph` namespace, while CephFS in `rook-cephfs` namespace