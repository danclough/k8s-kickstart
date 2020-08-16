# Rook Ceph Cluster - Cloud-native Storage
## Install
The instructions provided below will only create a bare minimum MetalLB deployment using Layer 2 mechanisms to announce IPs.  Complete installation instructions can be found in the [MetalLB installation docs](https://metallb.universe.tf/installation/)

1. Install MetalLB using the latest manifests.  Replace `main` with a specific version tag if desired.
```
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/main/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/main/manifests/metallb.yaml
# On first install only
kubectl create secret generic -n metallb-system memberlist --from-literal=secretkey="$(openssl rand -base64 128)"
```

## Configure
2. Modify the included `config.yaml` file.  Set the `ip_range_begin` and `ip_range_end` according to the range of IPs MetalLB should assign from.
```
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - <ip_range_begin>-<ip_range-end>
```

## Next Step
Continue to [Step 3 - Rook Ceph Storage](../03-rook-ceph/)