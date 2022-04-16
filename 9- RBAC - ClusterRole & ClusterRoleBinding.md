# Summary:

### First, we will create the test "user account" for testing
### Next,  We will create the "ClusterRole" with list of actions performed "across all namespaces"
### After that, We will assign this ClusterRole to "user" by creating "ClusterRoleBinding"
### Finally, we will test the above configuration by deploying sample applications.


##
##
# Creating Kubernetes test User Account(appmonitor) (using x509 for testing RBAC)




### Generating Key

```bash
root@master:~# openssl genrsa -out appmonitor.key 2048

Generating RSA private key, 2048 bit long modulus (2 primes)
.....+++++
...................................+++++
e is 65537 (0x010001)
```



### Generaing Certificate Signing request (csr):

```bash
root@master:~# openssl req -new -key appmonitor.key -out appmonitor.csr -subj "/CN=appmonitor"
```



### Singing CSR using K8s Cluster "Certificate" and "Key"

```bash
root@master:~# openssl x509 -req -in appmonitor.csr \
>         -CA /etc/kubernetes/pki/ca.crt \
>         -CAkey /etc/kubernetes/pki/ca.key \
>         -CAcreateserial \
>         -out appmonitor.crt -days 300
Signature ok
subject=CN = appmonitor
Getting CA Private Key
```



### Adding user credentials to "kubeconfig" file

```bash
root@master:~# kubectl config set-credentials appmonitor  --client-certificate=appmonitor.crt --client-key=appmonitor.key

User "appmonitor" set.
```



### Creating context for this user and associating it with our cluster:

```bash
root@master:~# kubectl config set-context appmonitor-context --cluster=kubernetes --user=appmonitor

Context "appmonitor-context" created.
```



### Displaying K8s Cluster Config

```bash
root@master:~# kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://192.168.100.103:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: appmonitor
  name: appmonitor-context
- context:
    cluster: kubernetes
    namespace: default
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: appmonitor
  user:
    client-certificate: /root/appmonitor.crt
    client-key: /root/appmonitor.key
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```


##
##
# Creating Namespaces and Pod for testing RBAC:

### Creating TEST Namespaces:

```bash
root@master:~# kubectl create ns test-ns1
namespace/test-ns1 created


root@master:~# kubectl create ns test-ns2
namespace/test-ns2 created
```



### Creating test Pods:

```bash
root@master:~# kubectl run nginx-pod-default --image=nginx
pod/nginx-pod-default created

root@master:~# kubectl run redis-pod-ns1 --image=redis -n test-ns1
pod/redis-pod-ns1 created

root@master:~# kubectl run httpd-pod-ns2 --image=busybox -n test-ns2
pod/httpd-pod-ns2 created
```



### Test Before Deploying:

```bash
root@master:~# kubectl get pods --user=appmonitor
Error from server (Forbidden): pods is forbidden: User "appmonitor" cannot list resource "pods" in API group "" in the namespace "default"


root@master:~# kubectl get pods -n test-ns1 --user=appmonitor
Error from server (Forbidden): pods is forbidden: User "appmonitor" cannot list resource "pods" in API group "" in the namespace "test-ns1"


root@master:~# kubectl get pods -n test-ns2 --user=appmonitor 
Error from server (Forbidden): pods is forbidden: User "appmonitor" cannot list resource "pods" in API group "" in the namespace "test-ns2"


root@master:~# kubectl get pods -n kube-system --user=appmonitor
Error from server (Forbidden): pods is forbidden: User "appmonitor" cannot list resource "pods" in API group "" in the namespace "kube-system"


root@master:~# kubectl get pods -A --user=appmonitor
Error from server (Forbidden): pods is forbidden: User "appmonitor" cannot list resource "pods" in API group "" at the cluster scope
```


##
##
# Creating a "ClusterRole" & "ClusterRoleBinding":


### Creating Resources Declaratively (Using YAML):

```bash
root@master:~# cat <<EOF >>ClusterRole-ClusterRoleBinding.yml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: clusterrole-monitoring
rules:
- apiGroups: [""] 
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: clusterrole-binding-monitoring
subjects:
- kind: User
  name: appmonitor 
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole 
  name: clusterrole-monitoring
  apiGroup: rbac.authorization.k8s.io
EOF
```



### Creating Resources Imperatively (Commands):

```bash
root@master:~# kubectl create clusterrole clusterrole-monitoring --verb=get,list,watch --resource=pods
clusterrole.rbac.authorization.k8s.io/clusterrole-monitoring created


root@master:~# kubectl create clusterrolebinding clusterrole-binding-monitoring --clusterrole=clusterrole-monitoring --user=appmonitor
clusterrolebinding.rbac.authorization.k8s.io/clusterrole-binding-monitoring created
```


##
##
# Display ClusterRole and ClusterRoleBinding:


```bash
root@master:~# kubectl get clusterrole | grep clusterrole-monitoring
clusterrole-monitoring                                                 2022-04-16T04:13:46Z


root@master:~# kubectl get clusterrolebinding | grep clusterrole-binding-monitoring
clusterrole-binding-monitoring                         ClusterRole/clusterrole-monitoring                                                 65s


root@master:~# kubectl describe clusterrole clusterrole-monitoring
Name:         clusterrole-monitoring
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  pods       []                 []              [get list watch]


root@master:~# kubectl describe clusterrolebinding clusterrole-binding-monitoring
Name:         clusterrole-binding-monitoring
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  clusterrole-monitoring
Subjects:
  Kind  Name        Namespace
  ----  ----        ---------
  User  appmonitor
```

##
##
# Testing ClusterRole & ClusterRoleBinding:

### Pod Operations: get, list, watch - in "kube-system", "default", "test-ns1", and "test-ns2" namespaces:

```bash
root@master:~# kubectl auth can-i get pods -n kube-system --user=appmonitor
yes


root@master:~# kubectl auth can-i get pods -n default --user=appmonitor
yes


root@master:~# kubectl auth can-i get pods -n test-ns1 --user=appmonitor
yes


root@master:~# kubectl auth can-i get pods -n test-ns2 --user=appmonitor
yes


root@master:~# kubectl get pods -n kube-system --user=appmonitor
NAME                                       READY   STATUS    RESTARTS       AGE
calico-kube-controllers-56fcbf9d6b-zkhc2   1/1     Running   58 (14h ago)   8d
calico-node-5hfv2                          1/1     Running   4 (14h ago)    8d
calico-node-wb5cc                          1/1     Running   6 (14h ago)    8d
coredns-64897985d-bzhgt                    1/1     Running   4 (14h ago)    8d
coredns-64897985d-jv568                    1/1     Running   4 (14h ago)    8d
etcd-master                                1/1     Running   13 (34m ago)   8d
kube-apiserver-master                      1/1     Running   47 (34m ago)   8d
kube-controller-manager-master             1/1     Running   11 (34m ago)   8d
kube-proxy-g7wcd                           1/1     Running   6 (46m ago)    8d
kube-proxy-z6zpg                           1/1     Running   4 (14h ago)    8d
kube-scheduler-master                      1/1     Running   11 (34m ago)   8d
metrics-server-847dcc659d-9sjvh            0/1     Running   4 (14h ago)    8d


root@master:~# kubectl get pods -n default --user=appmonitor
NAME                READY   STATUS    RESTARTS   AGE
nginx-pod-default   1/1     Running   0          7m1s


root@master:~# kubectl get pods -n test-ns1 --user=appmonitor
NAME            READY   STATUS    RESTARTS   AGE
redis-pod-ns1   1/1     Running   0          7m1s


root@master:~# kubectl get pods -n test-ns2 --user=appmonitor
NAME            READY   STATUS             RESTARTS        AGE
httpd-pod-ns2   0/1     CrashLoopBackOff   5 (2m29s ago)   7m
```



### Creating Objects in "default" (or in any other) namespace: 

```bash
root@master:~# kubectl auth can-i create pods --user=appmonitor
no


root@master:~# kubectl auth can-i create services --user=appmonitor
no


root@master:~# kubectl auth can-i create deployments --user=appmonitor
no


root@master:~# kubectl run redis-pod --image=redis --user=appmonitor
Error from server (Forbidden): pods is forbidden: User "appmonitor" cannot create resource "pods" in API group "" in the namespace "default"


root@master:~# kubectl create deploy redis-deploy --image=redis --user=appmonitor
error: failed to create deployment: deployments.apps is forbidden: User "appmonitor" cannot create resource "deployments" in API group "apps" in the namespace "default"
```



### Deleting Objects in "default" (or in any other) namespace: 


```bash
root@master:~# kubectl auth can-i delete pods --user=appmonitor
no


root@master:~# kubectl auth can-i delete services --user=appmonitor
no


root@master:~# kubectl auth can-i delete deployments --user=appmonitor
no


root@master:~# kubectl delete pods nginx-pod --user=appmonitor
Error from server (Forbidden): pods "nginx-pod" is forbidden: User "appmonitor" cannot delete resource "pods" in API group "" in the namespace "default"
```