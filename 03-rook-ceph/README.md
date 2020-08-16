# Rook Ceph Cluster - Cloud-native Storage
## Install
The instructions provided below assume that your Kubernetes nodes have extra hard drives available at a path like `/dev/sdb`.  Present more disks to your VM or physical host if needed.  If no free disks are found, the cluster will still be created but will not have any storage available.

1. Install common resources and the Rook operator using the latest manifests.  Replace `master` with a specific version tag if desired.
```
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/common.yaml
kubectl apply -f https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml
```

## Configure
2. Modify the included `cluster.yaml` file.  In the `storage` section, adjust the `deviceFilter` if needed to include the devices that Rook should use for Ceph storage.
```
  storage: # cluster level storage configuration and selection
    useAllNodes: true
    useAllDevices: false
    deviceFilter: ^sd.
```

3. Create the Rook Ceph cluster using `kubectl apply -f cluster.yaml` and watch as Rook creates new pods on each node to detect and prepare the storage devices as Ceph OSDs.

**Note:** Rook will not format a drive for Ceph if it finds an existing partition or filesystem.  Make sure any disks you want to use are blank.

## Next Step
Continue to [Step 4 - Traefik](../04-traefik/)