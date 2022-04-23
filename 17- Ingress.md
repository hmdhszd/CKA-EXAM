# Ingress:

### Ingress-controller:
The ingress-controller is the public facing endpoint. It is essentially a loadbalancer service that has a public IP through which traffic is going to be directed to your different services.

The Nginx ingress controller is nothing but a service of type LoadBalancer. What is does is be the public-facing endpoint for your services. The IP address assigned to this service can route traffic to multiple services. So you can go ahead and define your services as ClusterIP and have them exposed through the Nginx ingress controller.

### Ingress resources:
The ingress resources will be a step below the ingress controller, they will be assigned the same IP address as your ingress controller load balancing service. And based on the specs of your ingress, the controller routes the traffic to the corresponding service.

#

### Install Ingress:
```bash
root@master:~# kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.2.0/deploy/static/provider/cloud/deploy.yaml

namespace/ingress-nginx created
serviceaccount/ingress-nginx created
serviceaccount/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
configmap/ingress-nginx-controller created
service/ingress-nginx-controller created
service/ingress-nginx-controller-admission created
deployment.apps/ingress-nginx-controller created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
ingressclass.networking.k8s.io/nginx created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
```


### Verify:
```bash
root@master:~# kubectget all --namespace=ingress-nginx 

NAME                                            READY   STATUS    RESTARTS   AGE
pod/ingress-nginx-controller-7575567f98-d52bz   1/1     Running   0          33m


NAME                                         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
service/ingress-nginx-controller             LoadBalancer   10.110.231.79   <pending>     80:30842/TCP,443:30085/TCP   60m
service/ingress-nginx-controller-admission   ClusterIP      10.99.111.97    <none>        443/TCP                      60m


NAME                                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/ingress-nginx-controller   1/1     1            1           60m


NAME                                                  DESIRED   CURRENT   READY   AGE
replicaset.apps/ingress-nginx-controller-7575567f98   1         1         1       60m


NAME                                       COMPLETIONS   DURATION   AGE
job.batch/ingress-nginx-admission-create   1/1           35s        60m
job.batch/ingress-nginx-admission-patch    1/1           35s        60m

```


### Local testing (Command line)

Let's create a simple web server and the associated service:
```bash
root@master:~# kubectl create deployment demo --image=httpd --port=80

deployment.apps/demo created



root@master:~# kubectl expose deployment demo

service/demo exposed
```

Then create an ingress resource. The following example uses an host that maps to localhost:
```bash
root@master:~# kubectl create ingress demo-localhost --class=nginx \
>   --rule=demo.localdev.me/*=demo:80

ingress.networking.k8s.io/demo-localhost created
```

add a dns record for testing purposes:
```bash
root@master:~# echo "127.0.0.1 demo.localdev.me" >> /etc/hosts
```

Now, forward a local port to the ingress controller:
```bash
root@master:~# kubectl port-forward --namespace=ingress-nginx service/ingress-nginx-controller 8080:80

Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```


At this point, if you access http://demo.localdev.me:8080/, you should see an HTML page telling you "It works!".
```bash
root@master:~# curl http://demo.localdev.me:8080

<html><body><h1>It works!</h1></body></html>
```

#
#

### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```


### 
```bash

```