## Creating Configmap Declaratively (Using YAML file):

Example-1:

```bash
root@master:~# cat <<EOF >>my-env-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config-yaml
data:
  ENV_ONE: "va1ue1" 
  ENV_TWO: "va1ue2"
EOF
```

```bash
root@master:~# kubectl apply -f my-env-config.yaml

configmap/env-config-yaml created
```

Example-2:

```bash
root@master:~# cat <<EOF >>my-nginx-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-nginx-config-yaml
data:
  my-nginx-config.conf: |-
    server {
      listen 80;
      server_name www.config-map-example.com;
      gzip on;
      gzip_types text/plain application/xml;
      location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
      }
    }
    sleep-interval: 25
EOF
```

```bash
root@master:~# kubectl apply -f my-nginx-config.yaml

configmap/my-nginx-config-yaml created
```



```bash
root@master:~# kubectl apply -f multi-container-pod.yaml 

pod/multi-container-pod created
```



```bash
root@master:~# kubectl get -f my-env-config.yaml 
NAME              DATA   AGE
env-config-yaml   2      2m20s


root@master:~# kubectl get -f my-nginx-config.yaml 
NAME                   DATA   AGE
my-nginx-config-yaml   1      91s
```




## Edit a Configmap with yaml file

```bash
root@master:~# kubectl edit -f my-nginx-config.yaml

configmap/my-nginx-config-yaml edited
```



## Describe a Configmap with yaml file

```bash
root@master:~# kubectl describe -f my-env-config.yaml

Name:         env-config-yaml
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
ENV_ONE:
----
va1ue1
ENV_TWO:
----
va1ue2

BinaryData
====

Events:  <none>



 
root@master:~# kubectl describe -f my-nginx-config.yaml

Name:         my-nginx-config-yaml
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
my-nginx-config.conf:
----
server {
  listen 80;
  server_name www.my-config-map-example.com;
  gzip on;
  gzip_types text/plain application/xml;
  location / {
    root /usr/share/nginx/html;
    index index.html index.htm;
  }
}
sleep-interval: 25

BinaryData
====

Events:  <none>
```


## Delete a Configmap with yaml file

```bash
root@master:~# kubectl delete -f my-env-config.yaml 
configmap "env-config-yaml" deleted


root@master:~# kubectl delete -f my-nginx-config.yaml 
configmap "my-nginx-config-yaml" deleted
```




## Create a Configmap from Command line

From Literal value:

```bash
root@master:~# kubectl create configmap env-config-cmd --from-literal=ENV_ONE=value1 --from-literal=ENV_TWO=value2

configmap/env-config-cmd created
```

From File:

```bash
root@master:~# echo "ValueOne in the file" > ENV_ONE

root@master:~# kubectl create configmap my-config-from-file --from-file=/root/ENV_ONE
configmap/my-config-from-file created
```


```bash
root@master:~# kubectl get cm

NAME                  DATA   AGE
env-config-cmd        2      18s
my-config-from-file   1      35s
```


## YAML output

```bash
root@master:~# kubectl get cm env-config-cmd -o yaml

apiVersion: v1
data:
  ENV_ONE: value1
  ENV_TWO: value2
kind: ConfigMap
metadata:
  creationTimestamp: "2022-04-16T13:27:56Z"
  name: env-config-cmd
  namespace: default
  resourceVersion: "216140"
  uid: 96e61307-3d86-48b1-a774-2fa242f75982
```

## Print all Configmaps from all name spaces: 
```bash
root@master:~# kubectl get cm -A
NAMESPACE              NAME                                 DATA   AGE
default                env-config-cmd                       2      2m14s
default                kube-root-ca.crt                     1      9d
dev-ns                 kube-root-ca.crt                     1      2d
kube-node-lease        kube-root-ca.crt                     1      9d
kube-public            cluster-info                         1      9d
kube-public            kube-root-ca.crt                     1      9d
kube-system            calico-config                        4      9d
kube-system            coredns                              1      9d
kube-system            extension-apiserver-authentication   6      9d
kube-system            kube-proxy                           2      9d
kube-system            kube-root-ca.crt                     1      9d
kube-system            kubeadm-config                       1      9d
kube-system            kubelet-config-1.23                  1      9d
kubernetes-dashboard   kube-root-ca.crt                     1      9d
kubernetes-dashboard   kubernetes-dashboard-settings        0      9d
qa-namespace           kube-root-ca.crt                     1      2d5h
test-ns2               kube-root-ca.crt                     1      9h
tigera-operator        kube-root-ca.crt                     1      9d
tigera-operator        operator-lock                        0      9d
```


## Describe a Configmap

```bash
root@master:~# kubectl describe cm env-config-cmd 
Name:         env-config-cmd
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
ENV_ONE:
----
value1
ENV_TWO:
----
value2

BinaryData
====

Events:  <none>
```



## Edit a Configmap

```bash
export KUBE_EDITOR='nano'


root@master:~# kubectl edit cm env-config-cmd
configmap/env-config-cmd edited
```


## Delete a Configmap

```bash
root@master:~# kubectl delete cm env-config-cmd

configmap "env-config-cmd" deleted
```



# Injecting ConfigMap into Pods

## 1- As Environment Variables
## 2- As Arguments
## 3- As Files inside Volume


##

## 1- Injecting ConfigMap into Pod As Environment Variables

```bash
root@master:~# cat <<EOF >>cm-pod-env.yaml

apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-env
spec:
  containers:
    - name: test-container
      image: nginx
      env:
      - name: ENV_VARIABLE_1
        valueFrom:
          configMapKeyRef:
            name: env-config-yaml
            key: ENV_ONE
      - name: ENV_VARIABLE_2
        valueFrom:
          configMapKeyRef:
            name: env-config-yaml
            key: ENV_TWO
  restartPolicy: Never

EOF
```



Apply and Validate:

```bash
root@master:~# kubectl apply -f cm-pod-env.yaml
pod/cm-pod-env created


root@master:~# kubectl exec cm-pod-env -- env | grep ENV
ENV_VARIABLE_1=va1ue1
ENV_VARIABLE_2=va1ue2
```


## 2- Injecting ConfigMap into Pod As Arguments

```bash
root@master:~# cat <<EOF >>cm-pod-arg.yaml

apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-arg
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "echo \$(ENV_VARIABLE_1) and \$(ENV_VARIABLE_2)" ]
      env:
      - name: ENV_VARIABLE_1
        valueFrom:
          configMapKeyRef:
            name: env-config-yaml
            key: ENV_ONE
      - name: ENV_VARIABLE_2
        valueFrom:
          configMapKeyRef:
            name: env-config-yaml
            key: ENV_TWO
  restartPolicy: Never

EOF
```


Apply and Validate:

```bash
root@master:~# kubectl apply -f cm-pod-arg.yaml
pod/cm-pod-arg created


root@master:~# kubectl logs cm-pod-arg
value1 and value2
```


## 3- Injecting ConfigMap into As Files inside Volume

```bash
root@master:~# cat <<EOF >>cm-pod-file-vol.yaml
apiVersion: v1
kind: Pod
metadata:
  name: cm-pod-file-vol
spec:
  volumes:
    - name: mapvol
      configMap:
        name: my-nginx-config-yaml
  containers:
    - name: test-container
      image: nginx
      volumeMounts:
      - name: mapvol
        mountPath: /etc/config
  restartPolicy: Never
EOF
```




Apply and Validate:

```bash
root@master:~# kubectl apply -f cm-pod-file-vol.yaml 
pod/cm-pod-file-vol created



root@master:~# kubectl exec cm-pod-file-vol -- ls /etc/config
my-nginx-config.conf



root@master:~# kubectl exec cm-pod-file-vol -- cat /etc/config/etc/config/my-nginx-config.conf

cat: /etc/config/etc/config/my-nginx-config.conf: No such file or directory
command terminated with exit code 1
root@master:~# kubectl exec cm-pod-file-vol -- cat /etc/config/my-nginx-config.conf
server {
  listen 80;
  server_name www.config-map-example.com;
  gzip on;
  gzip_types text/plain application/xml;
  location / {
    root /usr/share/nginx/html;
    index index.html index.htm;
  }
}
```