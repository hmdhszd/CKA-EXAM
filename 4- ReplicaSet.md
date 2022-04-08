## Create a ReplicaSet with yaml file

```bash
root@master:~# cat <<EOF >>nginx-replicaset.yaml

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-app
        tier: frontend
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80

EOF
```


```bash
root@master:~# kubectl apply -f nginx-replicaset.yaml

replicaset.apps/nginx-rs created
```



```bash
root@master:~# kubectl get -f nginx-replicaset.yaml

NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       2m41s
```




## Edit a pod with yaml file

```bash
root@master:~# kubectl edit -f nginx-replicaset.yaml

replicaset.apps/nginx-rs edited
```



## Describe a pod with yaml file

```bash
root@master:~# kubectl describe -f nginx-replicaset.yaml

Name:         nginx-rs
Namespace:    default
Selector:     app=nginx-app
Labels:       <none>
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-app
           tier=frontend
  Containers:
   nginx-container:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  19s   replicaset-controller  Created pod: nginx-rs-lcl5d
  Normal  SuccessfulCreate  19s   replicaset-controller  Created pod: nginx-rs-6xv65
  Normal  SuccessfulCreate  19s   replicaset-controller  Created pod: nginx-rs-xs48s
```


## Delete a pod with yaml file

```bash
root@master:~# kubectl delete -f nginx-replicaset.yaml

replicaset.apps "nginx-rs" deleted
```



## Scale a ReplicaSet

```bash
root@master:~# k get rs
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         0       4s


root@master:~# kubectl scale rs nginx-rs --replicas=5
replicaset.apps/nginx-rs scaled


root@master:~# kubectl get rs nginx-rs 
NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   5         5         5       38s
```


## Print the ReplicaSets info with kubectl run command


```bash
root@master:~# kubectl get rs

NAME       DESIRED   CURRENT   READY   AGE
nginx-rs   3         3         3       72s
```


```bash
root@master:~# kubectl get rs -o wide

NAME       DESIRED   CURRENT   READY   AGE   CONTAINERS        IMAGES   SELECTOR
nginx-rs   3         3         3       77s   nginx-container   nginx    app=nginx-app
```


## YAML output

```bash
root@master:~# kubectl get rs -o yaml

apiVersion: v1
items:
- apiVersion: apps/v1
  kind: ReplicaSet
  metadata:
    annotations:
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"apps/v1","kind":"ReplicaSet","metadata":{"annotations":{},"name":"nginx-rs","namespace":"default"},"spec":{"replicas":3,"selector":{"matchLabels":{"app":"nginx-app"}},"template":{"metadata":{"labels":{"app":"nginx-app","tier":"frontend"},"name":"nginx-pod"},"spec":{"containers":[{"image":"nginx","name":"nginx-container","ports":[{"containerPort":80}]}]}}}}
    creationTimestamp: "2022-04-08T08:52:20Z"
    generation: 1
    name: nginx-rs
    namespace: default
    resourceVersion: "92866"
    uid: b857d375-4e1e-4c3e-9f3a-f6ba279c3689
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: nginx-app
    template:
      metadata:
        creationTimestamp: null
        labels:
          app: nginx-app
          tier: frontend
        name: nginx-pod
      spec:
        containers:
        - image: nginx
          imagePullPolicy: Always
          name: nginx-container
          ports:
          - containerPort: 80
            protocol: TCP
          resources: {}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
  status:
    availableReplicas: 3
    fullyLabeledReplicas: 3
    observedGeneration: 1
    readyReplicas: 3
    replicas: 3
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```


## json output

```bash
root@master:~# kubectl get rs -o json

{
    "apiVersion": "v1",
    "items": [
        {
            "apiVersion": "apps/v1",
            "kind": "ReplicaSet",
            "metadata": {
                "annotations": {
                    "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"apps/v1\",\"kind\":\"ReplicaSet\",\"metadata\":{\"annotations\":{},\"name\":\"nginx-rs\",\"namespace\":\"default\"},\"spec\":{\"replicas\":3,\"selector\":{\"matchLabels\":{\"app\":\"nginx-app\"}},\"template\":{\"metadata\":{\"labels\":{\"app\":\"nginx-app\",\"tier\":\"frontend\"},\"name\":\"nginx-pod\"},\"spec\":{\"containers\":[{\"image\":\"nginx\",\"name\":\"nginx-container\",\"ports\":[{\"containerPort\":80}]}]}}}}\n"
                },
                "creationTimestamp": "2022-04-08T08:52:20Z",
                "generation": 1,
                "name": "nginx-rs",
                "namespace": "default",
                "resourceVersion": "92866",
                "uid": "b857d375-4e1e-4c3e-9f3a-f6ba279c3689"
            },
            "spec": {
                "replicas": 3,
                "selector": {
                    "matchLabels": {
                        "app": "nginx-app"
                    }
                },
                "template": {
                    "metadata": {
                        "creationTimestamp": null,
                        "labels": {
                            "app": "nginx-app",
                            "tier": "frontend"
                        },
                        "name": "nginx-pod"
                    },
                    "spec": {
                        "containers": [
                            {
                                "image": "nginx",
                                "imagePullPolicy": "Always",
                                "name": "nginx-container",
                                "ports": [
                                    {
                                        "containerPort": 80,
                                        "protocol": "TCP"
                                    }
                                ],
                                "resources": {},
                                "terminationMessagePath": "/dev/termination-log",
                                "terminationMessagePolicy": "File"
                            }
                        ],
                        "dnsPolicy": "ClusterFirst",
                        "restartPolicy": "Always",
                        "schedulerName": "default-scheduler",
                        "securityContext": {},
                        "terminationGracePeriodSeconds": 30
                    }
                }
            },
            "status": {
                "availableReplicas": 3,
                "fullyLabeledReplicas": 3,
                "observedGeneration": 1,
                "readyReplicas": 3,
                "replicas": 3
            }
        }
    ],
    "kind": "List",
    "metadata": {
        "resourceVersion": "",
        "selfLink": ""
    }
}
```


## print ReplicaSets from all name spaces: 
```bash
root@master:~# kubectl get rs -A

NAMESPACE              NAME                                   DESIRED   CURRENT   READY   AGE
default                nginx-rs                               3         3         3       5m48s
kube-system            calico-kube-controllers-56fcbf9d6b     1         1         1       25h
kube-system            coredns-64897985d                      2         2         2       26h
kube-system            metrics-server-847dcc659d              1         1         0       17h
kubernetes-dashboard   dashboard-metrics-scraper-799d786dbf   1         1         1       25h
kubernetes-dashboard   kubernetes-dashboard-fb8648fd9         1         1         1       25h
tigera-operator        tigera-operator-b876f5799              1         1         1       26h
```


```bash
root@master:~# kubectl get rs --all-namespaces

NAMESPACE              NAME                                   DESIRED   CURRENT   READY   AGE
default                nginx-rs                               3         3         3       5m56s
kube-system            calico-kube-controllers-56fcbf9d6b     1         1         1       25h
kube-system            coredns-64897985d                      2         2         2       26h
kube-system            metrics-server-847dcc659d              1         1         0       17h
kubernetes-dashboard   dashboard-metrics-scraper-799d786dbf   1         1         1       25h
kubernetes-dashboard   kubernetes-dashboard-fb8648fd9         1         1         1       25h
tigera-operator        tigera-operator-b876f5799              1         1         1       26h
```



## describe ReplicaSets

```bash
root@master:~# kubectl describe rs nginx-rs

Name:         nginx-rs
Namespace:    default
Selector:     app=nginx-app
Labels:       <none>
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx-app
           tier=frontend
  Containers:
   nginx-container:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  7m19s  replicaset-controller  Created pod: nginx-rs-lcl5d
  Normal  SuccessfulCreate  7m19s  replicaset-controller  Created pod: nginx-rs-6xv65
  Normal  SuccessfulCreate  7m19s  replicaset-controller  Created pod: nginx-rs-xs48s
```



## Edit ReplicaSets

```bash
export KUBE_EDITOR='nano'

root@master:~# kubectl edit rs nginx-rs

replicaset.apps/nginx-rs edited
```


## Show logs of a pod of a ReplicaSet

```bash
root@master:~# kubectl logs nginx-rs-
nginx-rs-6xv65  nginx-rs-ggtfp  nginx-rs-lcl5d


root@master:~# kubectl logs nginx-rs-6xv65

/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2022/04/08 08:52:25 [notice] 1#1: using the "epoll" event method
2022/04/08 08:52:25 [notice] 1#1: nginx/1.21.6
2022/04/08 08:52:25 [notice] 1#1: built by gcc 10.2.1 20210110 (Debian 10.2.1-6) 
2022/04/08 08:52:25 [notice] 1#1: OS: Linux 5.4.0-105-generic
2022/04/08 08:52:25 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2022/04/08 08:52:25 [notice] 1#1: start worker processes
2022/04/08 08:52:25 [notice] 1#1: start worker process 33
2022/04/08 08:52:25 [notice] 1#1: start worker process 34
```



## Delete a pod

```bash
root@master:~# kubectl delete rs nginx-rs

replicaset.apps "nginx-rs" deleted
```


## Get info of pods using TOP command

```bash
kubectl top pods
kubectl top pods --sort-by cpu
kubectl top pods --sort-by memory
kubectl top pods --sort-by cpu -n kube-system > cpu.txt
kubectl top pods --sort-by memory --all-namespaces
```
