# Training plan 

<img src="plan.png">

## Revision 

### app containerization 

<img src="appc.png">

### app Deployment 

<img src="appd.png">

### Deletion of pods 

```
[ashu@docker-host ~]$ kubectl  delete pod  ashupod-123 
pod "ashupod-123" deleted
[ashu@docker-host ~]$
```

### deleting all the pdos 

```
[ashu@docker-host ~]$ kubectl delete pods --all
pod "chanpod-123" deleted
pod "eashupod-143" deleted
pod "kushpod-123" deleted
pod "omesh-pod-type-1" deleted
pod "palpod-123" deleted
[ashu@docker-host ~]$ kubectl  get  po
No resources found in default namespace.
[ashu@docker-host ~]$ 

```

### creating pod from cli 

```
[ashu@docker-host ashu-k8sapps]$ kubectl  run  ashupod2 --image=docker.io/dockerashu/ashuwebapp:frontendv1  --port 80  
pod/ashupod2 created
[ashu@docker-host ashu-k8sapps]$ kubectl  get  pods
NAME       READY   STATUS    RESTARTS   AGE
ashupod2   1/1     Running   0          4s
[ashu@docker-host ashu-k8sapps]$ 

```

### auto gen yaml / json 

```
[ashu@docker-host ashu-k8sapps]$ kubectl  run  ashupod2 --image=docker.io/dockerashu/ashuwebapp:frontendv1  --port 80  --dry-run=client  -o yaml 
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupod2
  name: ashupod2
spec:
  containers:
  - image: docker.io/dockerashu/ashuwebapp:frontendv1
    name: ashupod2
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```

### store output in yaml file 

```
kubectl  run  ashupod2 --image=docker.io/dockerashu/ashuwebapp:frontendv1  --port 80  --dry-run=client  -o yaml  >ashupod2.yaml
```

### lets deploy it 

```
[ashu@docker-host ashu-k8sapps]$ ls
ashupod1.yaml  ashupod2.yaml
[ashu@docker-host ashu-k8sapps]$ kubectl  apply -f ashupod2.yaml 
pod/ashupod2 created
[ashu@docker-host ashu-k8sapps]$ kubectl  get  po
NAME         READY   STATUS    RESTARTS   AGE
ashupod2     1/1     Running   0          4s
gauravpod2   1/1     Running   0          3m25s
[ashu@docker-host ashu-k8sapps]$ kubectl  get  po -o wide
NAME         READY   STATUS    RESTARTS   AGE     IP               NODE          NOMINATED NODE   READINESS GATES
ashupod2     1/1     Running   0          9s      192.168.216.84   workernode2   <none>           <none>
gauravpod2   1/1     R
```

### capturing events in k8s for troubleshooting purpose 

```
  399  kubectl  get  events
  400  kubectl  get  events --field-selector  type!=Normal
```

### json format for POD instruction 

```
  404  kubectl  run  ashupod3 --image=docker.io/dockerashu/ashuwebapp:frontendv1  --port 80  --dry-run=client  -o json  
  405  kubectl  run  ashupod3 --image=docker.io/dockerashu/ashuwebapp:frontendv1  --port 80  --dry-run=client  -o json       >test.json 
  406  history 
[ashu@docker-host ~]$ kubectl  apply -f test.json 
pod/ashupod3 created
[ashu@docker-host ~]$ kubectl  get po 
NAME          READY   STATUS    RESTARTS   AGE
ashupod2      1/1     Running   0          10m
ashupod3      1/1     Running   0          4s
```

###  Creating a private space for individuals using Namespace 

<img src="ns.png">

### checking default namespaces in k8s 

```
[ashu@docker-host ~]$ kubectl  get  namespaces 
NAME              STATUS   AGE
default           Active   19h
kube-node-lease   Active   19h
kube-public       Active   19h
kube-system       Active   19h
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ kubectl  get  pods
No resources found in default namespace.
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ kubectl  get  pods  -n kube-system 
NAME                                       READY   STATUS    RESTARTS      AGE
calico-kube-controllers-555bc4b957-59tts   1/1     Running   1 (69m ago)   19h
calico-node-gjj79                          1/1     Running   1 (69m ago)   19h
calico-node-h2dsj                          1/1     Running   1 (69m ago)   19h
calico-node-kfhzg                          1/1     Running   1 (69m ago)   19h
coredns-6d4b75cb6d-8r6tz                   1/1     Running   1 (69m ago)   19h
coredns-6d4b75cb6d-bqctw                   1/1     Running   1 (69m ago)   19h


```

### creating custom namespaces

```
[ashu@docker-host ashu-k8sapps]$ kubectl  create  namespace  ashu-apps --dry-run=client -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: ashu-apps
spec: {}
status: {}
[ashu@docker-host ashu-k8sapps]$ kubectl  create  namespace  ashu-apps --dry-run=client -o yaml >ns.yaml 
[ashu@docker-host ashu-k8sapps]$ ls
ashupod1.yaml  ashupod2.yaml  ns.yaml
[ashu@docker-host ashu-k8sapps]$ kubectl apply -f  ns.yaml 
namespace/ashu-apps created
[ashu@docker-host ashu-k8sapps]$ kubectl  get  namespaces 
NAME              STATUS   AGE
ashu-apps         Active   6s
default           Active   19h
kube-node-lease   Active   19h
kube-public       Active   19h
kube-system       Active   19h
```

### configure default namespace for user 

```
[ashu@docker-host ~]$ kubectl  get  pods
No resources found in default namespace.
[ashu@docker-host ~]$ kubectl  config set-context  --current --namespace ashu-apps  
Context "kubernetes-admin@kubernetes" modified.
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ kubectl  get  pods
No resources found in ashu-apps namespace.
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ kubectl config  get-contexts 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   ashu-apps
[ashu@docker-host ~]$ 


```

### deploy pods 

```
[ashu@docker-host ashu-k8sapps]$ kubectl apply -f  ashupod1.yaml  -f ashupod2.yaml 
pod/ashupod-123 created
pod/ashupod2 created
[ashu@docker-host ashu-k8sapps]$ kubectl  get pod
NAME          READY   STATUS    RESTARTS   AGE
ashupod-123   1/1     Running   0          7s
ashupod2      1/1     Running   0          6s
```

### deploy pods in different namespace 

```
[ashu@docker-host ashu-k8sapps]$ kubectl apply -f  ashupod1.yaml  -f ashupod2.yaml -n default 
pod/ashupod-123 created
pod/ashupod2 created
[ashu@docker-host ashu-k8sapps]$ 
[ashu@docker-host ashu-k8sapps]$ 
[ashu@docker-host ashu-k8sapps]$ kubectl  get pod -n default 
NAME          READY   STATUS    RESTARTS   AGE
ashupod-123   1/1     Running   0          7s
ashupod2      1/1     Running   0          7s
[ashu@docker-host ashu-k8sapps]$ 
```

### defining namespace in YAML of POD 

```
[ashu@docker-host ashu-k8sapps]$ kubectl apply -f  ashupod1.yaml  -f ashupod2.yaml -n default 
pod/ashupod-123 created
pod/ashupod2 created
[ashu@docker-host ashu-k8sapps]$ 
[ashu@docker-host ashu-k8sapps]$ 
[ashu@docker-host ashu-k8sapps]$ kubectl  get pod -n default 
NAME          READY   STATUS    RESTARTS   AGE
ashupod-123   1/1     Running   0          7s
ashupod2      1/1     Running   0          7s
[ashu@docker-host ashu-k8sapps]$ 
```

### PRivate Docker image deploy in k8s 

```
kubectl run ashupod3 --image=phx.ocir.io/axmbtg8judkl/ashuwebapp:frontendv1   --port 80  --dry-run=client -o yaml >ocrpod.yaml 
[ashu@docker-host ashu-k8sapps]$ kubectl apply -f ocrpod.yaml 
pod/ashupod3 created
[ashu@docker-host ashu-k8sapps]$ kubectl  get  po 
NAME       READY   STATUS             RESTARTS   AGE
ashupod3   0/1     ImagePullBackOff   0          5s
[ashu@docker-host ashu-k8sapps]$ 

```

### understanding secret in k8s under apiVersion v1 

<img src="secret.png">

### creating secret to store ocr registry credential 

```
kubectl  create  secret  docker-registry  ashuimg-sec  --docker-server=phx.ocir.io   --docker-username=""  --docker-password=""  --dry-run=client  -o yaml >ocr_secret.yaml 

===
[ashu@docker-host ashu-k8sapps]$ kubectl  apply -f ocr_secret.yaml 
secret/ashuimg-sec created
[ashu@docker-host ashu-k8sapps]$ kubectl  get  secret  
NAME          TYPE                             DATA   AGE
ashuimg-sec   kubernetes.io/dockerconfigjson   1      10s
[ashu@docker-host ashu-k8sapps]$ 

```

### let's use it inside pod yaml file 

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: ashupod3
  name: ashupod3
spec:
  imagePullSecrets: # for calling secret 
  - name: ashuimg-sec # name of secret 
  containers:
  - image: phx.ocir.io/axmbtg8judkl/ashuwebapp:frontendv1
    name: ashupod3
    ports:
    - containerPort: 80
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}

```

### replace after adding changes 

```
[ashu@docker-host ashu-k8sapps]$ kubectl replace  -f ocrpod.yaml --force 
pod "ashupod3" deleted
pod/ashupod3 replaced
[ashu@docker-host ashu-k8sapps]$ kubectl  get  po 
NAME       READY   STATUS    RESTARTS   AGE
ashupod3   1/1     Running   0          6s
[ashu@docker-host ashu-k8sapps]$ 
```








