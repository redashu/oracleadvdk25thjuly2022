# Training plan 

<img src="plan.png">

### appilication incompatibility in bare-metal system 

<img src="app.png">

### solution to app confict is Virtualization  -- Hypervisor

<img src="vm.png">

### virtualization is Good but Individual VM -- having os which is a problem 

<img src="prob1.png">

### Introduction to containers 

<img src="cont.png">

### Installing docker on Oracle linux 7.9 in OCI 

```
[root@docker-host ~]# yum install  docker  
Failed to set locale, defaulting to C
Loaded plugins: langpacks, ulninfo
ol7_MySQL80                                                                           | 3.0 kB  00:00:00     
ol7_MySQL80_connectors_community                                                      | 2.9 kB  00:00:00     
ol7_MySQL80_tools_community                                                           | 2.9 kB  00:00:00     
ol7_UEKR6                                                                             | 3.0 kB  00:00:00     
ol7_addons                                                                            | 3.0 kB  00:00:00     
ol7_ksplice             
```

### starting docker daemon /service 

```
[root@docker-host ~]# systemctl start  docker 
[root@docker-host ~]# systemctl  enable   docker 
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@docker-host ~]# systemctl  status    docker 
● docker.service - Docker Application Container Engine
   Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; vendor preset: disabled)
   Active: active (running) since Mon 2022-07-25 05:13:48 GMT; 13s ago
     Docs: https://docs.docker.com
 Main PID: 11824 (dockerd)
   CGroup: /system.slice/docker.service
           └─11824 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```


### user based access 

### only root user is allowed to connect to docker server 

### non root users will show given error 

<img src="error.png">

### fixing the problem by adding non root user to docker group 

```
[root@docker-host ~]# usermod -a  -G  docker  abhishek 
[root@docker-host ~]# 
[root@docker-host ~]# grep -i docker  /etc/group
docker:x:992:abhishek
[root@docker-host ~]# 
[root@docker-host ~]# 
[root@docker-host ~]# su - abhishek 
Last login: Mon Jul 25 05:15:34 GMT 2022 on pts/0
[abhishek@docker-host ~]$ whoami
abhishek
[abhishek@docker-host ~]$ docker  version 
Client: Docker Engine - Community
 Version:           19.03.11-ol
 API version:       1.40
 Go version:        go1.16.2
 Git commit:        9bb540d
 Built:             Fri Jul 23 01:33:55 2021
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.11-ol
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.16.2
  Git commit:       9bb540d
  Built:            Fri Jul 23 01:32:08 2021
  OS/Arch:          linux/amd64
  Experimental:     false
  Default Registry: docker.io
 containerd:
  Version:          v1.4.8
  GitCommit:        7eba5930496d9bbe375fdf71603e610ad737d2b2
 runc:
  Version:          1.1.1
  GitCommit:        52de29d
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```

### connecting as docker client 

```
fire@ashutoshhs-MacBook-Air ~ % ssh ashu@129.146.109.58
ashu@129.146.109.58's password: 
-bash: warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ docker  version 
Client: Docker Engine - Community
 Version:           19.03.11-ol
 API version:       1.40
 Go version:        go1.16.2
 Git commit:        9bb540d
 Built:             Fri Jul 23 01:33:55 2021
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.11-ol
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.16.2
  Git commit:       9bb540d
  Built:            Fri Jul 23 01:32:08 2021
  OS/Arch:          linux/amd64
  Experimental:     false
  Default Registry: docker.io
 containerd:
  Version:          v1.4.8
  GitCommit:        7eba5930496d9bbe375fdf71603e610ad737d2b2
 runc:
  Version:          1.1.1
  GitCommit:        52de29d
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683

```



