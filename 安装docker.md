# Docker 安装系列
环境：

```
$ uname -a
```
```
$ yum update -y
```

```
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
```
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
```
$ sudo yum install docker-ce -y
```
```
$ sudo systemctl start docker
```
```
$ sudo systemctl enable docker
```
```
$ sudo docker run hello-world
```

## 安装
```
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```

```
sudo chmod +x /usr/local/bin/docker-compose
```

```
$ docker-compose --version
```


#### 参考资料：
[Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/#install-docker-ce)

[Install Docker Compose](https://docs.docker.com/compose/install/)
