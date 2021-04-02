# Pod design
### Run a busybox pod with the image busybox:latest. Label the pod with 'env=dev'. Also check out the status of the pod, showing its label in the output.
<details>
<summary>answer</summary>
<p>

```
k run busybox --image=busybox -l env=dev
```
We check whether pod ran successfully. 
```
terminal ~ k get pod --show-labels
NAME      READY   STATUS             RESTARTS   AGE   LABELS
busybox   0/1     CrashLoopBackOff   2          38s   env=dev
```

</p>
</details>

### Deploy the yaml file given below. How many pods are running in the production namespace?
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: prod
  name: busybox
spec:
  containers:
  - image: busybox
    name: busybox
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: acc
  name: busybox2
spec:
  containers:
  - image: busybox
    name: busybox
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: prod
  name: busybox3
spec:
  containers:
  - image: busybox
    name: busybox
---
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: test
  name: busybox4
spec:
  containers:
  - image: busybox
    name: busybox
```
<details>
<summary>answer</summary>
<p>

```
terminal ~ k get pod --show-labels -l env=prod
NAME       READY   STATUS             RESTARTS   AGE   LABELS
busybox    0/1     CrashLoopBackOff   1          24s   env=prod
busybox3   0/1     CrashLoopBackOff   1          24s   env=prod
```

</p>
</details>

### Deploy an nginx pod with image nginx:latest and label status=removeme. Next remove the label from the pod.
<details>
<summary>answer</summary>
<p>

```
k run nginx --image=nginx -l status=removeme
```

```
k label pod nginx status-
```
</p>
</details>

### Deploy an nginx deployment with image nginx:latest, 3 replicas, exposing the port 80 on the container. Check that the deployment is running afterwards.
<details>
<summary>answer</summary>
<p>

```
k create deploy nginx --image=nginx --replicas=3 --port=80 
```
Next whether we check wether the deployment is running. Notice the ready column in the output.
```
terminal ~ k get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   3/3     3            3           9s
```

</p>
</details>

### Deploy a busybox deployment with image busybox:1.32.1, having 5 replicas, running the command "/bin/sh -c 'sleep 1d'". Check the satus of the deployment.
<details>
<summary>answer</summary>
<p>

The yaml file of the deployment is given below.
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busybox
  name: busybox
spec:
  replicas: 5
  selector:
    matchLabels:
      app: busybox
  strategy: {}
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
      - image: busybox:1.32.1
        name: busybox
        command:
        - /bin/sh
        - -c
        - sleep 1d;
```
Let's check the status of the deployment.
```
terminal ~ k rollout status deploy busybox
deployment "busybox" successfully rolled out
```
</p>
</details>


### Consider the deployment of the question above. Change the image tag from the busybox image to 1.33. Check whether the rollout was successful. The perform a rollback, and check the history of the rollouts.
<details>
<summary>answer</summary>
<p>

The yaml file of the deployment is given below.
```
k edit deploy busybox 
```
Let's check the rollout status.
```
terminal ~ k rollout status deploy busybox 
Waiting for deployment "busybox" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "busybox" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "busybox" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "busybox" rollout to finish: 4 of 5 updated replicas are available...
deployment "busybox" successfully rolled out
```

```
terminal ~ k rollout undo deploy busybox
deployment.apps/busybox rolled back
```

```
terminal ~ k rollout history deploy busybox
deployment.apps/busybox 
REVISION  CHANGE-CAUSE
2         <none>
3         <none>
```

</p>
</details>

### Create a busybox deployment with image busybox:latest that has a resource utilization of cpu=250m, memory=64Mi, and limits cpu=500m, memory=128Mi, and run the command that sleeps 1 day. Next scale the deployment to have 2 replicas instead of 1.

<details>
<summary>answer</summary>
<p>

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: busybox
  name: busybox
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox
  strategy: {}
  template:
    metadata:
      labels:
        app: busybox
    spec:
      containers:
      - image: busybox
        name: busybox
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
        command:
        - /bin/sh
        - -c
        - sleep 1d;
```
Next we will scale up the deployment
```
k scale deploy busybox --replicas=2
```

</p>
</details>

### Create an nginx deployment with image nginx:1.19.1 having a single replica. Next change the image tag to 1.19.2 and then to 1.19.3. Check the rollout history of the deployment. Then rollback from version 1.19.3 to version 1.19.1.

<details>
<summary>answer</summary>
<p>

```
terminal ~ k rollout history deploy nginx
deployment.apps/nginx 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
3         <none>
```

```
k rollout undo deploy nginx --to-revision=1
```

</p>
</details>


### Create a job named cowsay with image docker/whalesay, executing command "cowsay Im acing the CKAD". The job needs to complete the task 10 times before the job is considered complete. Also check the logs of the pods that are in a completed status.

<details>
<summary>answer</summary>
<p>

```
apiVersion: batch/v1
kind: Job
metadata:
  name: cowsay
spec:
  completions: 10
  template:
    metadata:
    spec:
      containers:
      - image: docker/whalesay
        name: cowsay
        command:
        - cowsay
        - "Im acing the CKAD"
      restartPolicy: Never
```
Let's check the logs of one of the pods that is in a completed status.
```
k logs cowsay-z44bj
 ___________________ 
< Im acing the CKAD >
 ------------------- 
    \
     \
      \     
                    ##        .            
              ## ## ##       ==            
           ## ## ## ##      ===            
       /""""""""""""""""___/ ===        
  ~~~ {~~ ~~~~ ~~~ ~~~~ ~~ ~ /  ===- ~~~   
       \______ o          __/            
        \    \        __/             
          \____\______/   `
```

</p>
</details>

### Do the same exercise as above, but this time run the job with parallelism set to 3. What difference do you notice if you check the pods status on the cluster with a --watch flag?

<details>
<summary>answer</summary>
<p>

```
apiVersion: batch/v1
kind: Job
metadata:
  name: cowsay
spec:
  completions: 10
  parallelism: 3
  template:
    metadata:
    spec:
      containers:
      - image: docker/whalesay
        name: cowsay
        command:
        - cowsay
        - "Im acing the CKAD"
      restartPolicy: Never
```
Let's check the pods status. Notice how the pods are run in parallel.
```
terminal ~ k get po --watch
NAME           READY   STATUS              RESTARTS   AGE
cowsay-6lrxg   0/1     ContainerCreating   0          2s
cowsay-cs44j   0/1     ContainerCreating   0          2s
cowsay-m7zxf   0/1     ContainerCreating   0          2s
```

</p>
</details>

### Create the kubernetes resource using the yaml file below. What logs do you get when running this job? 

```
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

<details>
<summary>answer</summary>
<p>

```
terminal ~ k logs pi-r8bb2
3.1415926535897932384
```

</p>
</details>
