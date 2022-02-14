# Install-Kubernetes-cluster-Ubuntu-20.04

## Step 1 - Update Ubuntu
```bash
sudo apt update -y ; sudo apt upgrade -y
```
## Step 2 - Install Docker
```bash
sudo apt install docker.io -y ; systemctl enable docker ; systemctl start docker
```
## Step 3 - Add cgroupdriver=systemd on Docker
```bash
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```
Reboot docker daemon
```bash
systemctl restart docker
```
Disable swap and coment the line in /etc/fstab
```bash
swapoff -a
```
## Step 4 - Add kubernetes Repository
```bash
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
```
```bash
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
## Step 5 - Install Kubernetes
```bash
apt-get install -y kubeadm kubelet kubectl ; sudo apt-mark hold kubeadm kubelet kubectl
```
Export admin.conf
```bash
export KUBECONFIG=/etc/kubernetes/admin.conf 
```
Copy admin.conf to /root folder
```bash
cp /etc/kubernetes/admin.conf /root/.kube/config  
```

## Step 6 - Initialize kubernetes kluster
```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```
## Step 7 - Install Network "CILIUM"
Install CLI
```bash
curl -L --remote-name-all https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz{,.sha256sum}
sha256sum --check cilium-linux-amd64.tar.gz.sha256sum
sudo tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
rm cilium-linux-amd64.tar.gz{,.sha256sum}
```
Install Cilium
```bash
cilium Install
```
test cilium
```bash
cilium status
```
