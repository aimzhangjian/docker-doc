# 推送docker镜像到hskp库

### 拉取猪齿鱼agent镜像
```
docker pull registry.choerodon.com.cn/hzero-hrds/hskp-devops-cluster-agent:0.1.0
```
### 登录要上传的镜像仓库
```
docker login harbor.open.hand-china.com -u hsopDeployer -p kjDJHGDJshfaflasfa34
```
### 调整标记指定制品库地址和版本
```
docker tag registry.choerodon.com.cn/hzero-hrds/hskp-devops-cluster-agent:0.2.0 harbor.open.hand-china.com/hskp/hskp-devops-cluster-agent:0.2.0
```
### 推送镜像
```
docker push harbor.open.hand-china.com/hskp/hskp-devops-cluster-agent:0.2.0
```


