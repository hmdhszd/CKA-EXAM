## Check kubeadm and kubectl version

```bash
root@master:~#  kubeadm version

kubeadm version: &version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:57:37Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
```


```bash
root@master:~# kubectl version

Client Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:58:47Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"23", GitVersion:"v1.23.5", GitCommit:"c285e781331a3785a7f436042c65c5641ce8a9e9", GitTreeState:"clean", BuildDate:"2022-03-16T15:52:18Z", GoVersion:"go1.17.8", Compiler:"gc", Platform:"linux/amd64"}
```


## Check nodes

```bash
root@master:~# kubectl get nodes -o wide

NAME     STATUS   ROLES                  AGE     VERSION   INTERNAL-IP       EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
master   Ready    control-plane,master   6h15m   v1.23.5   192.168.100.103   <none>        Ubuntu 20.04.4 LTS   5.4.0-105-generic   docker://20.10.14
worker   Ready    <none>                 6h5m    v1.23.5   192.168.100.104   <none>        Ubuntu 20.04.4 LTS   5.4.0-105-generic   docker://20.10.14
```


## Print join command to add new nodes
```bash
root@master:~# kubeadm token create --print-join-command
kubeadm join 192.168.100.103:6443 --token fq7zhb.r6gg5k9tanofiixb --discovery-token-ca-cert-hash sha256:30af195bcfd6258c4f46492380630bcfb0c7cba2b0f702d10122da2503962630
```

## Check kubernetes cluster components

```bash
root@master:~# kubectl get pods -n kube-system

NAME                                       READY   STATUS    RESTARTS        AGE
calico-kube-controllers-56fcbf9d6b-zkhc2   1/1     Running   7 (3h19m ago)   6h2m
calico-node-5hfv2                          1/1     Running   1 (3h19m ago)   6h2m
calico-node-wb5cc                          1/1     Running   1 (3h1m ago)    6h2m
coredns-64897985d-bzhgt                    1/1     Running   1 (3h19m ago)   6h9m
coredns-64897985d-jv568                    1/1     Running   1 (3h18m ago)   6h23m
etcd-master                                1/1     Running   1 (3h18m ago)   6h24m
kube-apiserver-master                      1/1     Running   2 (3h18m ago)   6h24m
kube-controller-manager-master             1/1     Running   2 (3h19m ago)   6h24m
kube-proxy-g7wcd                           1/1     Running   1 (3h1m ago)    6h13m
kube-proxy-z6zpg                           1/1     Running   1 (3h19m ago)   6h23m
kube-scheduler-master                      1/1     Running   2 (3h18m ago)   6h24m
```

and check all components in different name spaces

```bash
root@master:~# kubectl get all --all-namespaces

NAMESPACE              NAME                                             READY   STATUS    RESTARTS        AGE
kube-system            pod/calico-kube-controllers-56fcbf9d6b-zkhc2     1/1     Running   7 (3h21m ago)   6h4m
kube-system            pod/calico-node-5hfv2                            1/1     Running   1 (3h21m ago)   6h4m
kube-system            pod/calico-node-wb5cc                            1/1     Running   1 (3h3m ago)    6h4m
kube-system            pod/coredns-64897985d-bzhgt                      1/1     Running   1 (3h21m ago)   6h11m
kube-system            pod/coredns-64897985d-jv568                      1/1     Running   1 (3h20m ago)   6h25m
kube-system            pod/etcd-master                                  1/1     Running   1 (3h20m ago)   6h25m
kube-system            pod/kube-apiserver-master                        1/1     Running   2 (3h20m ago)   6h25m
kube-system            pod/kube-controller-manager-master               1/1     Running   2 (3h21m ago)   6h25m
kube-system            pod/kube-proxy-g7wcd                             1/1     Running   1 (3h3m ago)    6h15m
kube-system            pod/kube-proxy-z6zpg                             1/1     Running   1 (3h21m ago)   6h25m
kube-system            pod/kube-scheduler-master                        1/1     Running   2 (3h20m ago)   6h25m
kubernetes-dashboard   pod/dashboard-metrics-scraper-799d786dbf-bgc6h   1/1     Running   1 (3h3m ago)    5h56m
kubernetes-dashboard   pod/kubernetes-dashboard-fb8648fd9-pmm5c         1/1     Running   5 (3h22m ago)   5h56m
tigera-operator        pod/tigera-operator-b876f5799-ffkg2              1/1     Running   2 (3h2m ago)    6h

NAMESPACE              NAME                                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                  AGE
default                service/kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                  6h25m
kube-system            service/kube-dns                    ClusterIP   10.96.0.10      <none>        53/UDP,53/TCP,9153/TCP   6h25m
kube-system            service/metrics-server              ClusterIP   10.103.87.63    <none>        443/TCP                  5h15m
kubernetes-dashboard   service/dashboard-metrics-scraper   ClusterIP   10.111.181.61   <none>        8000/TCP                 5h56m
kubernetes-dashboard   service/kubernetes-dashboard        NodePort    10.107.149.6    <none>        443:30229/TCP            5h56m

NAMESPACE     NAME                         DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR            AGE
kube-system   daemonset.apps/calico-node   2         2         2       2            2           kubernetes.io/os=linux   6h4m
kube-system   daemonset.apps/kube-proxy    2         2         2       2            2           kubernetes.io/os=linux   6h25m

NAMESPACE              NAME                                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system            deployment.apps/calico-kube-controllers     1/1     1            1           6h4m
kube-system            deployment.apps/coredns                     2/2     2            2           6h25m
kubernetes-dashboard   deployment.apps/dashboard-metrics-scraper   1/1     1            1           5h56m
kubernetes-dashboard   deployment.apps/kubernetes-dashboard        1/1     1            1           5h56m
tigera-operator        deployment.apps/tigera-operator             1/1     1            1           6h18m

NAMESPACE              NAME                                                   DESIRED   CURRENT   READY   AGE
kube-system            replicaset.apps/calico-kube-controllers-56fcbf9d6b     1         1         1       6h4m
kube-system            replicaset.apps/coredns-64897985d                      2         2         2       6h25m
kubernetes-dashboard   replicaset.apps/dashboard-metrics-scraper-799d786dbf   1         1         1       5h56m
kubernetes-dashboard   replicaset.apps/kubernetes-dashboard-fb8648fd9         1         1         1       5h56m
tigera-operator        replicaset.apps/tigera-operator-b876f5799              1         1         1       6h18m
```

## Check Docker and kubelet


```bash
root@master:~# systemctl status docker

● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running)
```


```bash
root@master:~# systemctl status kubelet

● kubelet.service - kubelet: The Kubernetes Node Agent
     Loaded: loaded (/lib/systemd/system/kubelet.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/kubelet.service.d
             └─10-kubeadm.conf
     Active: active (running)
```

## Deploy test application to check overal health of an application

```bash
root@master:~# kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml

deployment.apps/nginx-deployment created
```


```bash
root@master:~# kubectl get deployments -o wide

NAME               READY   UP-TO-DATE   AVAILABLE   AGE    CONTAINERS   IMAGES         SELECTOR
nginx-deployment   3/3     3            3           4m3s   nginx        nginx:1.14.2   app=nginx
```


```bash
root@master:~# kubectl get pods -o wide

NAME                               READY   STATUS    RESTARTS   AGE     IP              NODE     NOMINATED NODE   READINESS GATES
nginx-deployment-9456bbbf9-ddqns   1/1     Running   0          4m40s   10.244.171.73   worker   <none>           <none>
nginx-deployment-9456bbbf9-vnfjq   1/1     Running   0          4m40s   10.244.171.72   worker   <none>           <none>
nginx-deployment-9456bbbf9-zj6pd   1/1     Running   0          4m40s   10.244.171.71   worker   <none>           <none>
```

in case of any problems, we can check the status of pods:

```bash
root@master:~# kubectl describe pod nginx-deployment-9456bbbf9-ddqns

Name:         nginx-deployment-9456bbbf9-ddqns
Namespace:    default
Priority:     0
Node:         worker/192.168.100.104
Start Time:   Thu, 07 Apr 2022 13:20:56 +0000
Labels:       app=nginx
              pod-template-hash=9456bbbf9
Annotations:  cni.projectcalico.org/containerID: c801c3bcae4ec95cf3c1cb956787a05ddd084e0da0c44f7ab386bb5354f72cb9
              cni.projectcalico.org/podIP: 10.244.171.73/32
              cni.projectcalico.org/podIPs: 10.244.171.73/32
Status:       Running
IP:           10.244.171.73
IPs:
  IP:           10.244.171.73
Controlled By:  ReplicaSet/nginx-deployment-9456bbbf9
Containers:
  nginx:
    Container ID:   docker://00694c54359a5d018a41a4ece28fa9b7af012e1973c9096044ae46a52eac69d8
    Image:          nginx:1.14.2
    Image ID:       docker-pullable://nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 07 Apr 2022 13:22:12 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-4cctn (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-4cctn:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  3m17s  default-scheduler  Successfully assigned default/nginx-deployment-9456bbbf9-ddqns to worker
  Normal  Pulling    3m15s  kubelet            Pulling image "nginx:1.14.2"
  Normal  Pulled     2m1s   kubelet            Successfully pulled image "nginx:1.14.2" in 1m14.139273415s
  Normal  Created    2m1s   kubelet            Created container nginx
  Normal  Started    2m1s   kubelet            Started container nginx
```


```bash

```


```bash

```


```bash

```


```bash

```


```bash

```


```bash

```
```bash

```


```bash

```


```bash

```


```bash

```


```bash

```


```bash

```


```bash

```