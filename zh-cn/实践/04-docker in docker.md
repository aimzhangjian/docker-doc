# docker in docker
在做CI流水线时，需要在流水线中使用docker完成应用镜像打包。无论你的CI流水线是在jenkins中运行，还是在gitlab runner中运行，只要你的jenkins或
gitlab runner是通过docker容器运行，都面临着docker in docker问题

没有办法在一个docker运行的容器中再安装一个docker。但官方提供了docker:dind镜像，提供docker daemon服务。docker:1.12.6镜像则提供了docker
客户端

通常解决docker in docker问题有如下几种方式

- 使用主机上的docker daemon服务：这种方式会造成对主机docker服务的污染，因为会使用主机的docker服务制作镜像，可能会覆盖已有镜像

- 使用官方提供的docker:dind镜像提供的docker daemon服务：这种方式不会对宿主机上的docker服务造成影响，但需要使用--privileged开启特权模式

- 使用Nestybox sysbox提供隔离运行时借助docker:dind镜像提供docker daemon服务：这种方式既能防止对宿主机上的docker服务的污染，又能通过 
  sysbox虚拟运行时，避免特权模式导致的安全问题

## 使用主机上的docker daemon服务
可以在已经安装docker服务的主机上运行如下指令，共享宿主机的docker服务
```
docker run -v /var/run/docker.sock:/var/run/docker.sock -ti docker
```
- docker镜像是官方提供的包含docker客户端的镜像

- 将宿主机的docker.sock文件挂载到docker容器的/var/run/docker.sock，使用宿主机的docker服务

- 可以在运行的容器中执行docker指令管理宿主机上的docker

## 使用官方提供的docker:dind镜像提供的docker daemon服务
1. 首先使用docker:dind镜像运行一个容器
```
docker run --privileged -d --name mydocker docker:1.12.6-dind
```
- docker:dind是官方提供的包含docker daemon服务的镜像

- 需要使用特权模式启动，存在安全隐患

2. 运行一个包含docker客户端的容器，并通过link与上面运行的docker:dind的容器进行通信，使用其中的docker daemon服务
```
docker run -it --rm --link mydocker:docker  docker:1.12.6
```
- 使用link与mydocker容器通信，使用其中的docker服务

- 避免污染宿主机。可以执行docker指令管理mydocker容器中的docker服务

## 使用Nestybox sysbox提供隔离运行时借助docker:dind镜像提供docker daemon服务
1. 根据官方文档安装sysbox运行时环境 https://github.com/nestybox/sysbox#installing-sysbox

2. 使用sysbox运行时启动docker:dind容器
```
docker run --runtime=sysbox-runc --name sysbox-dind -d docker:1.12.6-dind
```

3. 运行一个包含docker客户端的容器，并通过link与上面运行的docker:dind的容器进行通信，使用其中的docker daemon服务
```
docker run -it --rm --link sysbox-dind:docker  docker:1.12.6
```