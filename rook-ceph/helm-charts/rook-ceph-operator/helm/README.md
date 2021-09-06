# Ceph Operator Helm Chart

Installs rook to create, configure, and manage Ceph clusters on Kubernetes.

This chart bootstraps a rook-ceph-operator deployment on a Kubernetes cluster using the Helm package manager. 

## Chart Details

The Ceph Operator helm chart will install the basic components necessary to create a storage platform for your Kubernetes cluster. 

After the helm chart is installed, you will need to create a Rook cluster.

## Installing the Chart

To install the chart with the release name `my-release`:
```
$ helm install --name my-release stable/rook-ceph-operator
```

## Configuration
Configurable values are documented in the `values.yaml`

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. 

For example,
```
$ helm install --name my-release -f values.yaml stable/rook-ceph-operator
```

> **Tip**: You can use the default [values.yaml](values.yaml)

See the [Operator Helm Chart](/Documentation/helm-operator.md) documentation.
