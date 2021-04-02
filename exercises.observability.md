# Multi container pods
### Run a busybox pod with image busybox:latest that runs the command "/bin/sh -c 'touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600'". Add a livenessprobe that executes the command 'cat /tmp/healthy', starting af 5 seconds, and performing the probe every 5 seconds. Observe the pod on startup.
<details>
<summary>answer</summary>
<p>

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    image: busybox
    name: busybox
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```

We observe the pod on startup. Watch how the pod restarts after a specific time period, i.e. when it fails the livenessprobe.
```
terminal ~ k get po --watch
NAME      READY   STATUS              RESTARTS   AGE
busybox   0/1     ContainerCreating   0          3s
busybox   1/1     Running             0          4s
busybox   1/1     Running             1          81s
```

</p>
</details>


### Create an nginx pod with image nginx:latest, exposing port 80 on the container. Also configure a readinessprobe on path '/' on port 80, starting after 10 seconds, performing every 3 seconds. Observe the pod on startup. 
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
    name: nginx
    ports:
    - containerPort: 80
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 3
```
We observe the pod on startup. Notice the time it takes for the pod to be ready.
```
terminal ~ k get po --watch
NAME    READY   STATUS    RESTARTS   AGE
nginx   0/1     Running   0          4s
nginx   1/1     Running   0          14s
```

</p>
</details>

### Run a busybox with the image busybox:latest and command "echo 'hello world'". Check the container logs afterwards.
<details>
<summary>answer</summary>
<p>

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - command:
    - echo
    - hello world
    image: busybox
    name: busybox
```
Or rather, run the following command.
```
k run busybox --image=busybox --command -- echo 'hello world'
```
We check the logs.
```
terminal ~ k logs busybox
hello world
```

</p>
</details>