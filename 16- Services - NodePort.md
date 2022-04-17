

# Creating Sample Deployment and Service "Declaratively (Using YAML file)":

### Deployment YAML file:
```bash
root@master:~# cat <<EOF >>nginx-deploy.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.18
        ports:
        - containerPort: 80
EOF
```





### NodePort Service YAML file:
```bash
root@master:~# cat <<EOF >>nginx-svc-np.yaml
apiVersion: v1
kind: Service	
metadata:
  name: my-service
  labels:
    app: nginx-app
spec:
  selector:
    app: nginx-app
  type: NodePort
  ports:
  - nodePort: 31111
    port: 80
    targetPort: 80
EOF
```





###Apply and Validate:

```bash
root@master:~# kubectl apply -f nginx-deploy.yaml 
deployment.apps/nginx-deployment created

root@master:~# kubectl apply -f nginx-svc-np.yaml 
service/my-service created
```


#

# Creating Deployment and NodePort Service Application "Imperatively (From Command promp)":

### Create Sample Deployment
```bash
root@master:~# kubectl create deployment test --image=nginx --replicas=3

deployment.apps/test created
```





### Next, expose the previous Deployment:
```bash
root@master:~# kubectl expose deployment test --type=NodePort --name=test-svc --port=80

service/test-svc exposed
```





### Now, get the NodePort service number on which this service is exposed on:
```bash
root@master:~# kubectl get svc
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE

kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        10d
test-svc     NodePort    10.100.203.233   <none>        80:31993/TCP   3m8s
```





### Next, get the external IP address of the worker node on which this Pod is running
```bash
root@master:~# kubectl get pod -o wide

NAME                   READY   STATUS    RESTARTS   AGE     IP              NODE     NOMINATED NODE   READINESS GATES
test-8499f4f74-98jjr   1/1     Running   0          3m53s   10.244.171.93   worker   <none>           <none>
test-8499f4f74-dgjp7   1/1     Running   0          3m53s   10.244.171.97   worker   <none>           <none>
test-8499f4f74-dxhjn   1/1     Running   0          3m53s   10.244.171.95   worker   <none>           <none>
```









# Test the service:
```bash
root@master:~# curl master:31993
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```




```bash
root@master:~# curl worker:31993
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
