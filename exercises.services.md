# Services

### Get the services running in the default namespace
<details>
<summary>answer</summary>
<p>

```
$ k get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   104s
```

</p>
</details>

### Create a busybox pod with image busybox:latest, executing command `sleep 1d'. Next run an nginx pod with image nginx:latest, exposing port 80 on the container. Confirm that the port can receive traffic by connecting to it from the nginx pod. 
<details>
<summary>answer</summary>
<p>

```
k run busybox --image=busybox --command -- sleep 1d
```

```
k run nginx --image=nginx --port 80
```

```
$ k get po -o wide
NAME           READY   STATUS        RESTARTS   AGE    IP          NODE     NOMINATED NODE   READINESS GATES
busybox        1/1     Running       0          30s    10.32.0.7   node01   <none>           <none>
nginx          1/1     Running       0          20s    10.32.0.8   node01   <none>           <none>
```

Let's see if we can connect to the nginx pod.
```
$ k exec -it busybox -- sh
```
```
busybox ~ wget 10.32.0.8:80
Connecting to 10.32.0.8:80 (10.32.0.8:80)
saving to 'index.html'
index.html           100% |**********************************************************************************************|   612  0:00:00 ETA
'index.html' saved
```
```
busybox ~ cat index.html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
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

</p>
</details>

### Do the same exercise as before, but rather than connecting to the nginx container via the pods IP, try to connect to using a ClusterIP service.
<details>
<summary>answer</summary>
<p>

```
k run busybox --image=busybox --command -- sleep 1d
```

```
k run nginx --image=nginx --port 80
```

```
$ expose pod nginx --port=80 --name=nginx
service/nginx exposed
```
```
$ k get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP   8m49s
nginx        ClusterIP   10.99.214.196   <none>        80/TCP    2s
```

```
$ k exec -it busybox -- sh
```

```
busybox ~ wget nginx:80
Connecting to nginx:80 (10.99.214.196:80)
saving to 'index.html'
index.html           100% |**********************************************************************************************|   612  0:00:00 ETA
'index.html' saved
```

</p>
</details>


### Do the same exercise as before, but rather than connecting to the nginx container via the pods IP, try to connect to using a NodePort service.
<details>
<summary>answer</summary>
<p>

```
k run busybox --image=busybox --command -- sleep 1d
```

```
k run nginx --image=nginx --port 80
```

```
$ k expose pod nginx --port=80 --type=NodePort
service/nginx exposed
```
```
$ k get node -o wide
NAME           STATUS   ROLES    AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION       CONTAINER-RUNTIME
controlplane   Ready    master   13m   v1.19.0   172.17.0.71   <none>        Ubuntu 18.04.5 LTS   4.15.0-122-generic   docker://19.3.13
node01         Ready    <none>   13m   v1.19.0   172.17.0.72   <none>        Ubuntu 18.04.5 LTS   4.15.0-122-generic   docker://19.3.13
```
```
$ k get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP        13m
nginx        NodePort    10.101.53.210   <none>        80:30583/TCP   36s
```

```
$ k exec -it busybox -- sh
```

```
busybox ~ wget 172.17.0.72:30583
Connecting to 172.17.0.72:30583 (172.17.0.72:30583)
saving to 'index.html'
index.html           100% |**********************************************************************************************|   612  0:00:00 ETA
'index.html' saved
```

</p>
</details>


### Create the kubernetes resource using the yaml file below. Next take a look at the pods running in the default namespace, showing the labels of the pods. Create a network policy called restricted-out that only allows traffic to the nginx pod from pods with label traffic=out. Confirm that your network policy is applied correctly by confirming that you can connect to the nginx pod from nginx2 and nginx3, but not from pod nginx4.

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    traffic: in
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    traffic: out
  name: nginx2
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    traffic: out
  name: nginx3
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    traffic: out-no-allow
  name: nginx4
spec:
  containers:
  - image: nginx
    name: nginx
    ports:
    - containerPort: 80
```

<details>
<summary>answer</summary>
<p>

Let's check out the pods first.
```
$ k get pod -o wide
NAME     READY   STATUS    RESTARTS   AGE     IP         NODE             NOMINATED NODE   READINESS GATES
nginx    1/1     Running   0          6m46s   10.1.1.5   docker-desktop   <none>           <none>
nginx2   1/1     Running   0          6m46s   10.1.1.6   docker-desktop   <none>           <none>
nginx3   1/1     Running   0          6m46s   10.1.1.7   docker-desktop   <none>           <none>
nginx4   1/1     Running   0          4m5s    10.1.1.8   docker-desktop   <none>           <none>
```
Apply the network policy file and describe the network policy.
```
$ k describe netpol
Name:         restricted-out
Namespace:    default
Created on:   2021-01-16 10:56:13 +0100 CET
Labels:       <none>
Annotations:  <none>
Spec:
  PodSelector:     traffic=in
  Allowing ingress traffic:
    To Port: 80/TCP
    From:
      PodSelector: traffic=out
  Allowing egress traffic:
    To Port: <any> (traffic allowed to all ports)
    To: <any> (traffic not restricted by source)
  Policy Types: Ingress, Egress
```
We leave checking the correctness of the network policy as an exercise :)

</p>
</details>