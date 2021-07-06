# Multi container pods
### Run a pod name multi-pod with 2 containers. The first container is named pluto with image busybox:latest and runs the command "/bin/sh -c 'sleep 1d'". The second container is named mars with image busybox:latest and runs the command "/bin/sh -c 'echo hello'; sleep 1d". Check that the pod started correctly and output the logs of the second container.
<details>
<summary>answer</summary>
<p>

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: multi-pod
  name: multi-pod
spec:
  containers:
  - image: busybox
    name: pluto
    command:
    - /bin/sh
    - -c
    - sleep 1d;
  - image: busybox
    name: mars
    command:
    - /bin/sh
    - -c
    - echo "hello"; sleep 1d
```

```
k get pod multi-pod
```
Not the 2/2 ready in the output.
```
$ k get pod multi-pod
NAME        READY   STATUS    RESTARTS   AGE
multi-pod   2/2     Running   0          72s
```

Lets check the logs.
```
$ k logs multi-pod mars
hello
```


</p>
</details>

### Consider the yaml file, which is the yaml file of a pod with 2 containers. Apply the yaml to the cluster, and notice that the number of containers in the pod that are ready is 1/2. Can you explain why?
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: multi-pod
  name: multi-pod
spec:
  containers:
  - image: busybox
    name: busybox
  - image: nginx
    name: nginx
```
<details>
<summary>answer</summary>
<p>

```
k describe pod multi-pod
```
Next look at the busybox container.


</p>
</details>