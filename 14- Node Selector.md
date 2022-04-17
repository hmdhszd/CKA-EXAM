# First, we should label the nodes:



Before labeling:

```bash
root@master:~# kubectl get nodes --show-labels

NAME     STATUS   ROLES                  AGE   VERSION   LABELS

master   Ready    control-plane,master   9d    v1.23.5   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=master,kubernetes.io/os=linux,node-role.kubernetes.io/control-plane=,node-role.kubernetes.io/master=,node.kubernetes.io/exclude-from-external-load-balancers=

worker   Ready    <none>                 9d    v1.23.5   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=worker,kubernetes.io/os=linux
```





Labeling Node:

```bash
root@master:~# kubectl label nodes worker disktype=ssd

node/worker labeled
```


After labeling:
```bash
root@master:~# kubectl get nodes --show-labels

NAME     STATUS   ROLES                  AGE   VERSION   LABELS

master   Ready    control-plane,master   9d    v1.23.5   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,kubernetes.io/arch=amd64,kubernetes.io/hostname=master,kubernetes.io/os=linux,node-role.kubernetes.io/control-plane=,node-role.kubernetes.io/master=,node.kubernetes.io/exclude-from-external-load-balancers=

worker   Ready    <none>                 9d    v1.23.5   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/os=linux,disktype=ssd,kubernetes.io/arch=amd64,kubernetes.io/hostname=worker,kubernetes.io/os=linux
```








# Then we should Deploy a pod for testing Node-Selector:

```bash
root@master:~# cat <<EOF >>nodeSelector-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nodeselector-pod
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
EOF
```

```bash
root@master:~# kubectl apply -f nodeSelector-pod.yaml

pod/nodeselector-pod created
```


## Check:

```bash
root@master:~# kubectl get pods -o wide

NAME               READY   STATUS    RESTARTS   AGE     IP              NODE     NOMINATED NODE   READINESS GATES
nodeselector-pod   1/1     Running   0          2m59s   10.244.171.82   worker   <none>           <none>
```



## Delete tag

```bash
root@master:~# kubectl label nodes worker disktype-

node/worker unlabeled
```