First we should upgrade "kubeadm" then "kubernetes cluster" then "kubelet" and "kubectl"



## check current version of kubeadm on nodes:

```bash
root@master:~# kubectl get nodes -o wide

NAME     STATUS   ROLES                  AGE    VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
master   Ready    control-plane,master   7d3h   v1.23.4   192.168.100.103   <none>        Ubuntu 20.04.4 LTS   5.4.0-107-generic   docker://20.10.14
worker   Ready    <none>                 7d3h   v1.23.4   192.168.100.104   <none>        Ubuntu 20.04.4 LTS   5.4.0-107-generic   docker://20.10.14
```


## list available versions of "kubeadm":

```bash
root@master:~# apt list kubeadm -a | head

Listing...
kubeadm/kubernetes-xenial,now 1.23.4-00 amd64 [installed]
kubeadm/kubernetes-xenial 1.23.3-00 amd64
kubeadm/kubernetes-xenial 1.23.2-00 amd64
kubeadm/kubernetes-xenial 1.23.1-00 amd64
kubeadm/kubernetes-xenial 1.23.0-00 amd64
kubeadm/kubernetes-xenial 1.22.8-00 amd64
kubeadm/kubernetes-xenial 1.22.7-00 amd64
kubeadm/kubernetes-xenial 1.22.6-00 amd64

```


## Upgrade kubeadm

Ubuntu/Debian:

```bash
root@master:~# 	apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.23.5-00 && \
apt-mark hold kubeadm
```


CentOS/RHEL:

```bash
yum install -y kubeadm-1.23.5-0 --disableexcludes=kubernetes
```


## make the node unschedulable and gracefully terminate the pod

```bash
root@master:~# kubectl drain master --ignore-daemonsets 
```


## Upgrade kubernetes:

First, plan changes:

```bash
root@master:~# kubeadm upgrade plan
```


Then, apply the upgrade plan:

```bash
root@master:~# kubeadm upgrade apply v1.23.x

[upgrade/successful] SUCCESS! Your cluster was upgraded to "v1.23.x". Enjoy!

[upgrade/kubelet] Now that your control plane is upgraded, please proceed with upgrading your kubelets if you haven't already done so.

```

## Upgrade kubelet and kubectl 

Ubuntu/Debian:

```bash

root@master:~# 	apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.23.x-00 kubectl=1.23.x-00 && \
apt-mark hold kubelet kubectl
```




CentOS/RHEL:

```bash
root@master:~# yum install -y kubelet-1.23.x-0 kubectl-1.23.x-0 --disableexcludes=kubernetes
```

```bash
root@master:~# systemctl daemon-reload 
root@master:~# systemctl restart kubelet
```


At the end, we should make the master node schedulable again:

```bash
root@master:~# kubectl uncordon master 
```


verify the status of the cluster:

```bash

root@master:~# kubectl get nodes

NAME     STATUS   ROLES                  AGE    VERSION
master   Ready    control-plane,master   7d4h   v1.23.5
worker   Ready    <none>                 7d4h   v1.23.5


root@master:~# kubeadm version

kubeadm version: &version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:57:37Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}


root@master:~# kubectl version

Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:58:47Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:52:18Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
```
We should do this process for all Kubernetes Master nodes

##
## Then we should upgrade worker nodes one-by-one
##



## Upgrade kubeadm

Ubuntu/Debian:

```bash
root@worker1:~# apt-mark unhold kubeadm && \
apt-get update && apt-get install -y kubeadm=1.23.5-00 && \
apt-mark hold kubeadm
```


CentOS/RHEL:

```bash
root@worker1:~# yum install -y kubeadm-1.23.5-0 --disableexcludes=kubernetes
```

## Call "kubeadm upgrade"

### For worker nodes this upgrades the local kubelet configuration:

```bash
root@worker1:~# kubeadm upgrade node
```

## make the Worker node unschedulable and gracefully terminate the pod

```bash
root@master:~# kubectl drain worker1 --ignore-daemonsets 
```


## Upgrade kubelet and kubectl 

Ubuntu/Debian:

```bash

root@worker1:~# apt-mark unhold kubelet kubectl && \
apt-get update && apt-get install -y kubelet=1.23.x-00 kubectl=1.23.x-00 && \
apt-mark hold kubelet kubectl
```




CentOS/RHEL:

```bash
root@worker1:~# yum install -y kubelet-1.23.x-0 kubectl-1.23.x-0 --disableexcludes=kubernetes
```

```bash
root@worker1:~# systemctl daemon-reload 
root@worker1:~# systemctl restart kubelet
```


At the end, we should make the master node schedulable again:

```bash
root@master:~# kubectl uncordon worker1 
```


