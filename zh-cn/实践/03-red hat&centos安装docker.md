# Linux安装docker

## Red Hat安装docker
1. 清理已经安装的docker
```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine \
                  podman \
                  runc
```
- 对于Red Hat记得要移除旧的podman、runc，如果不移除可能后面安装docker时会有版本冲突

2. 安装必要工具
```
yum install -y yum-utils
```

3. 添加下载源
```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
- 不要用官网给的RHEL给的下载源https://download.docker.com/linux/rhel/docker-ce.repo

4. 查看可用版本列表
```
yum list docker-ce --showduplicates | sort -r
yum list docker-ce-cli --showduplicates | sort -r
```

5. 安装需要版本docker及docker-compose
```
yum install docker-ce-20.10.14 docker-ce-cli-20.10.14 containerd.io docker-compose-plugin
```

6. 确认安装版本及是否成功安装
```
docker version

docker compose version
```

7. 启动docker
```
systemctl start docker
```

## CentOS安装docker

1. 清理已经安装的docker
```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2. 安装必要工具
```
yum install -y yum-utils epel-release
```

3. 添加下载源
```
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
- 不要用官网给的RHEL给的下载源https://download.docker.com/linux/rhel/docker-ce.repo

4. 查看可用版本列表
```
yum list docker-ce --showduplicates | sort -r
yum list docker-ce-cli --showduplicates | sort -r
```

5. 安装需要版本docker及docker-compose
```
yum install docker-ce-20.10.14 docker-ce-cli-20.10.14 containerd.io docker-compose-plugin
```

6. 确认安装版本及是否成功安装
```
docker version

docker compose version
```

7. 启动docker
```
systemctl start docker
```

## 修改存储目录
1. 停用docker
```
systemctl stop docker
```

2. 创建存储目录
```
mkdir -p /user01/data/docker
```

3. 修改daemon.json文件,如果没有则新建
```
mkdir -p /etc/docker

cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ],
  "registry-mirrors": ["https://uyah70su.mirror.aliyuncs.com"],
  "data-root": "/user01/data/docker"
}
EOF
```
4. 重启docker使配置生效
```
systemctl daemon-reload

systemctl restart docker

## 配置开机重启
systemctl enable docker
```

5. 确认修改是否生效
```
docker info
```