# Below instructions should be followed for a manual bring up of jitsi suite


The kubernetes artifacts should be applied on the K8s cluster.
some tools needed to do this operation should be available on a developer machine

## Requirements for a development setup with 2 shards
 - developer machine - 1vCPU , 2 GB RAM , Centos7
- k8s cluster - 2 * (4vCPU, 8GB RAM)

### Setup the developement machine with artifacts to talk to k8s cluster

##### install wget and git
```
git clone -b topic/ayathiraj/develop  https://github.com/abhiky/jitsi-deployment.git
yum install -y git wget
```
##### install kubectl
```
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```
##### install kustomize
```
wget https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv3.5.4/kustomize_v3.5.4_linux_amd64.tar.gz
tar -xvf kustomize_v3.5.4_linux_amd64.tar.gz
mv kustomize /usr/local/bin/
chmod a+x /usr/local/bin/kustomize
```

##### prep the k8s cluster access
```
mkdir ~/.kube
# add the config file at  ~/.kube/config
```
##### add kubeconfig to ~/.kube/config

```
cat << EOF >> ~/.bashrc
source <(kubectl completion bash)
alias k="kubectl"
alias kd="kubectl describe"
alias kga="kubectl get all"
alias kgp="kubectl get pods"
alias kl="kubectl logs"
alias ke="kubectl exec -it "
alias kpv="kubectl get pvc"
alias kp="kubectl get pv"
alias kpw="kubectl get pod -w -o wide"
EOF
source ~/.bashrc
```


### apply artifacts to the k8s cluster


```
cd jitsi-deployment/base/ops/cert-manager
# change node1 to node name appropriately
kubectl set label node1 topology.kubernetes.io/zone=ZONE1
# change node2 to node name appropriately
kubectl set label node2 topology.kubernetes.io/zone=ZONE2


kubectl apply -f cert-manager.yaml
# edit cluster-issuer.yaml as required
kubectl apply -f cluster-issuer.yaml

cd jitsi-deployment/overlays/development
# change hosts as required
#[root@k8-decc development]# grep -ir "meetme" .
#./ops/certificate.yaml:  - meetme.abhishekyathiraj.in
#./ops/grafana-deployment-patch.yaml:              value: "meetme.abhishekyathiraj.in"
#./ops/grafana-ingress-patch.yaml:    - meetme.abhishekyathiraj.in
#./ops/grafana-ingress-patch.yaml:    - host: meetme.abhishekyathiraj.in
#./ops/haproxy-ingress-patch.yaml:    - meetme.abhishekyathiraj.in
#./ops/haproxy-ingress-patch.yaml:    - host: meetme.abhishekyathiraj.in

kustomize build . | kubectl -f apply -
```



