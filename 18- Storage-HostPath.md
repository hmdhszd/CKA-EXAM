# We will create a Pod with HostPath volume.


```bash
root@master:~# cat <<EOF >>NGINX-HostPath-Pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-hostpath
spec:
  containers:
    - name: nginx-container
      image: nginx
      volumeMounts:
      - mountPath: /test-mnt
        name: test-vol
  volumes:
  - name: test-vol
    hostPath:
      path: /test-vol
EOF
```




```bash
root@master:~# kubectl apply -f NGINX-HostPath-Pod.yml

pod/nginx-hostpath created
```




```bash
root@master:~# kubectl get pods -o wide

NAME             READY   STATUS    RESTARTS   AGE   IP              NODE     NOMINATED NODE   READINESS GATES
nginx-hostpath   1/1     Running   0          21s   10.244.171.76   worker   <none>           <none>
```




```bash
root@master:~# kubectl exec nginx-hostpath -- df /test-mnt

Filesystem                        1K-blocks    Used Available Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv  13352008 7776684   4919524  62% /test-mnt
```



# Testing


## In the Worker node:

First, will create the file on the host-path on the worker node where this pod is running.

```bash
root@worker:~# echo "Hello from Host" > /test-vol/from-host.txt


root@worker:~# cat /test-vol/from-host.txt
Hello from Host
```



## In the Master node:

Next, we will login to the Pod and will create the test file on the host-path directory from inside the Pod.

```bash
root@master:~# kubectl exec nginx-hostpath -it -- /bin/sh

# echo "Hello from Pod" > /test-mnt/from-pod.txt

# cat /test-mnt/from-pod.txt
Hello from Pod

# ls /test-mnt/            
from-host.txt  from-pod.txt
```



## In the Worker node:

Finally, we will validate that file from the worker node.

```bash
root@worker:~# ls /test-vol/
from-host.txt  from-pod.txt

root@worker:~# cat /test-vol/from-pod.txt 
Hello from Pod
```