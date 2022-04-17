

## Resource Request, defines the minimum amount of resources that containers need.

## Resource Limit, make sure a container never goes above a certain value. The container is only allowed to go up to the limit, and then it is restricted.



### Create a test container with resource request and limit:

```bash
root@master:~# cat <<EOF >>resource-request-limit-pod.yml

apiVersion: v1
kind: Pod
metadata:
  name: resource-request-limit-pod
spec:
  containers:
  - name: app
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"

EOF
```


```bash
root@master:~# kubectl apply -f resource-request-limit-pod.yml

pod/resource-request-limit-pod created
```


```bash
root@master:~# k describe pod resource-request-limit-pod 
Name:         resource-request-limit-pod
Namespace:    default
Priority:     0
Node:         worker/192.168.100.104
Start Time:   Sun, 17 Apr 2022 04:33:38 +0000
Labels:       <none>
Annotations:  cni.projectcalico.org/containerID: cf2a2166c5516271ba5b8aab8c66eee88845741c334a1118a5457cf4a676aba9
              cni.projectcalico.org/podIP: 10.244.171.87/32
              cni.projectcalico.org/podIPs: 10.244.171.87/32
Status:       Pending
IP:           
IPs:          <none>
Containers:
  app:
    Container ID:   
    Image:          nginx
    Image ID:       
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     500m
      memory:  128Mi
    Requests:
      cpu:        250m
      memory:     64Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-k2gt4 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-k2gt4:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  93s   default-scheduler  Successfully assigned default/resource-request-limit-pod to worker
  Normal  Pulling    92s   kubelet            Pulling image "nginx"
```
