# Bootstrapping your cluster

Provision 3 nodes on Debian 9.

**Note:** There is a known issue with Debian 10 ("buster") where pods are unable to communicate with each other across nodes.  This is due to an incompatability with kube-proxy and Debian's decision to make iptables a wrapper for nftables.  This is *supposedly* accounted for in K8s versions 1.18 and up, but I still have issues with Debian 10 as of 1.19.

## Control Plane

### Initialize Cluster
Use kubeadm to provision the first control plane node.  Replace the CIDR ranges, cluster_name and load_balancer with actual values.
```
sudo kubeadm init --pod-network-cidr=10.x.0.0/16 --pod-network-cidr=10.y.0.0/16 --service-dns-domain "<cluster_name>.local" --control-plane-endpoint "<load_balancer>:6443" --upload-certs
```

Save the `kubeadm join` commands somewhere safe so they can be used later to join the other control plane nodes and any worker nodes.

#### Gather kubectl config
Run these steps on the first control plane node to setup kubectl for your non-root user.
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

Copy the `.kube/config` file to your local workstation so you can run kubectl commands locally against the files in this repo.

#### Install Calico networking subsystem
From the `01-calico` directory, install the Tigera Calico operator and CRDs.
```
kubectl apply -f 01-calico/00-tigera-operator.yaml
```

If needed, modify the `01-custom-resources.yaml` to change the `blockSize` (size of pod IP range assigned to each node) and `cidr` (not necessary if you passed `--pod-network-cidr` in the `kubeadm init` command).  Apply the Calico config.
```
kubectl apply -f 01-calico/01-custom-resources.yaml
```

### Join Additional Control Plane Nodes
Use the previous `kubeadm join` command to join the remaining control plane nodes to the cluster.  The appropriate command to use is the one that contains the `--control-plane` and `--certificate-key` flags.

### Untaint Control Plane Nodes
If your control plane nodes will run pods, untaint them now.
```
kubectl taint nodes --all node-role.kubernetes.io/master-
```

## Worker Nodes
For any other nodes not intended to be control plane nodes, use the other `kubeadm join` command that does not contain the `--control-plane` flag.