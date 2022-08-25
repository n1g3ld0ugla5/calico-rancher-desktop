# calico-rancher-desktop
This lab is for running Calico OSS locally

```
cd desktop
```

```
mkdir rancher-desktop
```

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-darwin-amd64
```

```
chmod +x ./kind
```

```
vi kind-calico.yaml
```

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  disableDefaultCNI: true # disable kindnet
  podSubnet: 192.168.0.0/16 # set to Calico's default subnet
```

```  
kind create cluster --config kind-calico.yaml
```

Once the cluster is up, list the pods in the kube-system namespace to verify that kindnet is not running:

```
kubectl get pods -n kube-system
```
