# Kubernetes Installation Steps


#### We have to run certain commands at the Master to bring up the Cluster and some commands we have to run at all the agents end. Some commands have to be runs on both the Master and Agent.
## Master ubuntu box - 2GB Ram and 2 cores
## Agent ubuntu box - 2GB Ram and 1 core

## At both Master and Agent

#### Update the repository
```ubuntu
sudo su
apt-get update
```
#### Turn Off Swap Space
```ubuntu
swapoff -a
nano /etc/fstab
#use the nano editor and use the # to comment out the --> /swapfile line, save and exit.
```
#### Update Hostname, Hosts & set Static IP(If you have a dynamic address if the machine restarts it wont be able to join the cluster because there'll have a different IP address).
```ubuntu
nano /etc/hostname
#use the nano editor and replace the virtual box name with Kmaster (or AgentNode when installing on agent VM).
```
#### Note the IP address
```ubuntu
ifconfig
```
```ubuntu
#Update the hosts file
nano /etc/hosts

#set a static IP address
nano /etc/network/interfaces
#use the nano editor to add the IP address and Kmaster save and exit
#RESTART THE VM
#host name should be ...@kmaster and

#Add the below at the end of the file
nano enp0s8
iface enp0s8 inet static
address <IP-Address-Of-VM>

```
#### Install OpenSSH Server & Docker (K8 req the open ssh functionality and Docker because k8 is a container management tool.)
```ubuntu
sudo apt-get install openssh-server

sudo su
apt-get update
apt-get install -y docker.io
```
#### Install Kubeadm, Kubelet & Kubectl
```ubuntu
#pre req download packages using curl
apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF>/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial machineEOF

apt-get update

#Install Kubeadm, kubelet &Kunectl
apt-get install -y kubelet kubeadm kubectl

#Update the kubernetes configuration
nano /etc/system/Kubelet.service.d/10-kubeadm.conf

#Add the below after the last line (after the last Env Variable)
#Environment="cgroup-driver=systemd/cgroup-driver=cgroupfs"
```


## Only at Master
```ubuntu
#make sure there details about the Agent
cat /etc/hosts
sudo nano /etc/hosts
#add Ip address of the KNode save and exit
restart
```


#### Initiate Kubernetes Cluster on Master
```ubuntu
sudo kubeadm init --pod-network-cidr=<192.168.0.0/16> --apiserver-advertise-address=<ip-address-of-master>
#For starting a Calico CNI:192.168.0.0/16 #Or For starting a Flannel CNI:10.244.0.0/16
```
#### Install the Pod Network
```ubuntu
kubectl apply -f https://docs/projectcalico.org/v3.0/getting-started/kubernetes/Installation/hosted/kubeadm/1.7/calico.yaml
```
#### Setup the Kubernetes Dashboard
```ubuntu
kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml

kuberctl proxy

#to create a service for the Dashboard
kubectl create service account dashboard -n default

#To add cluster binding rules for your roles on dashboard
kubectl create clusterrolebinding dshboard-admin -n default \
 --clusterrole=cluster-admin \
 --serviceaccount=default:dashboard

#to get the secret key to be pasted into the dashboard token pwd. Copy the out coming secret key

kubectl get secret $(kubectl serviceaccount dashboard -o jsonpath="{.secrets[0].name}") -o jsonpath="{.data.token}" | base64 --decode

```
#### Only at Agent
```ubuntu
cat /etc/hosts
#take note of the Node Ip

ifconfig

cat /etc/network/interfaces
#Ensure that it is a statics IP address

sudo nano /etc/hosts
#add Ip address of the Kmaster save and exit
restart
```
#### Join the Cluster
```ubuntu

```

#### To start using the Cluster run these as a regular user:
```ubuntu
mkdir -p $Home/ .kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl get nodes                     //status of Nodes
kubectl1 get pods --all-namespaces   //status of PODS  
kubectl get -o wide pods --all-namespaces    //Detailed status of PODS
```
#### you should now deploy a pod network to the cluster.
```ubuntu
"kubectl apply -f [podnetwork].yaml" with one of the options listed at:
http://kubernete.io/doc/concepts/cluster-administration/addons/
```
you can now join any number of machines by running the following on each node as
root:

     kubeadm join <adress and token and cert from master initialisation>
