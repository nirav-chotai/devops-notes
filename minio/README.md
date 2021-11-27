# MinIO on Kubernetes

> MinIO is a Kubernetes-native high performance object store with an S3-compatible API. The MinIO Kubernetes Operator supports deploying MinIO Tenants onto private and public cloud infrastructures (“Hybrid” Cloud).

> MinIO helm chart is being deprecated and its project will cease to take new updates from April 2021. All users are advised to migrate to MinIO operator. MinIO operator supports helm chart based installation.

## MinIO Operator

Architecture:


Prerequisites:
- Kubernetes 1.19 or Later
- MinIO Operator Namespace
- MinIO Tenant Namespace
- MinIO Tenant Storage Class
- MinIO Tenant Persistent Volumes 