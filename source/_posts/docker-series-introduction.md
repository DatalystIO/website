---
title: Docker系列之Docker介绍
date: 2016/11/14
---

### 历史
2000年立项, 2013年开源. 目前有6B的镜像下载量. 得到很多公司和开发者的支持.

### 术语

#### 镜像
镜像包含程序可运行的最基本的文件系统和配置.

获得镜像可以使用 `docker pull` 命令获取.

例如,

```bash
vagrant@vagrant:~$ docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
f58f84d16010: Pull complete
Digest: sha256:fb76bd8c78f158a05b2d7b3ad624d4be0d094c6645a5c713883eb6af47553881
Status: Downloaded newer image for alpine:latest
```

可以通过使用 `docker inspect your-image-name` 命令了解镜像的配置.

例如

```bash
vagrant@vagrant:~$ docker inspect alpine
[
{
    "Id": "f58f84d16010475284eae656e7c64a8efbf53f37babd7ea6078f4de2351eab05",
    "RepoTags": [
        "alpine:latest"
    ],
    "RepoDigests": [],
    "Parent": "",
    "Comment": "",
    "Created": "2016-10-18T20:31:22.321427771Z",
    "Container": "1d811a9194c47475510bc53700001c32f2b0eb8e3aca0914c5424109c0cd2056",
    "ContainerConfig": {
        "Hostname": "1d811a9194c4",
        "Domainname": "",
        "User": "",
        "AttachStdin": false,
        "AttachStdout": false,
        "AttachStderr": false,
        "Tty": false,
        "OpenStdin": false,
        "StdinOnce": false,
        "Env": [
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        ],
        "Cmd": [
            "/bin/sh",
            "-c",
            "#(nop) ADD file:7afbc23fda8b0b3872623c16af8e3490b2cee951aed14b3794389c2f946cc8c7 in / "
        ],
        "Image": "",
        "Volumes": null,
        "WorkingDir": "",
        "Entrypoint": null,
        "OnBuild": null,
        "Labels": null
    },
    "DockerVersion": "1.12.1",
    "Author": "",
    "Config": {
        "Hostname": "1d811a9194c4",
        "Domainname": "",
        "User": "",
        "AttachStdin": false,
        "AttachStdout": false,
        "AttachStderr": false,
        "Tty": false,
        "OpenStdin": false,
        "StdinOnce": false,
        "Env": [
            "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
        ],
        "Cmd": null,
        "Image": "",
        "Volumes": null,
        "WorkingDir": "",
        "Entrypoint": null,
        "OnBuild": null,
        "Labels": null
    },
    "Architecture": "amd64",
    "Os": "linux",
    "Size": 4802964,
    "VirtualSize": 4802964,
    "GraphDriver": {
        "Name": "aufs",
        "Data": null
    }
}
]
```
会列出 **alpine** 的详细配置信息.

#### 容器

容器是真正运行程序的地方. 容器之间共享系统内核, 并且以独立进程的方式运行在宿主系统中. 可以通过使用 `docker ps` 命令查看目前正在运行的 docker 容器.

#### Docker 守护进程

后台宿主程序, 负责管理创建,运行和分发 docker 容器.

#### Docker 客户端

Docker 客户端提供了命令行的方式与 Docker Daemon 进行交互.

#### Docker Hub

免费的 Docker 镜像服务器. 可将你的 docker 镜像上传到 Docker Hub上保存分享.




### Reference

- [Docker Training](http://training.docker.com/category/docker-mentor-week)
