# Installation

```shell script
# DEV
+ helm upgrade --install --atomic --timeout 5m0s --namespace rook-ceph --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-operator/values/dev.yaml rook-ceph-operator devops-helm-repository/rook-ceph-operator
Release "rook-ceph-operator" has been upgraded. Happy Helming!

# PROD
+ helm upgrade --install --atomic --timeout 5m0s --namespace rook-ceph --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-operator/values/prod.yaml rook-ceph-operator devops-helm-repository/rook-ceph-operator
Release "rook-ceph-operator" has been upgraded. Happy Helming!
```

## Kubeval 

```shell script
# DEV Validation

+ Setting KUBEVAL_SCHEMA_LOCATION
+ export 'KUBEVAL_SCHEMA_LOCATION=/usr/local/kubeval/schemas'
+ Validating Helm Chart for DEV
+ helm template /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-operator/helm --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-operator/values/dev.yaml
+ kubeval --kubernetes-version 1.18.1 --strict --force-color --ignore-missing-schemas /usr/local/kubeval/schemas/dev.yaml
WARN - Set to ignore missing schemas
[32mPASS[0m - rook-ceph-operator/templates/psp.yaml contains a valid PodSecurityPolicy (00-rook-ceph-operator)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-system)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-cephfs-plugin-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-cephfs-provisioner-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-rbd-plugin-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-rbd-provisioner-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-admission-controller)
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephclusters.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephfilesystems.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephnfses.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectstores.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectstoreusers.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectrealms.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectzonegroups.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectzones.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephblockpools.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (volumes.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (objectbuckets.objectbucket.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (objectbucketclaims.objectbucket.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephclients.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephrbdmirrors.ceph.rook.io) was not validated against a schema
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-cluster-mgmt)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-global)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-mgr-cluster)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-mgr-system)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-object-bucket)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-agent-mount)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (cephfs-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (cephfs-external-provisioner-runner)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rbd-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rbd-external-provisioner-runner)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (psp:rook)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-admission-controller-role)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-global)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-mgr-cluster)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-object-bucket)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (cephfs-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (cephfs-csi-provisioner-role)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rbd-csi-provisioner-role)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-system-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-system-psp-users)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-cephfs-provisioner-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-cephfs-plugin-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-rbd-plugin-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-rbd-provisioner-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rbd-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-admission-controller-rolebinding)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-system)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.cephfs-external-provisioner-cfg)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rbd-external-provisioner-cfg)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-default-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-osd-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-cmd-reporter-psp)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-system)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-cluster-mgmt)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr-system)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.cephfs-csi-provisioner-role-cfg)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rbd-csi-provisioner-role-cfg)
[32mPASS[0m - rook-ceph-operator/templates/deployment.yaml contains a valid Deployment (rook-ceph-operator)
```

```shell script
# PROD Validation

+ Setting KUBEVAL_SCHEMA_LOCATION
+ export 'KUBEVAL_SCHEMA_LOCATION=/usr/local/kubeval/schemas'
+ Validating Helm Chart for PROD
+ helm template /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-operator/helm --values /home/jenkins/agent/workspace/K8s-OnPrem/rook-ceph-operator/values/prod.yaml
+ kubeval --kubernetes-version 1.18.1 --strict --force-color --ignore-missing-schemas /usr/local/kubeval/schemas/prod.yaml
WARN - Set to ignore missing schemas
[32mPASS[0m - rook-ceph-operator/templates/psp.yaml contains a valid PodSecurityPolicy (00-rook-ceph-operator)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-system)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-cephfs-plugin-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-cephfs-provisioner-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-rbd-plugin-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-csi-rbd-provisioner-sa)
[32mPASS[0m - rook-ceph-operator/templates/serviceaccount.yaml contains a valid ServiceAccount (jenkins.rook-ceph-admission-controller)
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephclusters.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephfilesystems.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephnfses.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectstores.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectstoreusers.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectrealms.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectzonegroups.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephobjectzones.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephblockpools.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (volumes.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (objectbuckets.objectbucket.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (objectbucketclaims.objectbucket.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephclients.ceph.rook.io) was not validated against a schema
[33mWARN[0m - rook-ceph-operator/templates/resources.yaml containing a CustomResourceDefinition (cephrbdmirrors.ceph.rook.io) was not validated against a schema
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-cluster-mgmt)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-global)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-mgr-cluster)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-mgr-system)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-object-bucket)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-agent-mount)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (cephfs-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (cephfs-external-provisioner-runner)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rbd-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rbd-external-provisioner-runner)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (psp:rook)
[32mPASS[0m - rook-ceph-operator/templates/clusterrole.yaml contains a valid ClusterRole (rook-ceph-admission-controller-role)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-global)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-mgr-cluster)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-object-bucket)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (cephfs-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (cephfs-csi-provisioner-role)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rbd-csi-provisioner-role)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-system-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-system-psp-users)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-cephfs-provisioner-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-cephfs-plugin-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-rbd-plugin-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-csi-rbd-provisioner-sa-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rbd-csi-nodeplugin)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid ClusterRoleBinding (rook-ceph-admission-controller-rolebinding)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-system)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.cephfs-external-provisioner-cfg)
[32mPASS[0m - rook-ceph-operator/templates/role.yaml contains a valid Role (jenkins.rbd-external-provisioner-cfg)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-default-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-osd-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr-psp)
[32mPASS[0m - rook-ceph-operator/templates/clusterrolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-cmd-reporter-psp)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-system)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-cluster-mgmt)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-osd)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-mgr-system)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rook-ceph-cmd-reporter)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.cephfs-csi-provisioner-role-cfg)
[32mPASS[0m - rook-ceph-operator/templates/rolebinding.yaml contains a valid RoleBinding (jenkins.rbd-csi-provisioner-role-cfg)
[32mPASS[0m - rook-ceph-operator/templates/deployment.yaml contains a valid Deployment (rook-ceph-operator)
```