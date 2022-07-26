# Training plan 

<img src="plan.png">

### installing docker-compose tool for managing multiple containers 

```
[root@docker-host ~]# curl -SL https://github.com/docker/compose/releases/download/v2.7.0/docker-compose-linux-x86_64 -o /usr/bin/docker-compose 
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 24.5M  100 24.5M    0     0  12.1M      0  0:00:02  0:00:02 --:--:-- 24.1M
[root@docker-host ~]# 
[root@docker-host ~]# 
[root@docker-host ~]# chmod +x /usr/bin/docker-compose 
[root@docker-host ~]# 
[root@docker-host ~]# docker-compose version 
Docker Compose version v2.7.0

```

## writing compose file in YAML 

### compose example 1 

```
version: '3.8'
services:
  ashuapp1: # app
    image: alpine
    container_name: ashuc1
    command: ping fb.com 
  
```

### lets run a compose file 

```
[ashu@docker-host ashu-compose]$ ls
docker-compose.yaml
[ashu@docker-host ashu-compose]$ docker-compose up -d
[+] Running 2/2
 ⠿ Network ashu-compose_default  Created                                                     0.1s
 ⠿ Container ashuc1              Started                                                     0.2s
[ashu@docker-host ashu-compose]$ ls
docker-compose.yaml
[ashu@docker-host ashu-compose]$ docker-compose  ps
NAME                COMMAND             SERVICE             STATUS              PORTS
ashuc1              "ping fb.com"       ashuapp1            running             
[ashu@docker-host ashu-compose]$ 
```

### more operations 

```
[ashu@docker-host ashu-compose]$ docker-compose  ps
NAME                COMMAND             SERVICE             STATUS              PORTS
ashuc1              "ping fb.com"       ashuapp1            running             
[ashu@docker-host ashu-compose]$ docker-compose  stop 
[+] Running 1/1
 ⠿ Container ashuc1  Stopped                                                                10.4s
[ashu@docker-host ashu-compose]$ docker-compose  ps
NAME                COMMAND             SERVICE             STATUS              PORTS
ashuc1              "ping fb.com"       ashuapp1            exited (137)        
[ashu@docker-host ashu-compose]$ docker-compose  start
[+] Running 1/1
 ⠿ Container ashuc1  Started                                                                 0.5s
[ashu@docker-host ashu-compose]$ docker-compose  ps
NAME                COMMAND             SERVICE             STATUS              PORTS
ashuc1              "ping fb.com"       ashuapp1            running             
[ashu@docker-host ashu-compose]$ 
```

### using exec in compose 

```
[ashu@docker-host ashu-compose]$ docker-compose  exec  ashuapp1  sh 
/ # 
/ # 
/ # ls
bin    etc    lib    mnt    proc   run    srv    tmp    var
dev    home   media  opt    root   sbin   sys    usr
/ # exit
```

### clean up 

```
[ashu@docker-host ashu-compose]$ docker-compose  down 
[+] Running 2/2
 ⠿ Container ashuc1              Removed                                                    10.4s
 ⠿ Network ashu-compose_default  Removed                                                     0.1s
[ashu@docker-host ashu-compose]$ 
```
### example 2 

```
version: '3.8'
services:
  ashudbserver: # mydb db server container 
    image: mysql
    container_name: ashudbc1
    environment:
      MYSQL_ROOT_PASSWORD: "Docker@123#"
  dbclient: # webui client of DB 
    image: adminer
    container_name: ashudbclient
    ports: # left side port is for host expose so use any in range of 1024-2000
      - "1234:8080"
```

### lets run it 

```
[ashu@docker-host ashu-compose]$ ls
docker-compose.yaml  mysqldb.yaml
[ashu@docker-host ashu-compose]$ docker-compose -f mysqldb.yaml  up  -d
[+] Running 3/3
 ⠿ Network ashu-compose_default  Created                                                     0.1s
 ⠿ Container ashudbc1            Started                                                     0.9s
 ⠿ Container ashudbclient        Started                                                     1.0s
[ashu@docker-host ashu-compose]$ docker-compose -f mysqldb.yaml  ps
NAME                COMMAND                  SERVICE             STATUS              PORTS
ashudbc1            "docker-entrypoint.s…"   ashudbserver        running             3306/tcp, 33060/tcp
ashudbclient        "entrypoint.sh docke…"   dbclient            running             0.0.0.0:1234->8080/tcp
[ashu@docker-host ashu-compose]$ 


```




