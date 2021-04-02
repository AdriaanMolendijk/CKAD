# State persistence

### Get persistence volumes in the cluster
<details>
<summary>answer</summary>
<p>

```
k get pv
```

</p>
</details>

### Get the persistentvolumeclaims in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get pvc
```

</p>
</details>

### Create a secret called credentials from the environment file creds.txt below. Next create a busybox pod with image busybox:latest, having this credentials mounted at path /credentials. Check that the pod mounted these credentials succesfully.
creds.txt
```
username=adriaan
password=unsafe
```
<details>
<summary>answer</summary>
<p>

```
k create secret generic credentials --from-env-file=creds.txt
```
Below is the yaml file for the busybox pod.
```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: busybox
  name: busybox
spec:
  containers:
  - image: busybox
    name: busybox
    command:
    - /bin/sh
    - -c
    - sleep 1d;
    volumeMounts:
    - name: myvolume
      mountPath: /credentials
  volumes:
  - name: myvolume
    secret:
      secretName: credentials
```

```
terminal ~ k exec -it busybox -- ls -al /credentials
total 4
drwxrwxrwt    3 root     root           120 Jan 16 10:50 .
drwxr-xr-x    1 root     root          4096 Jan 16 10:50 ..
drwxr-xr-x    2 root     root            80 Jan 16 10:50 ..2021_01_16_10_50_27.123534553
lrwxrwxrwx    1 root     root            31 Jan 16 10:50 ..data -> ..2021_01_16_10_50_27.123534553
lrwxrwxrwx    1 root     root            15 Jan 16 10:50 password -> ..data/password
lrwxrwxrwx    1 root     root            15 Jan 16 10:50 username -> ..data/username
```


</p>
</details>


### Create a folder /myfolder on the master node, having file empty.txt inside of it. Create a persistentvolume called mypv with storage capacity 1Gi, accessMode RWO and hostPath /myfolder. Create a suitable persistentvolumeclaim called mypvc and mount this folder inside a busybox pod at path /mybusybox. Check that the volume mount was successful (that is, make sure the pod is actually scheduled on the master node too!).
<details>
<summary>answer</summary>
<p>

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mypv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /myfolder
```

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
     storage: 1Gi
```

```
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: busybox
  name: busybox
spec:
  nodeName: controlplane
  containers:
  - command:
    - sleep
    - 1d
    image: busybox
    name: busybox
    volumeMounts:
    - name: vol
      mountPath: /mybusybox
  volumes:
  - name: vol
    persistentVolumeClaim:
      claimName: mypvc
```

Let's check whether the deployment was successful and that our configuration was correct.
```
terminal ~ k get pod -o wide
NAME      READY   STATUS    RESTARTS   AGE   IP          NODE           NOMINATED NODE   READINESS GATES
busybox   1/1     Running   0          53s   10.44.0.1   controlplane   <none>           <none>
```
```
terminal ~ k exec -it busybox -- ls mybusybox
empty.txt
```


</p>
</details>