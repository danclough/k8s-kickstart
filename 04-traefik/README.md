# Traefik
## Install
The instructions provided below assume that your Kubernetes nodes have extra hard drives available at a path like `/dev/sdb`.  Present more disks to your VM or physical host if needed.  If no free disks are found, the cluster will still be created but will not have any storage available.

1. Add the official Traefik Helm repo to your local Helm installation.
```
helm repo add traefik https://containous.github.io/traefik-helm-chart
helm repo update
```

2. Install Traefik with the included `traefik-values.yaml` file to customize installation settings.

I populated this file to do the following:
* Run two instances of Traefik for redundancy
* Always keep two instances available
* Do not automatically add an IngressRoute for the Traefik dashboard - we will do that later

```
helm install traefik traefik/traefik --values=traefik-values.yaml
```

## Configure
3. Modify the `traefik-dashboard.yaml` file and set `Host(traefik-dashboard.local)` to the URL you will use to access the Traefik dashboard.  The dashboard will only be accessible via the URL configured here!

4. If you want to set up TLS for the dashboard, modify the `traefik-dashboard-tls.yaml` file and set `tls.crt` and `tls.key` to base64-encoded values for your TLS certificate and private key.  Then uncomment the TLS stanza in `traefik-dashboard.yaml`.

5. Apply the IngressRoute for the Traefik dashboard, and if desired, the TLS certificate as well.
```
kubectl apply -f traefik-dashboard.yaml
kubectl apply -f traefik-dashboard-tls.yaml
```

## Next Step
Continue to [Step 5 - cert-manager](../05-cert-manager/)