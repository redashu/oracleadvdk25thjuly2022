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
          configMapRef: # from Configmap 
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





