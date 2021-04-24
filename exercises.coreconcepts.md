# Core concepts
### Get simple cluster information
<details>
<summary>answer</summary>
<p>

```
k cluster-info
```

</p>
</details>

### Get a list of the nodes in the cluster
<details>
<summary>answer</summary>
<p>

```
k get nodes
```

</p>
</details>

### Get a list of the namespaces in the cluster
<details>
<summary>answer</summary>
<p>

```
k get ns
```

</p>
</details>

### Get a list of the pods in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get po
```

```
k get pod
```

```
k get pods
```

</p>
</details>

### Get a list of the deployments in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get deploy
```

```
k get deployment
```

</p>
</details>

### Get a list of the services in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get svc
```

```
k get services
```

</p>
</details>

### Get a list of the pods, deployments and services in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get pod,deploy,svc
```

</p>
</details>

### Get a list of the pods in all namespace
<details>
<summary>answer</summary>
<p>

```
k get pod -A
```

```
k get pod --all-namespaces
```

</p>
</details>

### Run a pod named nginx with the image nginx:latest in the default namespace. Check to make sure that the pod started correctly.
<details>
<summary>answer</summary>
<p>

```
k run nginx --image=nginx 
```

```
k get pod --watch
```

</p>
</details>

### Delete the nginx pod in the default namespace
<details>
<summary>answer</summary>
<p>

```
k delete pod nginx
```

</p>
</details>

### Create a namespace called myns
<details>
<summary>answer</summary>
<p>

```
k create ns myns
```

</p>
</details>

### Run a pod named nginx with the image nginx:latest in the myns namespace. Check to make sure that the pod started correctly.
<details>
<summary>answer</summary>
<p>

```
k run nginx --image=nginx -n myns
```

```
k get pod --watch -n myns
```

</p>
</details>

### Delete the nginx pod in the myns namespace
<details>
<summary>answer</summary>
<p>

```
k delete pod nginx -n myns
```

</p>
</details>

### Run a pod called busyboxy with the image busybox:latest that runs the command 'env'. Make sure that the command executed correctly by checking its logs.
<details>
<summary>answer</summary>
<p>

```
k run busybox --image=busybox --command -- env 
```

```
k logs busybox
```

</p>
</details>

### Create a pod called nginx with the image nginx:latest. Obtain the internal IP address of the nginx pod.
<details>
<summary>answer</summary>
<p>

```
k run nginx --image=nginx
```

```
k get pod nginx -o wide 
```

</p>
</details>

### Get extensive details of the nginx pod.
<details>
<summary>answer</summary>
<p>

```
k describe pod nginx
```


</p>
</details>

### Obtain the yaml template of a simple ubuntu pod. Do NOT create the pod on the cluster
<details>
<summary>answer</summary>
<p>

```
k run ubuntu --image=ubuntu --dry-run=client -o yaml 
```
or, with the export do='--dry-run=client -yaml' that I use,

```
k run ubuntu --image=ubuntu $do
```

</p>
</details>

### Create two pods in the default namespace. The first is a pod named nginx with image nginx:1.19.6 having port 80 exposed for traffic. The second is a pod named busybox with image busybox:latest running the command "/bin/sh -c 'sleep 1d'". Start a shell inside the busybox pod and confirm that pod nginx receives traffic on the port 80.
<details>
<summary>answer</summary>
<p>

```
k run nginx --image=nginx:1.19.6 --port 80
```

```
k run busybox --image=busybox --command -- /bin/sh -c 'sleep 1d'
```

Obtain the IP address of the nginx pod.
```
k get pod nginx -o wide 
```
Say that this IP is equal to 10.1.0.17.

```
k exec -it busybox -- wget --spider 10.1.0.17:80
```
We get the following output.
```
Connecting to 10.1.0.17:80 (10.1.0.17:80)
remote file exists
```


</p>
</details>

### Create pod called ubuntu with image ubuntu:latest and command "/bin/sh -c 'mkdir mydir; sleep 1d;'". Next check whether this command was successful.
<details>
<summary>answer</summary>
<p>

```
k run ubuntu --image=ubuntu --command -- /bin/sh -c 'mkdir mydir; sleep 1d;'
```
Start a shell inside the ubuntu pod.

```
k exec -it ubuntu -- sh
```
Then inside the pod check whether creation was successful.
```
$ ls | grep mydir
mydir
```

</p>
</details>


### Create pod called nginx with image nginx:latest and containername my-container
<details>
<summary>answer</summary>
<p>

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: my-container
```

</p>
</details>