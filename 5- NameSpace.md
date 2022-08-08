## Create a NameSpace with yaml file

```bash
root@master:~# cat <<EOF >>dev-ns.yaml

apiVersion: v1
kind: Namespace
metadata:
  name: dev
EOF

root@master:~# cat <<EOF >>qa-ns.yaml

apiVersion: v1
kind: Namespace
metadata:
  name: qa
EOF

root@master:~# cat <<EOF >>prod-ns.yaml

apiVersion: v1
kind: Namespace
metadata:
  name: prod
EOF

```


```bash
root@master:~# kubectl apply -f dev-ns.yaml 
namespace/dev created

root@master:~# kubectl apply -f qa-ns.yaml 
namespace/qa created

root@master:~# kubectl apply -f prod-ns.yaml 
namespace/prod created

```



```bash
root@master:~# kubectl get -f dev-ns.yaml 
NAME   STATUS   AGE
dev    Active   34s

root@master:~# kubectl get -f qa-ns.yaml 
NAME   STATUS   AGE
qa     Active   36s

root@master:~# kubectl get -f prod-ns.yaml 
NAME   STATUS   AGE
prod   Active   37s
```




## Edit a NameSpace with yaml file

```bash
root@master:~# kubectl edit -f dev-ns.yaml

namespace/dev edited
```



## Describe a NameSpace with yaml file

```bash
root@master:~# kubectl describe -f dev-ns.yaml 
Name:         dev
Labels:       kubernetes.io/metadata.name=dev
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.
```


## Delete a NameSpace with yaml file

```bash
root@master:~# kubectl delete -f qa-ns.yaml

namespace "qa" deleted
```




## Create a NameSpace with kubectl run command


```bash
root@master:~# kubectl create namespace qa-namespace

namespace/qa-namespace created
```

```bash
root@master:~# kubectl get ns qa-namespace 
NAME           STATUS   AGE
qa-namespace   Active   31s
```

## Print NameSpace with labels

```bash
root@master:~# kubectl get ns qa-namespace --show-labels

NAME           STATUS   AGE     LABELS
qa-namespace   Active   3m25s   kubernetes.io/metadata.name=qa-namespace
```



## YAML output

```bash
root@master:~# kubectl get ns qa-namespace -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2022-04-14T08:28:31Z"
  labels:
    kubernetes.io/metadata.name: qa-namespace
  name: qa-namespace
  resourceVersion: "118797"
  uid: 67c2f4c1-af8d-44df-b870-57aabc744584
spec:
  finalizers:
  - kubernetes
status:
  phase: Active
```


## Print all name spaces:

```bash
root@master:~# kubectl get ns 
NAME                   STATUS        AGE
default                Active        7d1h
dev                    Terminating   5d22h
kube-node-lease        Active        7d1h
kube-public            Active        7d1h
kube-system            Active        7d1h
kubernetes-dashboard   Active        7d1h
prod                   Active        5d22h
qa                     Terminating   5d22h
qa-namespace           Active        5m7s
tigera-operator        Active        7d1h
```


## Describe NameSpace

```bash
root@master:~# kubectl describe ns prod
Name:         prod
Labels:       kubernetes.io/metadata.name=prod
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.
```



## Edit a NameSpace

```bash
export KUBE_EDITOR='nano'

root@master:~# kubectl edit ns prod

namespace/prod edited
```



## Delete a NameSpace

```bash
root@master:~# kubectl delete ns prod

namespace "prod" deleted
```



## Create a pod in a specific NameSpace

```bash
cat <<EOF >>pod-in-QA-Namespace.yaml

apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-in-qa-namespace-yaml
  namespace: qa-namespace
  labels:
    app: nginx

spec:

  containers:
  - name: nginx-container
    image: nginx
    ports:
    - containerPort: 80
EOF
```

## Get pods of a specific NameSpace:

```bash
root@master:~# kubectl apply -f pod-in-QA-Namespace.yaml

pod/nginx-pod-in-qa-namespace-yaml created
```

```bash
root@master:~# kubectl run nginx-pod-in-qa-namespace-commandline --image=nginx --namespace=qa-namespace

pod/nginx-pod-in-qa-namespace-commandline created
```


```bash
root@master:~# kubectl get pods -n qa-namespace

NAME                                    READY   STATUS    RESTARTS   AGE
nginx-pod-in-qa-namespace-commandline   1/1     Running   0          2m58s
nginx-pod-in-qa-namespace-yaml          1/1     Running   0          101s
```


## Change NameSpace permanently:

```bash
root@master:~# kubectl config set-context --current --namespace=ns-namespace
Context "kubernetes-admin@kubernetes" modified.


root@master:~# kubectl config view | grep namespace
    namespace: ns-namespace
```
