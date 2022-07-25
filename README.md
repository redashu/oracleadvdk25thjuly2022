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

### understanding docker image concept and pull docker image from docker hub to docker server 

```
[ashu@docker-host ~]$ docker  images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
[ashu@docker-host ~]$ docker pull mysql
Using default tag: latest
Trying to pull repository docker.io/library/mysql ... 
latest: Pulling from docker.io/library/mysql
e54b73e95ef3: Pull complete 
327840d38cb2: Pull complete 
642077275f5f: Pull complete 
e077469d560d: Pull complete 
cbf214d981a6: Pull complete 
7d1cc1ea1b3d: Pull complete 
d48f3c15cb80: Pull complete 
94c3d7b2c9ae: Pull complete 
f6cfbf240ed7: Pull complete 
e12b159b2a12: Pull complete 
4e93c6fd777f: Pull complete 
Digest: sha256:152cf187a3efc56afb0b3877b4d21e231d1d6eb828ca9221056590b0ac834c75
Status: Downloaded newer image for mysql:latest
mysql:latest
[ashu@docker-host ~]$ docker  images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mysql               latest              33037edcac9b        11 days ago         444MB
[ashu@docker-host ~]$ 


```


### pulling more image 

```
[ashu@docker-host ~]$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
openjdk             latest              04bf630c9556        4 days ago          464MB
alpine              latest              d7d3d98c851f        6 days ago          5.53MB
mysql               latest              33037edcac9b        11 days ago         444MB
mongo               latest              c8b57c4bf7e3        5 weeks ago         701MB
oraclelinux         8.4                 97e22ab49eea        8 months ago        246MB
[ashu@docker-host ~]$ 

[ashu@docker-host ~]$ docker pull quay.io/libpod/ubuntu
Using default tag: latest
Trying to pull repository quay.io/libpod/ubuntu ... 
latest: Pulling from quay.io/libpod/ubuntu
da7391352a9b: Pull complete 
14428a6d4bcd: Pull complete 
2c2d948710f2: Pull complete 
Digest: sha256:c95a8e48bf88e9849f3e0f723d9f49fa12c5a00cfc6e60d2bc99d87555295e4c
Status: Downloaded newer image for quay.io/libpod/ubuntu:latest
quay.io/libpod/ubuntu:latest
[ashu@docker-host ~]$ 
[ashu@docker-host ~]$ docker  images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
openjdk                 latest              04bf630c9556        4 days ago          464MB
alpine                  latest              d7d3d98c851f        6 days ago          5.53MB
mysql                   latest              33037edcac9b        11 days ago         444MB
tomee                   latest              a3e6e1133a38        12 days ago         361MB
mongo                   latest              c8b57c4bf7e3        5 weeks ago         701MB
oraclelinux             8.4                 97e22ab49eea        8 months ago        246MB
quay.io/libpod/ubuntu   latest              f643c72bc252        20 months ago       72.9MB
[ashu@docker-host ~]$ 

```


### creating containers from images 

### importance of process in containers 

<img src="process.png">

### creating our first container 

<img src="cont1.png">

```
docker  run  -d  --name  ashuc1  alpine:latest  ping  google.com 

```

### listing running containers 

```
[ashu@docker-host ~]$ docker  ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
b912115bf7ff        alpine              "ping google.com"   19 seconds ago      Up 18 seconds                           sahil1
13c6027adcbb        alpine:latest       "ping google.com"   24 seconds ago      Up 23 seconds                           gauravc1
cf4226260df8        alpine:latest       "ping oracle.com"   36 seconds ago      Up 34 seconds                           omeshs
6b6e2135be79        alpine:latest       "ping google.com"   2 minutes ago       Up 2 minutes                            ashuc1
```


### all the containers listing 

```
[ashu@docker-host ~]$ docker  ps  -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
8be37de02f2a        alpine:latest       "ping google.com"   31 seconds ago      Up 30 seconds                                  ayush2
9fe6ed99827f        alpine:latest       "ping google.com"   3 minutes ago       Up 3 minutes                                   eashu
dcba7ed6e1df        alpine:latest       "ping google.com"   4 minutes ago       Up 4 minutes                                   pallavi1
cf421ee95e66        alpine:latest       "ping google.com"   4 minutes ago       Up 4 minutes                                   var2
cd1355d28056        alpine:latest       "ping google.com"   4 minutes ago       Up 4 minutes                                   apoorvc1
420d8406f88c        alpine:latest       "ping google.com"   5 minutes ago       Up 5 minutes                                   abhishek
4ea07348afd3        alpine:latest       "date"              5 minutes ago       Exited (0) 5 minutes ago                       ayush1
```

### output of containers process 

```
 31  docker  logs  ashuc1
   32  docker  logs  -f  ashuc1
```

### accessing container shell and coming out 

```
[ashu@docker-host ~]$ docker  exec  -it  ashuc1  sh  
/ # 
/ # whoami
root
/ # ps  -e 
PID   USER     TIME  COMMAND
    1 root      0:00 ping google.com
    6 root      0:00 sh
   12 root      0:00 ps -e
/ # ifconfig  
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:02  
          inet addr:172.17.0.2  Bcast:172.17.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:825 errors:0 dropped:0 overruns:0 frame:0
          TX packets:796 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:77890 (76.0 KiB)  TX bytes:75880 (74.1 KiB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

/ # ls  /
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # exit

```
### more container 

```
 docker  stop  ashuc1
 docker  start  ashuc1
 [ashu@docker-host ~]$ docker  stop ashuc1
ashuc1
[ashu@docker-host ~]$ docker  rm  ashuc1
ashuc1

```

### container life cycle

<img src="clife.png">

### solution of question 1 

```
[ashu@docker-host ~]$ docker run -d --name ashuc1  alpine  ping fb.com 
6f318576f23a36fa7fa865eca0c93cfa2eb1ae407c1109a357fa25609fa9b945
[ashu@docker-host ~]$ docker run -d --name ashuc2  alpine  ping fb.com 
a9dd4c00c5f0f8b051789258fed3937524a564d67a3e4e33f71cbab4eaf924a8
[ashu@docker-host ~]$ docker exec -it  ashuc1  sh 
/ # pwd
/
/ # ls
bin    dev    etc    home   lib    media  mnt    opt    proc   root   run    sbin   srv    sys    tmp    usr    var
/ # echo "hey i am alpine contaienr "  >helloc1.txt 
/ # s
sh: s: not found
/ # ls
bin          etc          home         media        opt          root         sbin         sys          usr
dev          helloc1.txt  lib          mnt          proc         run          srv          tmp          var
/ # pwd
/
/ # exit
[ashu@docker-host ~]$ ls
[ashu@docker-host ~]$ docker  cp   ashuc1:/helloc1.txt  . 
[ashu@docker-host ~]$ ls
helloc1.txt
[ashu@docker-host ~]$ cat helloc1.txt 
hey i am alpine contaienr 
[ashu@docker-host ~]$ docker  cp  helloc1.txt   ashuc2:/
[ashu@docker-host ~]$ docker  exec -it ashuc2 ls  / 
bin          helloc1.txt  media        proc         sbin         tmp
dev          home         mnt          root         srv          usr
etc          lib          opt          run          sys          var
```

### some more adv commands in docker cli 

```
  62  docker  stop  $(docker  ps  -q)
   63  docker  ps
   64  docker  start  $(docker  ps  -qa)
   65  docker  ps
   66  history 
   67  docker  kill   $(docker  ps  -q)
   68  docker  ps 
   69  docker  ps -a
   70  docker  ps -aq
   71  docker rm $(docker  ps -aq)
```


### app to container journey 

<img src="app2cont.png">

### to write / change in sample apps and in Dockerfile we will be using vscode 

[link_to_download](https://code.visualstudio.com/download)

### Dockerfile demo with sample python Code 

### python code 

```
import time
while True:
    print("Hello world this is my container ")
    time.sleep(2)
    print("PYthon sample code by ashutoshh ")
```

### Dockerfile 

```
FROM python 
# we are calling python image from docker hub 
LABEL name=ashutoshh
LABEL email=ashutoshh@linux.com
# optional field but you can share image creator info 
RUN mkdir /mycode 
# it to get shell inside container during image build time 
COPY oracle.py /mycode/
# copy code from Dockerfile location to image which is going to build 
WORKDIR /mycode
# changing directory during image build time 
CMD ["python","oracle.py"]
# use of CMd is to set default process 
# note: we can't have more than one CMD because it container can have only one process 
```


### running dockerfile 

```
[ashu@docker-host myimages]$ pwd
/home/ashu/myimages
[ashu@docker-host myimages]$ ls
[ashu@docker-host myimages]$ mkdir  javacode  pythoncode golang  webapps 
[ashu@docker-host myimages]$ ls
golang  javacode  pythoncode  webapps
[ashu@docker-host myimages]$ cd pythoncode/
[ashu@docker-host pythoncode]$ ls
Dockerfile  oracle.py
[ashu@docker-host pythoncode]$ ls
Dockerfile  oracle.py
[ashu@docker-host pythoncode]$ docker  build  -t  ashupython:v1  . 
Sending build context to Docker daemon  3.584kB
Step 1/7 : FROM python
Trying to pull repository docker.io/library/python ... 
latest: Pulling from docker.io/library/python
d836772a1c1f: Already exists 
66a9e63c657a: Already exists 
d1989b6e74cf: Already exists 
c28818711e1e: Pull complete 
5084fa7ebd74: Extracting [======================================>            ]  152.6MB/196.8MB
7f162c881e4f: Download complete 
e3f48ccb2876: Download complete 
```

### checking images 

```
[ashu@docker-host pythoncode]$ docker  images
REPOSITORY                   TAG                 IMAGE ID            CREATED                  SIZE
tapupython                   v1                  65ea4743cc77        Less than a second ago   920MB
palpython                    v1                  32ca8447c46f        6 seconds ago            920MB
apoorvpython                 v1                  df77c73105d6        22 seconds ago           920MB
omeshs                       v1                  e91298f7b7ce        23 seconds ago           920MB
somefile                     v1                  761fcf50d7a9        32 seconds ago           920MB
ayushpython                  v1                  fa387c482feb        52 seconds ago           920MB
sahilphython                 v1                  e71d84ad1d31        59 seconds ago           920MB
chanpython                   v1                  7a24793f19ff        About a minute ago       920MB
gauravpython                 v1                  c60b3a2834f8        About a minute ago       920MB
ashupython                   v1                  38515e8260e1        About a minute ago       920MB
```

### remove image if you want to 

```
 docker rmi  ashupython:v1 
```

### an alternativ Dockerfile 

```
FROM oraclelinux:8.4
LABEL email=ashutoshh@linux.com 
RUN yum install python3 -y && mkdir  /mycode 
COPY oracle.py  /mycode/
WORKDIR /mycode
CMD ["python3","oracle.py"]

```

### using this docker

```
[ashu@docker-host pythoncode]$ docker build -t  ashupython:v2 -f  oracle.dockerfile . 
Sending build context to Docker daemon  4.608kB
Step 1/6 : FROM oraclelinux:8.4
 ---> 97e22ab49eea
Step 2/6 : LABEL email=ashutoshh@linux.com
 ---> Running in ad0a51e2566e
Removing intermediate container ad0a51e2566e
 ---> ada32b2e4266
Step 3/6 : RUN yum install python3 -y && mkdir  /mycode
 ---> Running in f72de32850d6
Oracle Linux 8 BaseOS Latest (
```

### creating container 

```
[ashu@docker-host pythoncode]$ docker  run -d  -it  --name ashuc1  ashupython:v2  
506be4629948cc015de4c2458b4b2d7b81488ccfc657824b65679a7b231362da
[ashu@docker-host pythoncode]$ docker  ps
CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS               NAMES
506be4629948        ashupython:v2       "python3 oracle.py"   7 seconds ago       Up 6 seconds                            ashuc1
```

### checking resources consumption 

```
[ashu@docker-host pythoncode]$ docker  stats  

CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
9b6daa5c807d        pallavic1           0.01%               3.418MiB / 15.35GiB   0.02%               866B / 0B           0B / 0B             1
be5c748d8642        ayushc1             0.01%               3.422MiB / 15.35GiB   0.02%               936B / 0B           0B / 0B             1
4f77f6c0a11f        omeshc1             0.00%               3.488MiB / 15.35GiB   0.02%               1.1kB / 0B          0B / 0B             1
28bacb8bd869        varpy               0.01%               3.406MiB / 15.35GiB   0.02%               1.1kB / 0B          0B / 0B             1
6afa68ce107e        chanpython          0.01%               3.418MiB / 15.35GiB   0.02%               1.17kB / 0B         0B / 0B             1
89e85102111e        sahilc1phython      0.00%               3.422MiB / 15.35GiB   0.02%               1.17kB / 0B         0B / 0B             1
506be4629948        ashuc1              0.01%               3.414MiB / 15.35GiB   0.02%               1.17kB / 0B         0B / 0B             1
19b70bab48d0        apoorvpythonv2      0.00%               3.523MiB / 15.35GiB   0.02%               1.44kB / 0B         0B / 0B             1
^C

```

### docker images sharing 

<img src="reg.png">

## steps to push images to docker hub 

### tagging 

```
docker  tag  ashualp:pycodev1   docker.io/dockerashu/ashualp:1.0

```

### login 
```
[ashu@docker-host ~]$ docker  login  
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: dockerashu
Password: 
WARNING! Your password will be stored unencrypted in /home/ashu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

```

### pushing 

```
[ashu@docker-host ~]$ docker  push  docker.io/dockerashu/ashualp:1.0
The push refers to repository [docker.io/dockerashu/ashualp]
ba976c86024d: Pushed 
8c97e55265e5: Pushed 
ec34fcc1d526: Mounted from library/alpine 
1.0: digest: sha256:9f53e753d4fee300b36a2d1a844ec9c90785fe56479d3b91a48eba7499733e46 size: 947
```

### optiona step --logout 

```
[ashu@docker-host ~]$ docker  logout 
Removing login credentials for https://index.docker.io/v1/
```



