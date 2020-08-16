# cert-manager
## Install
1. Install cert-manager into the cluster using Helm.
```
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v0.16.1 \
  --set installCRDs=true
```

## Configure
2. Configure at least one issuer using the included templates.  See below for two examples - ACME and Private CA.

### ACME
The Let's Encrypt example is configured for DNS01 validation against Cloudflare using a Cloudflare API Token.

**Note:** The ACME example provided here uses an `Issuer`, which is a namespaced entity - meaning it can only fulfill orders for `Certificates` in the same namespace.  The secrets referenced by an `Issuer` must be stored in the same namespace as the `Issuer`.  A `ClusterIssuer`, however, is not bound to a particular namespace and can fulfill `Certificate` orders across any namespace.  The secrets referenced by a `ClusterIssuer` must be stored in the `cert-manager` namespace.

1. Edit the `01-letsencrypt-issuer.yaml` file and modify the `email` fields accordingly.
2. Edit the `01-cloudflare-secret.yaml` file and set the `api-token` field to your Cloudflare API token in base64-encoded format.
3. `kubectl apply` both YAML files into the cluster.  Use the `-n <namespace>` flag to ensure both resources are placed in the correct namespace.

### Private CA
This example configures an `Issuer` to fulfill `Certificate` orders using a private CA.

**Note:** This option requires adding your CA's public/private key pair to a Kubernetes TLS `Secret`.  One major caveat here is that if you use a `ClusterIssuer` as described above, anyone with access to your cluster can generate a valid certificate using your CA.

**Recommendations**
1. Only use a `ClusterIssuer` if absolutely necessary.  Having to configure an `Issuer` in multiple namespaces is a small price to pay for greater security and tighter controls around obtaining a signed `Certificate` from your CA.
2. Consider obtaining a dedicated intermediate CA or private root CA solely for your `Issuer`.  If an unauthorized service is allowed to request a `Certificate` - or even if the `Issuer` secret falls into the wrong hands - your CA should be considered compromised.

## Request Certificates
Use the included `Certificate` YAML templates to request a `Certificate` from the `Issuer`.

Because an ACME request typically has a fixed duration and specific options, there are very few parameters to configure.

Private CA requests are more flexible, and thus have more options available for key size, key algorithm, duration, encoding, and other fields.