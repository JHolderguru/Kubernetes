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


#### Initiate Kubernetes Cluster
```ubuntu

```
#### Install the Pod Network
```ubuntu

```
#### Setup the Kubernetes Dashboard
```ubuntu

```
## Only at Agent
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
