# Worker node

### Create an index.html file on your Worker Node

```bash
root@worker:~# sudo mkdir /mnt/data


root@worker:~# sudo sh -c "echo 'Hello from Kubernetes storage in the worker node' > /mnt/data/index.html"


root@worker:~# cat /mnt/data/index.html
Hello from Kubernetes storage in the worker node
```



# Master node

### Create a PersistentVolume

```bash
root@master:~# cat <<EOF >>pv-volume.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
EOF
```

Apply and Validate:

```bash
root@master:~# kubectl apply -f pv-volume.yaml

persistentvolume/task-pv-volume created



root@master:~# kubectl get pv task-pv-volume

NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
task-pv-volume   10Gi       RWO            Retain           Available           manual                  10s
```


### Create a PersistentVolumeClaim 

```bash
root@master:~# cat <<EOF >>pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
EOF
```


Apply and Validate:

```bash
root@master:~# kubectl apply -f pv-claim.yaml 
persistentvolumeclaim/task-pv-claim created



root@master:~# kubectl get pvc
NAME            STATUS   VOLUME           CAPACITY   ACCESS MODES   STORAGECLASS   AGE
task-pv-claim   Bound    task-pv-volume   10Gi       RWO            manual         8s



root@master:~# kubectl get pv
NAME             CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                   STORAGECLASS   REASON   AGE
task-pv-volume   10Gi       RWO            Retain           Bound    default/task-pv-claim   manual                  2m58s
```


### Create a Pod

```bash
root@master:~# cat <<EOF >>pv-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
EOF
```



```bash
root@master:~# kubectl apply -f pv-pod.yaml

pod/task-pv-pod created




root@master:~# kubectl get pod task-pv-pod -o wide

NAME          READY   STATUS    RESTARTS   AGE   IP              NODE     NOMINATED NODE   READINESS GATES
task-pv-pod   1/1     Running   0          11s   10.244.171.97   worker   <none>           <none>
```


## Test from master node:


```bash
root@master:~# kubectl exec -it task-pv-pod -- /bin/bash


root@task-pv-pod:/# curl localhost
Hello from Kubernetes storage in the worker node


root@task-pv-pod:/# sh -c "echo 'Hello from Kubernetes storage inside pod in master node' > /usr/share/nginx/html/index.html"
```


## Test from worker node:

```bash
root@worker:~# cat /mnt/data/index.html

Hello from Kubernetes storage inside pod in master node
```
