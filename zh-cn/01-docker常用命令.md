# docker常用命令

### 查询镜像
```
docker images
```

### 查询正常运行容器
```
docker ps
```

### 查询正常运行容器
```
docker ps -a
```

### 复制容器文件到本机
```
docker cp 容器名称:需要复制文件路径 本机路径
```

### 复制本地文件到容器
```
docker cp 本机文件路径 容器名称:需要复制到容器中的文件路径
```
### 使用镜像以交互模式启动容器
```
docker run -it nginx:latest /bin/bash
```
### 进入容器执行命令
```
docker exec -it 容器Id /bin/bash
```
### 查看容器日志
```
docker logs -f --tail=500 容器ID
```