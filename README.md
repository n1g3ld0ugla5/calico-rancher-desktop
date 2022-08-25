# calico-rancher-desktop
This lab is for running Calico OSS locally <br/>
All source files are pulled into a new folder called ```rancher-desktop```

```
cd desktop
```

```
mkdir rancher-desktop
```

## Set Context to Rancher Desktop

Display list of contexts
```
kubectl config get-contexts                         
```

Display the current-context
```
kubectl config current-context                     
```

Set the default context to the cluster name ```rancher-desktop```
```
kubectl config use-context rancher-desktop    
```

## Install KinD

KinD offers a lot of customization like using another CNI component like Calico for CNI and network policies instead of relying on the built-in one <br/>
If you want to use KinD on Rancher Desktop for Kubernetes, make sure you are always using the latest release. It’s ```v0.12.0``` at the time of writing

```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.12.0/kind-darwin-amd64
```

```
chmod +x ./kind
```

## Disabling Kindnet
To use Calico as the CNI plugin in Kind clusters, we need to do the following:<br/>
<br/>

Create a kind-calico.yaml file that contains the following:

```
vi kind-calico.yaml
```

Disable the installation of kindnet:

```
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
networking:
  disableDefaultCNI: true # disable kindnet
  podSubnet: 192.168.0.0/16 # set to Calico's default subnet
```

```  
./kind create cluster --config kind-calico.yaml
```

## Verify Kind Cluster
Once the cluster is up, list the pods in the ```kube-system``` namespace to verify that kindnet is not running:

```
kubectl get pods -n kube-system
```

```kindnet``` should be missing from the list of pods: <br/>
<br/>
```Note:``` The coredns pods are in the ```pending``` state. This is expected. <br/>
They will remain in the pending state until a CNI plugin is installed.

## Install Calico CNI
Install the Tigera Calico operator and custom resource definitions.
```
kubectl create -f https://projectcalico.docs.tigera.io/manifests/tigera-operator.yaml
```

Install Calico by creating the necessary custom resource.
```
kubectl create -f https://projectcalico.docs.tigera.io/manifests/custom-resources.yaml
```

Confirm that all of the pods are running with the following command.
```
watch kubectl get pods -n calico-system
```
Wait until each pod has the ```STATUS``` of ```Running```.

Congratulations! You now have an Rancher Desktop cluster running Calico
As expected, those coredns pods are now in a ```Running``` state after the CNI install

