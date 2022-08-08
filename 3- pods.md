## Create a pod with yaml file

```bash
root@master:~# cat <<EOF >>multi-container-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
  labels:
    app: nginx
    tier: dev

spec:

  containers:
  - name: nginx-container
    image: nginx
    env:
    - name: DEMO_GREETING_ENV
      value: "Hello from the environement"
    ports:
    - containerPort: 80

  - name: redis-container
    image: redis

EOF
```


```bash
root@master:~# kubectl apply -f multi-container-pod.yaml 

pod/multi-container-pod created
```



```bash
root@master:~# kubectl get -f multi-container-pod.yaml

NAME                  READY   STATUS    RESTARTS      AGE
multi-container-pod   2/2     Running   2 (24m ago)   15h
```




## Edit a pod with yaml file

```bash
root@master:~# kubectl edit -f multi-container-pod.yaml

pod/multi-container-pod edited
```



## Describe a pod with yaml file

```bash
root@master:~# kubectl describe -f multi-container-pod.yaml 
Name:         multi-container-pod
Namespace:    default
Priority:     0
Node:         worker/192.168.100.104
Start Time:   Thu, 07 Apr 2022 15:47:20 +0000
Labels:       app=nginx
              tier=dev
Annotations:  cni.projectcalico.org/containerID: 73704f665131debea1e50e1de52a03a0662a58a1e61072c7df404aaa2be0f55a
              cni.projectcalico.org/podIP: 10.244.171.92/32
              cni.projectcalico.org/podIPs: 10.244.171.92/32
Status:       Running
IP:           10.244.171.92
IPs:
  IP:  10.244.171.92
Containers:
  nginx-container:
    Container ID:   docker://eeba955782bdd1accc9a43aad2d2346ce844cf8f40e8df5a4fa61eba0c4e2b5c
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2275af0f20d71b293916f1958f8497f987b8d8fd8113df54635f2a5915002bf1
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 08 Apr 2022 06:52:21 +0000
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Thu, 07 Apr 2022 15:47:25 +0000
      Finished:     Fri, 08 Apr 2022 06:51:17 +0000
    Ready:          True
    Restart Count:  1
    Environment:
      DEMO_GREETING_ENV:  Hello from the environement
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vtp4f (ro)
  redis-container:
    Container ID:   docker://7cb0acf894cdcd2aa2e0d8d38679c41ffb93a19d39e0ad0b19b0f21aaffefffc
    Image:          redis
    Image ID:       docker-pullable://redis@sha256:69a3ab2516b560690e37197b71bc61ba245aafe4525ebdece1d8a0bc5669e3e2
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 08 Apr 2022 06:52:24 +0000
    Last State:     Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Thu, 07 Apr 2022 15:47:29 +0000
      Finished:     Fri, 08 Apr 2022 05:20:21 +0000
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vtp4f (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-vtp4f:
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
  Type    Reason          Age                From               Message
  ----    ------          ----               ----               -------
  Normal  Scheduled       15h                default-scheduler  Successfully assigned default/multi-container-pod to worker
  Normal  Pulling         15h                kubelet            Pulling image "nginx"
  Normal  Pulled          15h                kubelet            Successfully pulled image "nginx" in 3.719443283s
  Normal  Created         15h                kubelet            Created container nginx-container
  Normal  Started         15h                kubelet            Started container nginx-container
  Normal  Pulling         15h                kubelet            Pulling image "redis"
  Normal  Pulled          15h                kubelet            Successfully pulled image "redis" in 3.689370068s
  Normal  Created         15h                kubelet            Created container redis-container
  Normal  Started         15h                kubelet            Started container redis-container
  Normal  SandboxChanged  23m (x2 over 24m)  kubelet            Pod sandbox changed, it will be killed and re-created.
  Normal  Pulling         23m                kubelet            Pulling image "nginx"
  Normal  Pulled          23m                kubelet            Successfully pulled image "nginx" in 4.785987496s
  Normal  Created         23m                kubelet            Created container nginx-container
  Normal  Started         23m                kubelet            Started container nginx-container
  Normal  Pulling         23m                kubelet            Pulling image "redis"
  Normal  Pulled          23m                kubelet            Successfully pulled image "redis" in 3.125366656s
  Normal  Created         23m                kubelet            Created container redis-container
  Normal  Started         23m                kubelet            Started container redis-container

```


## Delete a pod with yaml file

```bash
root@master:~# kubectl delete -f multi-container-pod.yaml

pod "multi-container-pod" deleted
```




## Create a pod with kubectl run command


```bash
root@master:~# kubectl run nginx-pod --image=nginx

pod/nginx-pod created
```

```bash
root@master:~# kubectl get pods
NAME                  READY   STATUS    RESTARTS   AGE
nginx-pod             1/1     Running   0          24m
```


```bash
root@master:~# kubectl get pods -o wide
NAME                  READY   STATUS    RESTARTS   AGE     IP              NODE     NOMINATED NODE   READINESS GATES
nginx-pod             1/1     Running   0          24m     10.244.171.93   worker   <none>           <none>
```

## print pods with labels

```bash
root@master:~# kubectl get pods --show-labels

NAME                  READY   STATUS    RESTARTS      AGE   LABELS
nginx-pod             1/1     Running   0             10m   run=nginx-pod
```

```bash
root@master:~# k get pods -l run=nginx-pod

NAME                  READY   STATUS    RESTARTS      AGE
nginx-pod             1/1     Running   0             10m
```



## YAML output

```bash
root@master:~# kubectl get pods nginx-pod -o yaml
apiVersion: v1
kind: Pod
metadata:
  annotations:
    cni.projectcalico.org/containerID: 56d5e37c1dca912dd8a8ee4e056bcc7c5c85dafd69c2f5e1125c614fe0387759
    cni.projectcalico.org/podIP: 10.244.171.93/32
    cni.projectcalico.org/podIPs: 10.244.171.93/32
  creationTimestamp: "2022-04-08T07:01:11Z"
  labels:
    run: nginx-pod
  name: nginx-pod
  namespace: default
  resourceVersion: "77445"
  uid: 63525d07-4990-4435-aa10-5a3e3ebbf454
spec:
  containers:
  - image: nginx
    imagePullPolicy: Always
    name: nginx-pod
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-shmkx
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: worker
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-shmkx
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2022-04-08T07:01:11Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2022-04-08T07:01:11Z"
    message: 'containers with unready status: [nginx-pod]'
    reason: ContainersNotReady
    status: "False"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2022-04-08T07:01:11Z"
    message: 'containers with unready status: [nginx-pod]'
    reason: ContainersNotReady
    status: "False"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2022-04-08T07:01:11Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - image: nginx
    imageID: ""
    lastState: {}
    name: nginx-pod
    ready: false
    restartCount: 0
    started: false
    state:
      waiting:
        reason: ContainerCreating
  hostIP: 192.168.100.104
  phase: Pending
  qosClass: BestEffort
  startTime: "2022-04-08T07:01:11Z"
```

## print pods from all name spaces: 
```bash
root@master:~# kubectl get pods -A
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
default                multi-container-pod                          2/2     Running   2 (5m29s ago)   15h
kube-system            calico-kube-controllers-56fcbf9d6b-zkhc2     1/1     Running   10              23h
kube-system            calico-node-5hfv2                            1/1     Running   2 (5m40s ago)   23h
kube-system            calico-node-wb5cc                            1/1     Running   3 (96m ago)     23h
kube-system            coredns-64897985d-bzhgt                      1/1     Running   2 (14h ago)     23h
kube-system            coredns-64897985d-jv568                      1/1     Running   2 (14h ago)     24h
kube-system            etcd-master                                  1/1     Running   2 (5m40s ago)   24h
kube-system            kube-apiserver-master                        1/1     Running   3 (5m40s ago)   24h
kube-system            kube-controller-manager-master               1/1     Running   4 (5m40s ago)   24h
kube-system            kube-proxy-g7wcd                             1/1     Running   3 (96m ago)     24h
kube-system            kube-proxy-z6zpg                             1/1     Running   2 (5m40s ago)   24h
kube-system            kube-scheduler-master                        1/1     Running   4 (5m40s ago)   24h
kube-system            metrics-server-847dcc659d-9sjvh              0/1     Running   1 (96m ago)     15h
kubernetes-dashboard   dashboard-metrics-scraper-799d786dbf-bgc6h   1/1     Running   3 (96m ago)     23h
kubernetes-dashboard   kubernetes-dashboard-fb8648fd9-pmm5c         1/1     Running   7 (96m ago)     23h
tigera-operator        tigera-operator-b876f5799-ffkg2              1/1     Running   6 (4m47s ago)   23h

```


```bash
root@master:~# kubectl get pods --all-namespaces
NAMESPACE              NAME                                         READY   STATUS    RESTARTS        AGE
default                multi-container-pod                          2/2     Running   2 (7m58s ago)   15h
kube-system            calico-kube-controllers-56fcbf9d6b-zkhc2     1/1     Running   10              23h
kube-system            calico-node-5hfv2                            1/1     Running   2 (8m9s ago)    23h
kube-system            calico-node-wb5cc                            1/1     Running   3 (98m ago)     23h
kube-system            coredns-64897985d-bzhgt                      1/1     Running   2 (14h ago)     23h
kube-system            coredns-64897985d-jv568                      1/1     Running   2 (14h ago)     24h
kube-system            etcd-master                                  1/1     Running   2 (8m9s ago)    24h
kube-system            kube-apiserver-master                        1/1     Running   3 (8m9s ago)    24h
kube-system            kube-controller-manager-master               1/1     Running   4 (8m9s ago)    24h
kube-system            kube-proxy-g7wcd                             1/1     Running   3 (98m ago)     24h
kube-system            kube-proxy-z6zpg                             1/1     Running   2 (8m9s ago)    24h
kube-system            kube-scheduler-master                        1/1     Running   4 (8m9s ago)    24h
kube-system            metrics-server-847dcc659d-9sjvh              0/1     Running   1 (98m ago)     15h
kubernetes-dashboard   dashboard-metrics-scraper-799d786dbf-bgc6h   1/1     Running   3 (98m ago)     23h
kubernetes-dashboard   kubernetes-dashboard-fb8648fd9-pmm5c         1/1     Running   7 (98m ago)     23h
tigera-operator        tigera-operator-b876f5799-ffkg2              1/1     Running   6 (7m16s ago)   23h
```



## describe pods

```bash
root@master:~# kubectl describe pod nginx-pod 
Name:         nginx-pod
Namespace:    default
Priority:     0
Node:         worker/192.168.100.104
Start Time:   Fri, 08 Apr 2022 07:01:11 +0000
Labels:       run=nginx-pod
Annotations:  cni.projectcalico.org/containerID: 56d5e37c1dca912dd8a8ee4e056bcc7c5c85dafd69c2f5e1125c614fe0387759
              cni.projectcalico.org/podIP: 10.244.171.93/32
              cni.projectcalico.org/podIPs: 10.244.171.93/32
Status:       Running
IP:           10.244.171.93
IPs:
  IP:  10.244.171.93
Containers:
  nginx-pod:
    Container ID:   docker://ea3ba8877b7c8291681ac52beca8e74e5b170704f1be20ed86add5b8f3163c51
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2275af0f20d71b293916f1958f8497f987b8d8fd8113df54635f2a5915002bf1
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 08 Apr 2022 07:01:18 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-shmkx (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-shmkx:
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
  Normal  Scheduled  2m13s  default-scheduler  Successfully assigned default/nginx-pod to worker
  Normal  Pulling    2m12s  kubelet            Pulling image "nginx"
  Normal  Pulled     2m6s   kubelet            Successfully pulled image "nginx" in 5.996991311s
  Normal  Created    2m6s   kubelet            Created container nginx-pod
  Normal  Started    2m5s   kubelet            Started container nginx-pod
```



## Edit pods

```bash
export KUBE_EDITOR='nano'

root@master:~# kubectl edit pod nginx-pod
```


## Show logs of a pod

```bash
root@master:~# kubectl logs nginx-pod

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/04/07 15:48:30 [notice] 1#1: using the "epoll" event method
2022/04/07 15:48:30 [notice] 1#1: nginx/1.21.6
2022/04/07 15:48:30 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2022/04/07 15:48:30 [notice] 1#1: OS: Linux 5.4.0-105-generic
2022/04/07 15:48:30 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/04/07 15:48:30 [notice] 1#1: start worker processes
2022/04/07 15:48:30 [notice] 1#1: start worker process 31
2022/04/07 15:48:30 [notice] 1#1: start worker process 32
```



```bash 
root@master:~# kubectl logs multi-container-pod redis-container

1:C 07 Apr 2022 15:47:29.420 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
1:C 07 Apr 2022 15:47:29.420 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=1, just started
1:C 07 Apr 2022 15:47:29.420 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
1:M 07 Apr 2022 15:47:29.423 * monotonic clock: POSIX clock_gettime
1:M 07 Apr 2022 15:47:29.424 * Running mode=standalone, port=6379.
1:M 07 Apr 2022 15:47:29.424 # Server initialized
1:M 07 Apr 2022 15:47:29.424 * Ready to accept connections
```


## Delete a pod

```bash
root@master:~# kubectl delete pod nginx-pod 

pod "nginx-pod" deleted
```


## Get info of pods using TOP command

```bash
kubectl top pods
kubectl top pods --sort-by cpu
kubectl top pods --sort-by memory
kubectl top pods --sort-by cpu -n kube-system > cpu.txt
kubectl top pods --sort-by memory --all-namespaces
```
