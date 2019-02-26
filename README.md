# docker-lnmpr
 Deploy LNMPR(Linux + Nginx + Mysql + PHP7 + Redis) using docker.


## install docker-ce

1.Uninstall old versions

```linux
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine

```

2.SET UP THE REPOSITORY

```linux
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

3.disables the nightly and test repository

```linux
$ sudo yum-config-manager --disable docker-ce-nightly
$ sudo yum-config-manager --disable docker-ce-test
```

4.INSTALL DOCKER CE

```
$ sudo yum install docker-ce docker-ce-cli containerd.io
```

5.display your-user

```
$ whoami
```

6.use Docker as a non-root user

```
$ sudo usermod -aG docker your-user
```

7.Start Docker

```
$ sudo systemctl start docker
```

##reference
[how to install docker-ce on centos](https://docs.docker.com/install/linux/docker-ce/centos/)

[Docker - 从入门到实践 by yeasy@github](https://yeasy.gitbooks.io/docker_practice/content/index.html)

