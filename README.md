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
### HPA in k8s 

<img src="hpa.png">

### deployment with restricted vertical scaling 

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
      labels: # labels of pod 
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
        resources: # dynamic resources 
          requests:
            memory: 200M 
            cpu: 100m # 1vcpu === 1000 Milicore 
          limits: 
            memory: 400M 
            cpu: 300m 
status: {}

```

### lets redeploy deployment 

```
ashu@docker-host customer-app-deploy]$ kubectl apply -f deployment.yaml 
deployment.apps/ashucustomerapp configured
[ashu@docker-host customer-app-deploy]$ kubectl  get po 
NAME                               READY   STATUS        RESTARTS   AGE
ashucustomerapp-5c78756686-gbp4t   1/1     Running       0          3s
ashucustomerapp-5d4f579f7b-b7p2n   1/1     Terminating   0          47m
[ashu@docker-host customer-app-deploy]$ 

```

### implementing autoscaling 

```
[ashu@docker-host customer-app-deploy]$ kubectl get deploy 
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
ashucustomerapp   1/1     1            1           77m
[ashu@docker-host customer-app-deploy]$ kubectl autoscale deployment ashucustomerapp --min=3  --max=10   --cpu-percent 80  --dry-run=client -o yaml  >hpa.yaml 
[ashu@docker-host customer-app-deploy]$ kubectl apply -f hpa.yaml 
horizontalpodautoscaler.autoscaling/ashucustomerapp created
[ashu@docker-host customer-app-deploy]$ kubectl get  hpa
NAME              REFERENCE                    TARGETS         MINPODS   MAXPODS   REPLICAS   AGE
ashucustomerapp   Deployment/ashucustomerapp   <unknown>/80%   3         10        0          6s
[ashu@docker-host customer-app-deploy]$ kubectl get deployments
NAME              READY   UP-TO-DATE   AVAILABLE   AGE
ashucustomerapp   1/1     1            1           78m
```

### hpa 

```
[ashu@docker-host customer-app-deploy]$ kubectl get hpa
NAME              REFERENCE                    TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
ashucustomerapp   Deployment/ashucustomerapp   1%/80%    3         10        3          2m9s
[ashu@docker-host customer-app-deploy]$ 
```

### CLoud controller in k8s 

<img src="clc.png">

### loadbalancer service 

```
ashu@docker-host customer-app-deploy]$ kubectl expose deployment ashucustomerapp --type LoadBalancer  --port 80 --name x1

Error from server (AlreadyExists): services "x1" already exists
[ashu@docker-host customer-app-deploy]$ kubectl get  svc 
NAME      TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
ashulb1   NodePort       10.104.145.51   <none>        1234:30004/TCP   137m
x1        LoadBalancer   10.99.7.7       <pending>     80:32586/TCP     14s
[ashu@docker-host customer-app-deploy]$ 
```






