# Hashicorp Vault

Vault is a tool used to manage secrets. 

# Vault Architecture

![Vault Architecture](../imgs/vault-architecture.png)

# Vault Components

There are two main components: Server, Injector

## Vault Server

Vault Server is an application which will store the secrets and policies.

For High Availability, GCS can be used to persist Vault's data. 

Reference: [Google Cloud Storage Storage Backend](https://www.vaultproject.io/docs/configuration/storage/google-cloud-storage)

GCS Bucket can be created via Terraform or Ansible. Below is an example of Ansible.

```yaml
- name: "Creating a bucket for vault data"
  gcp_storage_bucket:
    name: "{{ gcp_bucket }}"
    project: "{{ gcp_project }}"
    auth_kind: "{{ gcp_cred_kind }}"
    service_account_file: "/tmp/{{ gcp_cred_file }}"
    state: present
```

Data inside GCS bucket will be encrypted using Google KMS Service. 
Hence, Vault server must have role `cloudkms.cryptoKeyEncrypterDecrypter`. 
Noted that due to this condition, a bucket and keyrings on KMS must be created before deploying Vault.

```yaml
- name: "Creating a KMS key ring"
  gcp_kms_key_ring:
    name: "{{ key_ring }}"
    location: "{{ gcp_location }}"
    project: "{{ gcp_project }}"
    auth_kind: "{{ gcp_cred_kind }}"
    service_account_file: "/tmp/{{ gcp_cred_file }}"
    state: present

- name: "Creating a crypto key for the key ring"
  gcp_kms_crypto_key:
    name: "{{ crypto_key }}"
    key_ring: "projects/{{ gcp_project }}/locations/{{ gcp_location }}/keyRings/{{ key_ring }}"
    project: "{{ gcp_project }}"
    auth_kind: "{{ gcp_cred_kind }}"
    service_account_file: "/tmp/{{ gcp_cred_file }}"
    state: present
```

Vault server will be deployed with a sidecar called vault-init. 
This sidecar also need access to configured bucket and will use KMS to decrypt data. 
Its responsibility is to initialise and unseal Vault pod whenever it is started. 
It is needed for High-Availability set up. 

References:
- [Auto-unseal using GCP Cloud KMS](https://learn.hashicorp.com/tutorials/vault/autounseal-gcp-kms)
- [The GCP Cloud KMS seal configurations](https://www.vaultproject.io/docs/configuration/seal/gcpckms)

## Vault Injector

This component has responsibility to inject vault-agent sidecars, which will pre-populate and update secrets as configured, to any pod whose required annotations (will be mentioned later). 

It use `MutatingWebhookConfiguration` to interfere pod creation. 

![vault-agent-injector](../imgs/vault-agent-injector.png)

Noted that Vault injector does not works with istio. In the helm chart, by default it will disable istio for injector.

GitHub Issue: [vault-k8s and istio service mesh don't work together](https://github.com/hashicorp/vault-k8s/issues/41)

# Vault Setup

The Vault Helm chart is the recommended way to install and configure Vault on Kubernetes. 

Helm Chart: https://github.com/hashicorp/vault-helm \
Instructions: https://www.vaultproject.io/docs/platform/k8s/helm

Sample `values.yaml`
```yaml
global:
  enabled: true
  imagePullSecret: gcr-json-key
  imagePullSecrets:
    - name: gcr-json-key

server:
  image:
    repository: 'asia.gcr.io/PROJECT_ID/devops/vault'
    tag: '1.4.0'

  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "8200"
    prometheus.io/path: "/v1/sys/metrics"

  extraEnvironmentVars:
    GOOGLE_REGION: REGION
    GOOGLE_PROJECT: PROJECT_ID
    GOOGLE_APPLICATION_CREDENTIALS: /vault/userconfig/prod-kms-vault-creds/prod-vault.json
    HTTP_PROXY: IF_ANY
    HTTPS_PROXY: IF_ANY

  extraVolumes:
    - type: 'secret'
      name: 'prod-kms-vault-creds'

  ha:
    enabled: true
    replicas: 3

    config: |
      ui = true

      listener "tcp" {
        tls_disable = 1
        address = "[::]:8200"
        cluster_address = "[::]:8201"
      }

      seal "gcpckms" {
        project     = "PROJECT_ID"
        region      = "REGION"
        key_ring    = "prod_vault_key_ring"
        crypto_key  = "prod_vault_crypto_key"
      }

      storage "gcs" {
        bucket = "prod-vault"
        ha_enabled    = "true"
      }

      telemetry {
        prometheus_retention_time = "5m"
        disable_hostname = true
      }

ui:
  enabled: true
  serviceType: NodePort
```
[Google KMS Auto Unseal](https://www.vaultproject.io/docs/platform/k8s/helm/run#google-kms-auto-unseal) is used to auto unseal the vault.

Helm Chart Configurations: https://www.vaultproject.io/docs/platform/k8s/helm/configuration

Auto-unseal does not initialize Vault. When you run `vault operator init`, root token will be found there.

```yaml
kubectl -n vault exec devops-vault-0 -- vault operator init -recovery-shares=1 -recovery-threshold=1

kubectl -n vault exec devops-vault-0 -- vault status

Key                      Value
---                      -----
Recovery Seal Type       shamir
Initialized              true
Sealed                   false
Total Recovery Shares    1
Threshold                1
Version                  1.4.0
Cluster Name             vault-cluster-fb97d7f5
Cluster ID               c235a780-c82b-49f6-9c1c-e4f93ccf69a9
HA Enabled               true
HA Cluster               https://devops-vault-0.devops-vault-internal:8201
HA Mode                  active

kubectl -n vault exec devops-vault-1 -- vault status

Key                      Value
---                      -----
Recovery Seal Type       shamir
Initialized              true
Sealed                   false
Total Recovery Shares    1
Threshold                1
Version                  1.4.0
Cluster Name             vault-cluster-fb97d7f5
Cluster ID               c235a780-c82b-49f6-9c1c-e4f93ccf69a9
HA Enabled               true
HA Cluster               https://devops-vault-1.devops-vault-internal:8201
HA Mode                  standby
Active Node Address      http://192.168.78.194:8200
```
In HA mode, 1 will be active, and others will be standby.

After Initialization, you'll get root token as shown below. Keep it safe. 
```shell script
Recovery Key 1: hmempMOTCWB2nFK2i1EixevS1P4TmRbhwKJYjeUeCSY=

Initial Root Token: s.SUVXmDNxVColi4kSBbTPm57A

Success! Vault is initialized

Recovery key initialized with 1 key shares and a key threshold of 1. Please
securely distribute the key shares printed above.
```

# Secrets Permissions



