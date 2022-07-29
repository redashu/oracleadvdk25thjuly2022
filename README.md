# Training plan 

<img src="plan.png">

## Understanding IAM in k8s for secuirty reason 

<img src="iam.png">

### few commands for super admin to see config admin.conf file 

```
kubectl config view 
kubectl  config  view --raw 
```

### create Namespace for Developer 

```
[ashu@docker-host ~]$ kubectl create  ns   ashu-dev 
namespace/ashu-dev created
[ashu@docker-host ~]$ kubectl  get  ns  --sort-by=.metadata.name 
NAME                   STATUS   AGE
abhi-apps              Active   47h
ashu-apps              Active   47h
ashu-dev               Active   18s

```

### understanding serviceaccoutns inside Namespace 

<img src="sa.png">

### by default everynamespace is having default service account 

```
[ashu@docker-host ~]$ kubectl  get  serviceaccounts -n ashu-dev 
NAME      SECRETS   AGE
default   0         21m
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ kubectl  get  sa -n ashu-dev 
NAME      SECRETS   AGE
default   0         21m

```

### we can also create your own service accounts 

```
[ashu@docker-host ~]$ kubectl  create   sa  dev   -n ashu-dev 
serviceaccount/dev created
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ kubectl  get  sa -n ashu-dev 
NAME      SECRETS   AGE
default   0         23m
dev       0         3s
```

### for token fo service account we need to create secret 

```
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: dev-secret # name of secret 
  namespace: ashu-dev 
  annotations:
    kubernetes.io/service-account.name: "dev" # name of serviceaccount 
```

====

```
[ashu@docker-host myimages]$ kubectl apply -f iam-k8s/sasecret.yaml 
secret/dev-secret created

[ashu@docker-host ~]$ kubectl  get  secrets -n ashu-dev 
NAME         TYPE                                  DATA   AGE
dev-secret   kubernetes.io/service-account-token   3      56s
```

### fetching token from secret 

```
kubectl -n ashu-dev describe secrets dev-secret 
Name:         dev-secret
Namespace:    ashu-dev
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: dev
              kubernetes.io/service-account.uid: 7d403358-75f0-4251-84cf-6292e3b12978

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1099 bytes
namespace:  8 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6ImczdVNGUW1PTEJwREFxQXFHNHhPbVZ5cXczQ21mNTFQa25OZ2JXNjVvdmsifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJhc2h1LWRldiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkZXYtc2VjcmV0Iiwia3ViZXJuZXRlcy5pby9z
```

### lets test new token config file with new user 

```
fire@ashutoshhs-MacBook-Air ~ % ssh common-dev@129.146.109.58                   
common-dev@129.146.109.58's password: 
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[common-dev@docker-host ~]$ 
[common-dev@docker-host ~]$ 
[common-dev@docker-host ~]$ 
[common-dev@docker-host ~]$ 
[common-dev@docker-host ~]$ cp -v  /tmp/ashu-dev.conf  . 
'/tmp/ashu-dev.conf' -> './ashu-dev.conf'
[common-dev@docker-host ~]$ ls
ashu-dev.conf
[common-dev@docker-host ~]$ kubectl  get nodes  --kubeconfig  ashu-dev.conf 
Error from server (Forbidden): nodes is forbidden: User "system:serviceaccount:ashu-dev:dev" cannot list resource "nodes" in API group "" at the cluster scope
[common-dev@docker-host ~]$ ls
ashu-dev.conf  chan-dev.conf
[common-dev@docker-host ~]$ 
[common-dev@docker-host ~]$ kubectl  get pods   --kubeconfig  ashu-dev.conf 
Error from server (Forbidden): pods is forbidden: User "system:serviceaccount:ashu-dev:dev" cannot list resource "pods" in API group "" in the namespace "ashu-dev"
[common-dev@docker-host ~]$ kubectl  cluster-info    --kubeconfig  ashu-dev.conf 

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
Error from server (Forbidden): services is forbidden: User "system:serviceaccount:ashu-dev:dev" cannot list resource "services" in API group "" in the namespace "kube-system"
[common-dev@docker-host ~]$ ls
ashu-dev.conf  chan-dev.conf  pal-dev.conf
[common-dev@docker-host ~]$ 

```

### as we have seen we don't have any permission with this service account --

### role creation 

```
kubectl create role  dev-pod-role1 --resource=pods --verb=create --verb=get  --dry-run=client -o yaml >devsa-podrole1.yaml
```

## YAML file of role 

```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: null
  name: dev-pod-role1
  namespace: ashu-dev # roles are namespace dependent 
rules:
- apiGroups:
  - ""
  resources:
  - pods
  verbs:
  - create
  - get
  - list 

```

### 

```
[ashu@docker-host iam-k8s]$ kubectl apply -f  devsa-podrole1.yaml 
role.rbac.authorization.k8s.io/dev-pod-role1 created
[ashu@docker-host iam-k8s]$ kubectl get  roles -n ashu-dev 
NAME            CREATED AT
dev-pod-role1   2022-07-29T06:15:39Z
[ashu@docker-host iam-k8s]$ 

```





