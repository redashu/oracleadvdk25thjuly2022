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



