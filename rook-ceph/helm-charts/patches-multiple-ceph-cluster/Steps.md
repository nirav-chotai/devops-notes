# Be Careful

## After installing new Ceph Cluster for MinIO, perform below step

```yaml
kubectl apply -f patch/clusterrolebindings-minio.yaml
```

## After removing new Ceph Cluster for MinIO, perform below step

```yaml
kubectl apply -f patch/clusterrolebindings-org.yaml
``` 

## Annotate and Label resources else helm will fail for original rook operator

```yaml
kubectl annotate ClusterRoleBinding rook-ceph-global meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-ceph-mgr-cluster meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-ceph-osd meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-ceph-object-bucket meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding cephfs-csi-nodeplugin meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding cephfs-csi-provisioner-role meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rbd-csi-provisioner-role meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-ceph-system-psp meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-ceph-system-psp-users meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-csi-cephfs-provisioner-sa-psp meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-csi-cephfs-plugin-sa-psp meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-csi-rbd-plugin-sa-psp meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-csi-rbd-provisioner-sa-psp meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rbd-csi-nodeplugin meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl annotate ClusterRoleBinding rook-ceph-admission-controller-rolebinding meta.helm.sh/release-name=rook-ceph-operator meta.helm.sh/release-namespace=rook-ceph --overwrite
kubectl label ClusterRoleBinding rook-ceph-global app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-ceph-mgr-cluster app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-ceph-osd app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-ceph-object-bucket app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding cephfs-csi-nodeplugin app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding cephfs-csi-provisioner-role app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rbd-csi-provisioner-role app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-ceph-system-psp app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-ceph-system-psp-users app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-csi-cephfs-provisioner-sa-psp app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-csi-cephfs-plugin-sa-psp app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-csi-rbd-plugin-sa-psp app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-csi-rbd-provisioner-sa-psp app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rbd-csi-nodeplugin app.kubernetes.io/managed-by=Helm --overwrite
kubectl label ClusterRoleBinding rook-ceph-admission-controller-rolebinding app.kubernetes.io/managed-by=Helm --overwrite
```
