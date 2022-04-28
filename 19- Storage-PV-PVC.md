### Create an index.html file on your Master Node

```bash
root@master:~# sudo mkdir /mnt/data


root@master:~# sudo sh -c "echo 'Hello from Kubernetes storage' > /mnt/data/index.html"


root@master:~# cat /mnt/data/index.html
Hello from Kubernetes storage

```



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

Apply anv Validate:

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


Apply anv Validate:

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



