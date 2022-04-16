## Create a Deployment with yaml file

```bash
root@master:~# cat <<EOF >>nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2
      maxUnavailable: 1
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      name: nginx-pod
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-container
        image: nginx
        ports:
        - containerPort: 80
EOF
```


```bash
root@master:~# k apply -f nginx-deployment.yaml

deployment.apps/nginx-deployment created
```



```bash
root@master:~# kubectl get -f nginx-deployment.yaml

NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           3m5s
```


```bash
root@master:~# k get rs -l app=nginx-app

NAME                          DESIRED   CURRENT   READY   AGE
nginx-deployment-55d7578c57   3         3         3       95s
```


```bash
root@master:~# k get pods -l app=nginx-app

NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-55d7578c57-7nv8n   1/1     Running   0          101s
nginx-deployment-55d7578c57-fdcvd   1/1     Running   0          101s
nginx-deployment-55d7578c57-t5qx7   1/1     Running   0          101s
```




## Edit a Deployment with yaml file

```bash
root@master:~# kubectl edit -f nginx-deployment.yaml

deployment.apps/nginx-deployment edited
```



## Describe a Deployment with yaml file

```bash
root@master:~# kubectl describe -f nginx-deployment.yaml 
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sat, 16 Apr 2022 06:03:59 +0000
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-app
Replicas:               4 desired | 4 updated | 4 total | 4 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 2 max surge
Pod Template:
  Labels:  app=nginx-app
  Containers:
   nginx-container:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-55d7578c57 (4/4 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  5m6s  deployment-controller  Scaled up replica set nginx-deployment-55d7578c57 to 3
  Normal  ScalingReplicaSet  74s   deployment-controller  Scaled up replica set nginx-deployment-55d7578c57 to 4

```


## Delete a Deployment with yaml file

```bash
root@master:~# kubectl delete -f nginx-deployment.yaml

deployment.apps "nginx-deployment" deleted
```




## Create a Deployment with kubectl run command


```bash
root@master:~# kubectl create deployment nginx-deployment --image=nginx --replicas=3

deployment.apps/nginx-deployment created
```

```bash
root@master:~# kubectl get deployments

NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           70s
```


```bash
root@master:~# kubectl get deployments -o wide

NAME               READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS   IMAGES   SELECTOR
nginx-deployment   3/3     3            3           3m57s   nginx        nginx    app=nginx-deployment
```

## Print Deployment with labels

```bash
root@master:~# kubectl get deployments --show-labels

NAME               READY   UP-TO-DATE   AVAILABLE   AGE     LABELS
nginx-deployment   3/3     3            3           4m32s   app=nginx-deployment
```

```bash
root@master:~# kubectl get deployments -l app=nginx-deployment

NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           5m26s
```



## YAML output

```bash
root@master:~# kubectl get deployments nginx-deployment -o yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2022-04-16T06:24:33Z"
  generation: 1
  labels:
    app: nginx-deployment
  name: nginx-deployment
  namespace: default
  resourceVersion: "177401"
  uid: 3670c88a-02c6-408e-971c-c729a143e9d1
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx-deployment
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx-deployment
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
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
  conditions:
  - lastTransitionTime: "2022-04-16T06:24:47Z"
    lastUpdateTime: "2022-04-16T06:24:47Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2022-04-16T06:24:33Z"
    lastUpdateTime: "2022-04-16T06:24:47Z"
    message: ReplicaSet "nginx-deployment-794f656f8b" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 3
  replicas: 3
  updatedReplicas: 3
```


## json output

```bash
root@master:~# kubectl get deployments nginx-deployment -o json
{
    "apiVersion": "apps/v1",
    "kind": "Deployment",
    "metadata": {
        "annotations": {
            "deployment.kubernetes.io/revision": "1"
        },
        "creationTimestamp": "2022-04-16T06:24:33Z",
        "generation": 1,
        "labels": {
            "app": "nginx-deployment"
        },
        "name": "nginx-deployment",
        "namespace": "default",
        "resourceVersion": "177401",
        "uid": "3670c88a-02c6-408e-971c-c729a143e9d1"
    },
    "spec": {
        "progressDeadlineSeconds": 600,
        "replicas": 3,
        "revisionHistoryLimit": 10,
        "selector": {
            "matchLabels": {
                "app": "nginx-deployment"
            }
        },
        "strategy": {
            "rollingUpdate": {
                "maxSurge": "25%",
                "maxUnavailable": "25%"
            },
            "type": "RollingUpdate"
        },
        "template": {
            "metadata": {
                "creationTimestamp": null,
                "labels": {
                    "app": "nginx-deployment"
                }
            },
            "spec": {
                "containers": [
                    {
                        "image": "nginx",
                        "imagePullPolicy": "Always",
                        "name": "nginx",
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
        "conditions": [
            {
                "lastTransitionTime": "2022-04-16T06:24:47Z",
                "lastUpdateTime": "2022-04-16T06:24:47Z",
                "message": "Deployment has minimum availability.",
                "reason": "MinimumReplicasAvailable",
                "status": "True",
                "type": "Available"
            },
            {
                "lastTransitionTime": "2022-04-16T06:24:33Z",
                "lastUpdateTime": "2022-04-16T06:24:47Z",
                "message": "ReplicaSet \"nginx-deployment-794f656f8b\" has successfully progressed.",
                "reason": "NewReplicaSetAvailable",
                "status": "True",
                "type": "Progressing"
            }
        ],
        "observedGeneration": 1,
        "readyReplicas": 3,
        "replicas": 3,
        "updatedReplicas": 3
    }
}

```


## print Deployment from all name spaces: 
```bash
root@master:~# kubectl get deployments -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            3/3     3            3           10m
kube-system            calico-kube-controllers     1/1     1            1           8d
kube-system            coredns                     2/2     2            2           8d
kube-system            metrics-server              0/1     1            0           8d
kubernetes-dashboard   dashboard-metrics-scraper   1/1     1            1           8d
kubernetes-dashboard   kubernetes-dashboard        1/1     1            1           8d
tigera-operator        tigera-operator             1/1     1            1           8d

```



## describe Deployment

```bash
root@master:~# kubectl describe deployments nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sat, 16 Apr 2022 06:24:33 +0000
Labels:                 app=nginx-deployment
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx-deployment
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx-deployment
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-deployment-794f656f8b (3/3 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  11m   deployment-controller  Scaled up replica set nginx-deployment-794f656f8b to 3

```



## Edit Deployment

```bash
export KUBE_EDITOR='nano'

root@master:~# kubectl edit deployments nginx-deployment

deployment.apps/nginx-deployment edited
```



## Delete a Deployment

```bash
root@master:~# kubectl delete deployments nginx-deployment

deployment.apps "nginx-deployment" delete
```
