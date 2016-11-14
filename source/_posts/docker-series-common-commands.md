---
title: Docker系列之常用命令
date: 2016/11/14
---

### 常用命令

#### 创建镜像
如果你已经有了 **Dockerfile**, 可以通过使用 `docker build` 命令创建镜像.

例如, 我的 Dockerfile 为

```bash
FROM nginx
ENV AUTHOR=Docker

WORKDIR /usr/share/nginx/html
COPY hello_docker.html /usr/share/nginx/html

CMD cd /usr/share/nginx/html && sed -e s/Docker/"$AUTHOR"/ hello_docker.html > index.html ; nginx -g 'daemon off;'
```

我可以通过使用 `docker build -t static-site .` 这条命令创建标签为 **static-site** 的镜像.

```bash
vagrant@vagrant:~/static-site$ docker build -t static-site .
Sending build context to Docker daemon 11.78 kB
Step 1 : FROM nginx
latest: Pulling from library/nginx

1f2a121fc3e4: Pull complete
8d8b679a55bc: Pull complete
a6c255f07aa8: Pull complete
688deb3ebd31: Pull complete
a906acdecdc6: Pull complete
49c4ad89029f: Pull complete
4205260e35c3: Pull complete
ef068b58f0cd: Pull complete
Digest: sha256:55219a0643014008ee3b0ea9a6aac749867a3f42f1bd408431acc4a1b2947699
Status: Downloaded newer image for nginx:latest
 ---> ef068b58f0cd
Step 2 : ENV AUTHOR Docker
 ---> Running in 9847060f72e2
 ---> 4f56aa91a67b
Removing intermediate container 9847060f72e2
Step 3 : WORKDIR /usr/share/nginx/html
 ---> Running in 0d64d28eb19e
 ---> 51f45ec68634
Removing intermediate container 0d64d28eb19e
Step 4 : COPY hello_docker.html /usr/share/nginx/html
 ---> 4d67df4f9678
Removing intermediate container 6d6e30274514
Step 5 : CMD cd /usr/share/nginx/html && sed -e s/Docker/"$AUTHOR"/ hello_docker.html > index.html ; nginx -g 'daemon off;'
 ---> Running in 29de2f027129
 ---> 51cfaace4ea4
Removing intermediate container 29de2f027129
Successfully built 51cfaace4ea4
```

通过使用 `docker images` 命令可以看到, static-site 镜像已经创建成功.
```bash
vagrant@vagrant:~/static-site$ docker images
REPOSITORY                   TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
static-site                  latest              51cfaace4ea4        2 minutes ago       181.5 MB
```

使用 `docker run` 命令可以运行创建的镜像,

```bash
vagrant@vagrant:~/static-site$ docker run -d static-site
097c8468e1cb044295668a745b72d03554c5f2122364fe18d64ac3abca9b400f
vagrant@vagrant:~/static-site$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
097c8468e1cb        static-site         "/bin/sh -c 'cd /usr/"   6 seconds ago       Up 6 seconds        80/tcp, 443/tcp     tender_hugle
```

这说明镜像已经运行成功!

停止镜像可以使用 `docker stop your-container-id`.

删除镜像可以使用 `docker rm your-container-id`.
