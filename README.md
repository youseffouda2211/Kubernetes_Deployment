# Kubernetes Cluster Deployment Guide 

Welcome to the comprehensive guide for deploying a Kubernetes cluster! In this guide, we'll walk you through setting up a highly available Kubernetes cluster using kubeadm, containerd, and Calico for networking.

# Overview
This project involves deploying a Kubernetes cluster with:

🖥️ 3 Master Nodes - Ensuring high availability and fault tolerance.

💻 4 Worker Nodes - Providing the compute power to run your applications.

Technologies Used:

Kubeadm: Bootstrap and manage the cluster.

Containerd: Lightweight container runtime.

Calico: Network policies and networking plugin.


## Installation

1- Prepare All Nodes
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y apt-transport-https ca-certificates curl gnupg lsb-release
```
2- Install Containerd
```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
```
Install Containerd:
```bash
sudo apt install -y containerd.io
```
Configure and start Containerd:
```bash
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl start containerd
sudo systemctl enable containerd
```
3- Install Kubernetes Components
```bash
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes.gpg

echo "deb [arch=amd64] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
Install kubeadm, kubelet, and kubectl:
```bash
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
```
4- Initialize the Kubernetes Control Plane
```bash
sudo kubeadm init --control-plane-endpoint "master.example.com:6443" --upload-certs --pod-network-cidr=192.168.0.0/16
```
5- Set Up kubectl for the Admin User
```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
6- Install Calico Network Plugin
```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```
7- Join Worker Nodes
```bash
sudo kubeadm join master.example.com:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```
8- Verify Cluster Status
```bash
kubectl get Nodes
```
