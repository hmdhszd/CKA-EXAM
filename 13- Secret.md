## Creating Secret Declaratively (Using YAML file):



Base-64 Encoding:

```bash
root@master:~# echo -n 'admin' | base64
YWRtaW4=



root@master:~# echo -n '1f2d1e2e67df' | base64
MWYyZDFlMmU2N2Rm

```



```bash
root@master:~# cat <<EOF >>db-user-pass.yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-user-pass
  namespace: default
data:
  username: YWRtaW4=
  password: MWYyZDFlMmU2N2Rm
EOF
```

```bash
root@master:~# kubectl apply -f db-user-pass.yaml

secret/db-user-pass created
```



```bash
root@master:~# kubectl get -f db-user-pass.yaml

NAME           TYPE     DATA   AGE
db-user-pass   Opaque   2      2m2s
```




## Edit a Secret with yaml file

```bash
root@master:~# kubectl edit -f db-user-pass.yaml 
```



## Describe a Secret with yaml file

```bash
root@master:~# kubectl describe -f db-user-pass.yaml 
Name:         db-user-pass
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
username:  5 bytes
password:  12 bytes
```


## Delete a Secret with yaml file

```bash
root@master:~# kubectl delete -f db-user-pass.yaml 
secret "db-user-pass" deleted
```




## Create a Secret from Command line

From Literal value:

```bash
root@master:~# kubectl create secret generic test-secret --from-literal='username=my-app' --from-literal='password=39528$vdg7Jb'

secret/test-secret created
```

From File:

```bash
root@master:~# echo -n 'admin' > ./username.txt
root@master:~# echo -n '1f2d1e2e67df' > ./password.txt



root@master:~# kubectl create secret generic db-user-pass-from-file --from-file=./username.txt --from-file=./password.txt

secret/db-user-pass-from-file created

```


```bash
root@master:~# kubectl get secrets

NAME                     TYPE                                  DATA   AGE
db-user-pass-from-file   Opaque                                2      2m49s
default-token-mvtt9      kubernetes.io/service-account-token   3      9d
test-secret              Opaque                                2      3m44s
```


## YAML output

```bash
root@master:~# kubectl get secrets test-secret -o yaml 
apiVersion: v1
data:
  password: Mzk1MjgkdmRnN0pi
  username: bXktYXBw
kind: Secret
metadata:
  creationTimestamp: "2022-04-16T17:12:13Z"
  name: test-secret
  namespace: default
  resourceVersion: "247973"
  uid: 8e741bab-dd8c-483c-8fa7-e42bdb7baaf0
type: Opaque
```


## Describe a Secret

```bash
root@master:~# kubectl describe secrets test-secret 
Name:         test-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  12 bytes
username:  6 bytes

```



## Edit a Secret

```bash
export KUBE_EDITOR='nano'


root@master:~# kubectl edit secrets test-secret
```


## Delete a Secret

```bash
root@master:~# kubectl delete secrets test-secret

secret "test-secret" deleted
```



# Injecting Secret into Pods

## 1- As Environmental Variables
## 2- As Files inside the Volume


##

## 1- Injecting "Secrets" into Pod As Environmental Variables:

```bash
root@master:~# cat <<EOF >>my-secrets-pod-env.yaml

apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
  - name: mycontainer
    image: redis
    env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: db-user-pass
            key: username
      - name: SECRET_PASSWORD
        valueFrom:
          secretKeyRef:
            name: db-user-pass
            key: password
  restartPolicy: Never
EOF
```



Apply and Validate:

```bash
root@master:~# kubectl apply -f my-secrets-pod-env.yaml

pod/secret-env-pod created



root@master:~# kubectl exec secret-env-pod -- env | grep SECRET

SECRET_USERNAME=admin
SECRET_PASSWORD=1f2d1e2e67df
```

##
## 2- Injecting "Secrets" into Pod As Files inside the Volume:

```bash
root@master:~# cat <<EOF >>my-secrets-vol-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-vol-pod
spec:
  containers:
    - name: test-container
      image: nginx
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret-volume
  volumes:
    - name: secret-volume
      secret:
        secretName: test-secret
EOF
```


Apply and Validate:

```bash
root@master:~# kubectl apply -f my-secrets-vol-pod.yaml 
pod/secret-vol-pod created



root@master:~# kubectl exec secret-vol-pod -- ls /etc/secret-volume
password
username



root@master:~# kubectl exec secret-vol-pod -- cat /etc/secret-volume/username
my-app


root@master:~# kubectl exec secret-vol-pod -- cat /etc/secret-volume/password
39528$vdg7Jb
```

