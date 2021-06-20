# Configuration
### Get the configmaps in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get cm
```

```
k get configmap
```

```
k get configmaps
```

</p>
</details>

### Get the secrets in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get secrets
```

```
k get secrets
```

</p>
</details>

### Get the service accounts in the default namespace
<details>
<summary>answer</summary>
<p>

```
k get sa
```

```
k get serviceaccount
```

```
k get serviceaccounts
```

</p>
</details>

### Create a configmap called bar with data food=pizza and drink=lemonade
<details>
<summary>answer</summary>
<p>

```
k create cm bar --from-literal=food=pizza --from-literal=drink=lemonade
```

</p>
</details>

### Get detailed information on this bar configmap
<details>
<summary>answer</summary>
<p>

```
k describe cm bar
```
The output is given below.
```
Name:         bar
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
drink:
----
lemonade
food:
----
pizza
Events:  <none>
```

</p>
</details>

### Create a configmap person from the file below. Describe the configmap afterwards.
Create the file person.txt
```
name=foo
age=44
sex=male
```
Now create the configmap using this file.
<details>
<summary>answer</summary>
<p>

```
k create cm person --from-file=person.txt
```

```
k describe cm person
```

</p>
</details>

### Create a configmap person again, but this time convert the file to environment data.

<details>
<summary>answer</summary>
<p>

```
k create cm person --from-file=person.txt
```

```
Name:         person
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
age:
----
44
name:
----
foo
sex:
----
male
Events:  <none>
```

</p>
</details>

### Create a configmap named busybox-envs with data env1=val1, env2=val2. Then create a busybox pod with image busybox:latest that has this reads this environment variables from the configmaps (note: there are two ways to do this reading/loading). Check that the loading was successful.

<details>
<summary>answer</summary>
<p>

```
k create cm busybox-envs --from-literal=env1=val1 --from-literal=env2=val2
```

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
    envFrom:
    - configMapRef:
        name: busybox-envs
    command:
    - env
```

```
$ k logs busybox | grep env
env1=val1
env2=val2
```


</p>
</details>

### Create a secret named user-creds with key value pairs user=username and password=unsafe. Describe the secret afterwards.

<details>
<summary>answer</summary>
<p>

```
k create secret generic user-creds --from-literal=user=username --from-literal=password=unsafe
```

```
k describe secret user-creds
```
This gives output
```
Name:         user-creds
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  6 bytes
user:      8 bytes
```

</p>
</details>


### Consider the yaml file of the secret below. Apply the secret to the cluster and decrypt it.

```
apiVersion: v1
data:
  password: ZGVjcnlwdC1tZS1wbGVhc2U=
kind: Secret
metadata:
  name: mysecret
```

<details>
<summary>answer</summary>
<p>

```
k apply -f secret.yaml
```

```
$ echo "ZGVjcnlwdC1tZS1wbGVhc2U=" | base64 --decode
decrypt-me-please
```

</p>
</details>

### Get the access token associated with the default service account in the default namespace 

<details>
<summary>answer</summary>
<p>

```
k describe sa default
```
We obtain the following output.
```
Name:                default
Namespace:           default
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   default-token-7l59s
Tokens:              default-token-7l59s
Events:              <none>
```

```
k describe secret default-token-7l59s
```
We obtain the following output.
```
Name:         default-token-7l59s
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: 57b32f7f-cf26-46a0-9dec-bf88f221518f

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  7 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IldEZ2VoQXlHZWQzV1JMV1NMb0hYNXpEVmNRZ3Utb3NaLU91ZmNQYlBRUUEifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tN2w1OXMiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3YjMyZjdmLWNmMjYtNDZhMC05ZGVjLWJmODhmMjIxNTE4ZiIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0PmRlZmF1bHQifQ.MZwH6rtLpP06XJb8s_bjy97JXO7nsqekDctNgpo5bDiJKYPmpffbRysKfk6GIe8WEz3bsjHEwUOZ1w0I64lyH0Y8ZLnFJobUgNe6G2Y2SLHm_oAWSx5Jm70BQn6Ei6JGuuGp0R3LcGpGaIYJ7M1vqiHjXHhZ1lo13QW3fZUxprif0KNQPEAuID3z1thcOoOYoF71Ose4Ldto0CxtJ_1SzPpb96haJYABieZlz-RYgQgXLKUdHd2OyNxJAaRNp9hX-x3X1jUILetGo441gFqTX0hNRevudRtDzq4dGT7Rj4iTXW8NV5Zp62xHEe0YBzfCaeuw3VwnBOX69ulnbAoZ0A
```

</p>
</details>

### Run a busybox pod with image busybox:latest that has a resource utilization of cpu=250m, memory=64Mi, and limits cpu=500m, memory=128Mi.

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
  - image: busybox
    name: busybox
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

</p>
</details>


### Run an nginx pod with image nginx:latest that runs as root and has the SYS_TIME capabilities added to its securitycontext. Confirm that you configured the settings correctly by starting a shell in the pod and executing appropriate commands.

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
    securityContext:
      runAsUser: 0
      capabilities:
        add: ["SYS_TIME"]
```

```
$ k exec -it nginx -- sh
```

```
nginx $ whoami
root
```
```
nginx $ id
uid=0(root) gid=0(root) groups=0(root)
```
```
nginx $ date -s '1 JAN 2021'
Fri Jan  1 00:00:00 UTC 2021 
```

</p>
</details>

### Get a list of all nodes running in the cluster. Determine if the master nodes has any taints on it.

<details>
<summary>answer</summary>
<p>

```
k get nodes
```

```
k describe node docker-desktop | grep Taint
```
We get the following output (this obviously differs per cluster).
```
$ k describe node docker-desktop | grep Taint
Taints:             <none>
```


</p>
</details>