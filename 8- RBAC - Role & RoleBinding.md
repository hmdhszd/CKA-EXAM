# Summary:

### First, we will create the test "user account" and "namespace"
### Then, We will create the Role with list of actions performed in a "specific namespace"
### And finally, We will assign this role to "user" by creating "RoleBinding"


##
##
# Creating Kubernetes test User Account(appuser) (using x509 for testing RBAC)

### Generating Key:

```bash
root@master:~# openssl genrsa -out appuser.key 2048

Generating RSA private key, 2048 bit long modulus (2 primes)
..............+++++
......................+++++
e is 65537 (0x010001)
```


### Generaing Certificate Signing request (csr):

```bash
root@master:~# openssl req -new -key appuser.key -out appuser.csr -subj "/CN=appuser"
```


### Singing CSR using K8s Cluster "Certificate" and "Key"

```bash
root@master:~# openssl x509 -req -in appuser.csr \
>         -CA /etc/kubernetes/pki/ca.crt \
>         -CAkey /etc/kubernetes/pki/ca.key \
>         -CAcreateserial \
>         -out appuser.crt -days 300
Signature ok
subject=CN = appuser
Getting CA Private Key
```


### Adding user credentials to "kubeconfig" file

```bash
root@master:~# kubectl config set-credentials appuser  --client-certificate=appuser.crt --client-key=appuser.key

User "appuser" set.
```


### Creating context for this user and associating it with our cluster:

```bash
root@master:~# kubectl config set-context appuser-context --cluster=kubernetes --user=appuser

Context "appuser-context" created.
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
    user: appuser
  name: appuser-context
- context:
    cluster: kubernetes
    namespace: default
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: appuser
  user:
    client-certificate: /root/appuser.crt
    client-key: /root/appuser.key
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
```

## 
## 
# Creating Namespaces and Pod for testing RBAC:

### Creating test Namespace:
```bash
root@master:~# kubectl create ns dev-ns

namespace/dev-ns created
```


### Creating test Pod:
```bash
root@master:~# kubectl run nginx-pod --image=nginx -n dev-ns
pod/nginx-pod created


root@master:~# kubectl get pods -n dev-ns
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          7s

```


### Test Before Deploying:
```bash
root@master:~# kubectl get pods -n dev-ns --user=appuser 
Error from server (Forbidden): pods is forbidden: User "appuser" cannot list resource "pods" in API group "" in the namespace "dev-ns"
```



## 
## 
# Creating a "Role" & "RoleBinding":

### Creating Resources Declaratively (Using YAML):
```bash
root@master:~# cat <<EOF >>Role-RoleBinding.yml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  namespace: dev-ns
  name: pod-reader
rules:
- apiGroups: [""] 
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: read-pods
  namespace: dev-ns
subjects:
- kind: User
  name: appuser 
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role 
  name: pod-reader 
  apiGroup: rbac.authorization.k8s.io
EOF


root@master:~# kubectl apply -f Role-RoleBinding.yml

```


### Creating Resources Imperatively (Commands):
```bash
root@master:~# kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods --namespace=dev-ns

role.rbac.authorization.k8s.io/pod-reader created



root@master:~# kubectl create rolebinding read-pods --role=pod-reader --user=appuser --namespace=dev-ns

rolebinding.rbac.authorization.k8s.io/read-pods created
```


### Display Role and RoleBinding:
```bash
root@master:~# kubectl get role -n dev-ns

NAME         CREATED AT
pod-reader   2022-04-14T16:26:25Z
```



```bash
root@master:~# kubectl get rolebinding -n dev-ns

NAME        ROLE              AGE
read-pods   Role/pod-reader   12s
```



```bash
root@master:~# kubectl describe role -n dev-ns

Name:         pod-reader
Labels:       <none>
Annotations:  <none>
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  pods       []                 []              [get list watch]
```



```bash
root@master:~# kubectl describe rolebinding -n dev-ns

Name:         read-pods
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  Role
  Name:  pod-reader
Subjects:
  Kind  Name     Namespace
  ----  ----     ---------
  User  appuser  
```



## 
## 
# Testing RBAC:

### Pod Operations: get, list, watch - in "dev-ns" namespace:

```bash
root@master:~# kubectl auth can-i get pods -n dev-ns --user=appuser

yes


root@master:~# kubectl auth can-i list pods -n dev-ns --user=appuser
yes
```



```bash
root@master:~# kubectl get pod nginx-pod -n dev-ns --user=appuser
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          3h20m


root@master:~# kubectl get pods -n dev-ns --user=appuser
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          3h20m

```


### Pod Operations: get, list, watch - in "NON dev-ns" namespace:

```bash
root@master:~# kubectl auth can-i get pods -n kube-system --user=appuser
no


root@master:~# kubectl auth can-i list pods -n kube-system --user=appuser
no


root@master:~# kubectl auth can-i watch pods -n kube-system --user=appuser
no
```



```bash
root@master:~# kubectl get pods --user=appuser # queries default namespace
Error from server (Forbidden): pods is forbidden: User "appuser" cannot list resource "pods" in API group "" in the namespace "default"


root@master:~# kubectl get pods -n kube-system --user=appuser
Error from server (Forbidden): pods is forbidden: User "appuser" cannot list resource "pods" in API group "" in the namespace "kube-system"
```


### Creating Objects in "dev-ns" namespace: 

```bash
root@master:~# kubectl auth can-i create pods -n dev-ns --user=appuser
no


root@master:~# kubectl auth can-i create services -n dev-ns --user=appuser
no


root@master:~# kubectl auth can-i create deployments -n dev-ns --user=appuser
no
```



```bash
root@master:~# kubectl run redis-pod -n dev-ns --image=redis --user=appuser
Error from server (Forbidden): pods is forbidden: User "appuser" cannot create resource "pods" in API group "" in the namespace "dev-ns"


root@master:~# kubectl create deploy redis-deploy -n dev-ns --image=redis --user=appuser
error: failed to create deployment: deployments.apps is forbidden: User "appuser" cannot create resource "deployments" in API group "apps" in the namespace "dev-ns"
```


### Deleting Objects in "dev-ns" namespace: 
```bash
root@master:~# kubectl auth can-i delete pods -n dev-ns --user=appuser
no
root@master:~# kubectl auth can-i delete services -n dev-ns --user=appuser
no
root@master:~# kubectl auth can-i delete deployments -n dev-ns --user=appuser
no
```



```bash
root@master:~# kubectl delete pods nginx-pod -n dev-ns --user=appuser
Error from server (Forbidden): pods "nginx-pod" is forbidden: User "appuser" cannot delete resource "pods" in API group "" in the namespace "dev-ns"
```

#
#
### The difference between a Role and a ClusterRole is the scope:
in a Role, the rules are applicable to a single namespace, whereas a ClusterRole is cluster-wide, so the rules are applicable to more than one namespace. ClusterRoles can define rules for cluster-scoped resources (such as nodes) as well.
#
