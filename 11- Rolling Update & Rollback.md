#### What is Max surge and Max unavailable?

maxSurge is an optional field that specifies the maximum number of Pods that can be created over the desired number of Pods. The value can be an absolute number (for example, 5) or a percentage of desired Pods (for example, 10%). The value cannot be 0 if MaxUnavailable is 0.

maxUnavailable declares the maximum number (or percentage) of unavailable pods during the update.



## Create a Deployment with yaml file for testing Rolling Update and Rollback

```bash
root@master:~# cat <<EOF >>nginx-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2
      maxUnavailable: 0
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      name: nginx-pod
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

You can specify the --record flag to write the command executed in the resource annotation kubernetes.io/change-cause . The recorded change is useful for future introspection. For example, to see the commands executed in each Deployment revision.

```bash
root@master:~# kubectl apply -f nginx-deployment.yaml  --record

deployment.apps/nginx-deployment created
```

```bash
root@master:~# kubectl get deployments -o wide

NAME               READY   UP-TO-DATE   AVAILABLE   AGE     CONTAINERS        IMAGES       SELECTOR
nginx-deployment   3/3     3            3           2m50s   nginx-container   nginx:1.18   app=nginx-app
```

#
#

# Two ways of update an application:
### 1- Imperative : (use command)
```bash
root@master:~# kubectl set image deployment nginx-deployment nginx-container=nginx:1.19 --record

deployment.apps/nginx-deployment image updated
```

```bash
root@master:~# kubectl rollout status  deployment nginx-deployment

Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
```

```bash
root@master:~# kubectl rollout history deployment nginx-deployment

deployment.apps/nginx-deployment 
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=nginx-deployment.yaml --record=true
2         kubectl set image deployment nginx-deployment nginx-container=nginx:1.19 --record=true
```




### 2- Declarative: update the image version in the source YAML file, then use kubectl apply command.

#
#


# Rollback:

```bash
root@master:~# kubectl rollout undo deployment nginx-deployment

deployment.apps/nginx-deployment rolled back
```
or
```bash
root@master:~# kubectl rollout undo deployment nginx-deployment --to-revision=4

deployment.apps/nginx-deployment rolled back
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


