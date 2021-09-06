# Be Careful

## After installing new Ceph Cluster for MinIO, perform below step

```yaml
kubectl apply -f patch/clusterrolebindings-minio.yaml
```

## After installing new Ceph FileSystem Cluster, perform below step

```yaml
kubectl apply -f patch/clusterrolebindings-cephfs.yaml
```

## After removing new Ceph Cluster for MinIO or CephFS, perform below step

```yaml
kubectl apply -f patch/clusterrolebindings-org.yaml
``` 