
# install wget and git
yum install -y git wget

# install kubectl
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl

# install kustomize
wget https://github.com/kubernetes-sigs/kustomize/releases/download/kustomize%2Fv3.5.4/kustomize_v3.5.4_linux_amd64.tar.gz
tar -xvf kustomize_v3.5.4_linux_amd64.tar.gz
mv kustomize /usr/local/bin/
chmod a+x /usr/local/bin/kustomize


#prep the k8s cluster access

mkdir ~/.kube
#add kubeconfig to ~/.kube/config

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


# install cert manager
# install cluster certificates

# install jitsi
