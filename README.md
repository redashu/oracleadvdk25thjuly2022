# Training plan 

<img src="plan.png">

### k8s in overall 

<img src="rev.png">

### customer multiple app 

<img src="app1.png">

### Deployment with configMap and Auto scaling in k8s 

<img src="dep.png">

## creating deployment YAMLS 

### creating Deployment 

```
kubectl create  deployment  ashucustomerapp --image=dockerashu/oracleapp:v1  --port   80  --dry-run=client -o yaml  >deployment.yaml
```

### creating configMap 

```
kubectl create configmap appenv  --from-literal deploy=app1 --dry-run=client -o yaml  >config.yaml 
```

### calling Env in Deployment yaml 

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashucustomerapp
  name: ashucustomerapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashucustomerapp
  strategy: {}
  template: # for creating pod 
    metadata:
      creationTimestamp: null
      labels:
        app: ashucustomerapp
    spec:
      containers:
      - image: dockerashu/oracleapp:v1
        name: oracleapp
        ports:
        - containerPort: 80
        envFrom: # taking env from somewhere 
        - configMapRef: # from Configmap 
            name: appenv # name of configmap 
        resources: {}
status: {}

```

### creating nodeport service 

```
kubectl create  service nodeport ashulb1 --tcp 1234:80  --dry-run=client -o yaml >nodeport.yaml
```

### updating pod label in service yaml 

```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: ashulb1
  name: ashulb1
spec:
  ports:
  - name: 1234-80
    port: 1234
    protocol: TCP
    targetPort: 80
  selector: # using label of pod to find 
    app: ashucustomerapp # this is label my deployemnt pod section 
  type: NodePort
status:
  loadBalancer: {}

```

### lets deploy it 

```
[ashu@docker-host customer-app-deploy]$ kubectl  apply -f  . 
configmap/appenv configured
deployment.apps/ashucustomerapp created
service/ashulb1 configured
[ashu@docker-host customer-app-deploy]$ kubectl get deploy
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
ashucustomerapp   1/1     1            1           30s
[ashu@docker-host customer-app-deploy]$ kubectl get rs
NAME                         DESIRED   CURRENT   READY   AGE
ashucustomerapp-7fdbb87555   1         1         1       36s
[ashu@docker-host customer-app-deploy]$ kubectl get po
NAME                               READY   STATUS    RESTARTS   AGE
ashucustomerapp-7fdbb87555-jkr6q   1/1     Running   0          40s
[ashu@docker-host customer-app-deploy]$ kubectl get cm
NAME               DATA   AGE
appenv             1      78s
kube-root-ca.crt   1      24h
[ashu@docker-host customer-app-deploy]$ kubectl get svc
NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
ashulb1   NodePort   10.104.145.51   <none>        1234:30004/TCP   84s
[ashu@docker-host customer-app-deploy]$ kubectl get ep
NAME      ENDPOINTS            AGE
ashulb1   192.168.216.127:80   89s
```

### if we change in configmap for ENV -- then to update deployment we can using rolling updates 

```
[ashu@docker-host customer-app-deploy]$ kubectl apply -f config.yaml 
configmap/appenv configured
[ashu@docker-host customer-app-deploy]$ kubectl rollout restart deployment ashucustomerapp 
deployment.apps/ashucustomerapp restarted
[ashu@docker-host customer-app-deploy]$ kubectl  get  po 
NAME                               READY   STATUS        RESTARTS   AGE
ashucustomerapp-7c7fc9f5d8-jlf7k   1/1     Running       0          6s
ashucustomerapp-7fdbb87555-m2skf   1/1     Terminating   0          93s
[ashu@docker-host customer-app-deploy]$ 


```





