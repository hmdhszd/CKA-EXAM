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

check the status of the pod:

```bash
root@master:~# kubectl get pods

NAME                  READY   STATUS    RESTARTS   AGE
multi-container-pod   2/2     Running   0          18s

```




## Create a pod with kubectl run command


```bash
root@master:~# kubectl run nginx-pod --image=nginx

pod/nginx-pod created
```

```bash
root@master:~# k get pods

NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          7s
```


```bash
root@master:~# kubectl get pods -o wide
NAME                  READY   STATUS    RESTARTS   AGE     IP              NODE     NOMINATED NODE   READINESS GATES
multi-container-pod   2/2     Running   0          7m20s   10.244.171.83   worker   <none>           <none>

```


## YAML output

```bash
root@master:~# kubectl get pods -o yaml

apiVersion: v1
items:
- apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      cni.projectcalico.org/containerID: c6bb6a7e0cc37b617eba71209d99e0560654e202136f5a577f31d1160299add5
      cni.projectcalico.org/podIP: 10.244.171.83/32
      cni.projectcalico.org/podIPs: 10.244.171.83/32
      kubectl.kubernetes.io/last-applied-configuration: |
        {"apiVersion":"v1","kind":"Pod","metadata":{"annotations":{},"labels":{"app":"nginx","tier":"dev"},"name":"multi-container-pod","namespace":"default"},"spec":{"containers":[{"env":[{"name":"DEMO_GREETING_ENV","value":"Hello from the environement"}],"image":"nginx","name":"nginx-container","ports":[{"containerPort":80}]},{"image":"redis","name":"redis-container"}]}}
    creationTimestamp: "2022-04-07T15:22:24Z"
    labels:
      app: nginx
      tier: dev
    name: multi-container-pod
    namespace: default
    resourceVersion: "68655"
    uid: 606e68b5-2f7c-4783-830a-eb4f3de44c74
  spec:
    containers:
    - env:
      - name: DEMO_GREETING_ENV
        value: Hello from the environement
      image: nginx
      imagePullPolicy: Always
      name: nginx-container
      ports:
      - containerPort: 80
        protocol: TCP
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-mthq5
        readOnly: true
    - image: redis
      imagePullPolicy: Always
      name: redis-container
      resources: {}
      terminationMessagePath: /dev/termination-log
      terminationMessagePolicy: File
      volumeMounts:
      - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
        name: kube-api-access-mthq5
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
    - name: kube-api-access-mthq5
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
      lastTransitionTime: "2022-04-07T15:22:24Z"
      status: "True"
      type: Initialized
    - lastProbeTime: null
      lastTransitionTime: "2022-04-07T15:22:33Z"
      status: "True"
      type: Ready
    - lastProbeTime: null
      lastTransitionTime: "2022-04-07T15:22:33Z"
      status: "True"
      type: ContainersReady
    - lastProbeTime: null
      lastTransitionTime: "2022-04-07T15:22:24Z"
      status: "True"
      type: PodScheduled
    containerStatuses:
    - containerID: docker://4f64e48a131a50a29421217b550f71755f9fd2f90b044d01f1905fd05552a5ab
      image: nginx:latest
      imageID: docker-pullable://nginx@sha256:2275af0f20d71b293916f1958f8497f987b8d8fd8113df54635f2a5915002bf1
      lastState: {}
      name: nginx-container
      ready: true
      restartCount: 0
      started: true
      state:
        running:
          startedAt: "2022-04-07T15:22:29Z"
    - containerID: docker://61e4cfb5e61648db0794e1acd137422888452445413fc2449c6f281537fec4b5
      image: redis:latest
      imageID: docker-pullable://redis@sha256:69a3ab2516b560690e37197b71bc61ba245aafe4525ebdece1d8a0bc5669e3e2
      lastState: {}
      name: redis-container
      ready: true
      restartCount: 0
      started: true
      state:
        running:
          startedAt: "2022-04-07T15:22:32Z"
    hostIP: 192.168.100.104
    phase: Running
    podIP: 10.244.171.83
    podIPs:
    - ip: 10.244.171.83
    qosClass: BestEffort
    startTime: "2022-04-07T15:22:24Z"
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```


## json output

```bash
root@master:~# kubectl get pods -o json

{
    "apiVersion": "v1",
    "items": [
        {
            "apiVersion": "v1",
            "kind": "Pod",
            "metadata": {
                "annotations": {
                    "cni.projectcalico.org/containerID": "c6bb6a7e0cc37b617eba71209d99e0560654e202136f5a577f31d1160299add5",
                    "cni.projectcalico.org/podIP": "10.244.171.83/32",
                    "cni.projectcalico.org/podIPs": "10.244.171.83/32",
                    "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"Pod\",\"metadata\":{\"annotations\":{},\"labels\":{\"app\":\"nginx\",\"tier\":\"dev\"},\"name\":\"multi-container-pod\",\"namespace\":\"default\"},\"spec\":{\"containers\":[{\"env\":[{\"name\":\"DEMO_GREETING_ENV\",\"value\":\"Hello from the environement\"}],\"image\":\"nginx\",\"name\":\"nginx-container\",\"ports\":[{\"containerPort\":80}]},{\"image\":\"redis\",\"name\":\"redis-container\"}]}}\n"
                },
                "creationTimestamp": "2022-04-07T15:22:24Z",
                "labels": {
                    "app": "nginx",
                    "tier": "dev"
                },
                "name": "multi-container-pod",
                "namespace": "default",
                "resourceVersion": "68655",
                "uid": "606e68b5-2f7c-4783-830a-eb4f3de44c74"
            },
            "spec": {
                "containers": [
                    {
                        "env": [
                            {
                                "name": "DEMO_GREETING_ENV",
                                "value": "Hello from the environement"
                            }
                        ],
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
                        "terminationMessagePolicy": "File",
                        "volumeMounts": [
                            {
                                "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                                "name": "kube-api-access-mthq5",
                                "readOnly": true
                            }
                        ]
                    },
                    {
                        "image": "redis",
                        "imagePullPolicy": "Always",
                        "name": "redis-container",
                        "resources": {},
                        "terminationMessagePath": "/dev/termination-log",
                        "terminationMessagePolicy": "File",
                        "volumeMounts": [
                            {
                                "mountPath": "/var/run/secrets/kubernetes.io/serviceaccount",
                                "name": "kube-api-access-mthq5",
                                "readOnly": true
                            }
                        ]
                    }
                ],
                "dnsPolicy": "ClusterFirst",
                "enableServiceLinks": true,
                "nodeName": "worker",
                "preemptionPolicy": "PreemptLowerPriority",
                "priority": 0,
                "restartPolicy": "Always",
                "schedulerName": "default-scheduler",
                "securityContext": {},
                "serviceAccount": "default",
                "serviceAccountName": "default",
                "terminationGracePeriodSeconds": 30,
                "tolerations": [
                    {
                        "effect": "NoExecute",
                        "key": "node.kubernetes.io/not-ready",
                        "operator": "Exists",
                        "tolerationSeconds": 300
                    },
                    {
                        "effect": "NoExecute",
                        "key": "node.kubernetes.io/unreachable",
                        "operator": "Exists",
                        "tolerationSeconds": 300
                    }
                ],
                "volumes": [
                    {
                        "name": "kube-api-access-mthq5",
                        "projected": {
                            "defaultMode": 420,
                            "sources": [
                                {
                                    "serviceAccountToken": {
                                        "expirationSeconds": 3607,
                                        "path": "token"
                                    }
                                },
                                {
                                    "configMap": {
                                        "items": [
                                            {
                                                "key": "ca.crt",
                                                "path": "ca.crt"
                                            }
                                        ],
                                        "name": "kube-root-ca.crt"
                                    }
                                },
                                {
                                    "downwardAPI": {
                                        "items": [
                                            {
                                                "fieldRef": {
                                                    "apiVersion": "v1",
                                                    "fieldPath": "metadata.namespace"
                                                },
                                                "path": "namespace"
                                            }
                                        ]
                                    }
                                }
                            ]
                        }
                    }
                ]
            },
            "status": {
                "conditions": [
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2022-04-07T15:22:24Z",
                        "status": "True",
                        "type": "Initialized"
                    },
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2022-04-07T15:22:33Z",
                        "status": "True",
                        "type": "Ready"
                    },
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2022-04-07T15:22:33Z",
                        "status": "True",
                        "type": "ContainersReady"
                    },
                    {
                        "lastProbeTime": null,
                        "lastTransitionTime": "2022-04-07T15:22:24Z",
                        "status": "True",
                        "type": "PodScheduled"
                    }
                ],
                "containerStatuses": [
                    {
                        "containerID": "docker://4f64e48a131a50a29421217b550f71755f9fd2f90b044d01f1905fd05552a5ab",
                        "image": "nginx:latest",
                        "imageID": "docker-pullable://nginx@sha256:2275af0f20d71b293916f1958f8497f987b8d8fd8113df54635f2a5915002bf1",
                        "lastState": {},
                        "name": "nginx-container",
                        "ready": true,
                        "restartCount": 0,
                        "started": true,
                        "state": {
                            "running": {
                                "startedAt": "2022-04-07T15:22:29Z"
                            }
                        }
                    },
                    {
                        "containerID": "docker://61e4cfb5e61648db0794e1acd137422888452445413fc2449c6f281537fec4b5",
                        "image": "redis:latest",
                        "imageID": "docker-pullable://redis@sha256:69a3ab2516b560690e37197b71bc61ba245aafe4525ebdece1d8a0bc5669e3e2",
                        "lastState": {},
                        "name": "redis-container",
                        "ready": true,
                        "restartCount": 0,
                        "started": true,
                        "state": {
                            "running": {
                                "startedAt": "2022-04-07T15:22:32Z"
                            }
                        }
                    }
                ],
                "hostIP": "192.168.100.104",
                "phase": "Running",
                "podIP": "10.244.171.83",
                "podIPs": [
                    {
                        "ip": "10.244.171.83"
                    }
                ],
                "qosClass": "BestEffort",
                "startTime": "2022-04-07T15:22:24Z"
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

## describe pod

```bash

root@master:~# kubectl describe pod multi-container-pod 
Name:         multi-container-pod
Namespace:    default
Priority:     0
Node:         worker/192.168.100.104
Start Time:   Thu, 07 Apr 2022 15:22:24 +0000
Labels:       app=nginx
              tier=dev
Annotations:  cni.projectcalico.org/containerID: c6bb6a7e0cc37b617eba71209d99e0560654e202136f5a577f31d1160299add5
              cni.projectcalico.org/podIP: 10.244.171.83/32
              cni.projectcalico.org/podIPs: 10.244.171.83/32
Status:       Running
IP:           10.244.171.83
IPs:
  IP:  10.244.171.83
Containers:
  nginx-container:
    Container ID:   docker://4f64e48a131a50a29421217b550f71755f9fd2f90b044d01f1905fd05552a5ab
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:2275af0f20d71b293916f1958f8497f987b8d8fd8113df54635f2a5915002bf1
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 07 Apr 2022 15:22:29 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      DEMO_GREETING_ENV:  Hello from the environement
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-mthq5 (ro)
  redis-container:
    Container ID:   docker://61e4cfb5e61648db0794e1acd137422888452445413fc2449c6f281537fec4b5
    Image:          redis
    Image ID:       docker-pullable://redis@sha256:69a3ab2516b560690e37197b71bc61ba245aafe4525ebdece1d8a0bc5669e3e2
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Thu, 07 Apr 2022 15:22:32 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-mthq5 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-mthq5:
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
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  11m   default-scheduler  Successfully assigned default/multi-container-pod to worker
  Normal  Pulling    11m   kubelet            Pulling image "nginx"
  Normal  Pulled     10m   kubelet            Successfully pulled image "nginx" in 3.204279813s
  Normal  Created    10m   kubelet            Created container nginx-container
  Normal  Started    10m   kubelet            Started container nginx-container
  Normal  Pulling    10m   kubelet            Pulling image "redis"
  Normal  Pulled     10m   kubelet            Successfully pulled image "redis" in 3.04616718s
  Normal  Created    10m   kubelet            Created container redis-container
  Normal  Started    10m   kubelet            Started container redis-container
```



## Edit pod

```bash
export KUBE_EDITOR='nano'

root@master:~# kubectl edit pod nginx-pod
```


## Show logs of a pod

```bash
root@master:~# k logs nginx-pod

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
root@master:~# k logs multi-container-pod redis-container

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


## TOP

```bash
kubectl top pods
kubectl top pods --sort-by cpu
kubectl top pods --sort-by memory
kubectl top pods --sort-by cpu -n kube-system > cpu.txt
kubectl top pods --sort-by memory --all-namespaces
```