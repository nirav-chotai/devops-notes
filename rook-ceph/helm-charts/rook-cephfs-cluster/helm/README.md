# Rook Ceph Cluster Helm Chart

Installs Ceph cluster filesystem on Kubernetes 

## Chart Details

This chart will do the following:

- Creates cluster
- Install toolbox
- Configure storage class

## Installing the Chart
To install the chart with the release name `my-release`:
```
$ helm install --name my-release stable/rook-ceph-cluster
```

## Configuration
Configurable values are documented in the `values.yaml`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. 

For example,
```
$ helm install --name my-release -f values.yaml stable/rook-ceph-cluster
```

> **Tip**: You can use the default [values.yaml](values.yaml)

