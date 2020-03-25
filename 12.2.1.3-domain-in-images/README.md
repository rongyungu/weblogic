集群规模
admin-server:1
managed-server:2
注意：如果需要修改managed-server个数，需要重新构建镜像

镜像构建
1.提供Admin Server username和password。

./properties/domain_security.properties配置文件格式：

username=myadminusername
password=myadminpassword

2.配置Weblogic域的自定义参数，如域名、端口、被管理节点数、集群类型等信息。

./properties/domain.properties

DOMAIN_NAME=domain1
ADMIN_PORT=7001
ADMIN_NAME=admin-server
ADMIN_HOST=admin-server
MANAGED_SERVER_PORT=8001
MANAGED_SERVER_NAME_BASE=managed-server
CONFIGURED_MANAGED_SERVER_COUNT=2
CLUSTER_NAME=cluster-1
DEBUG_PORT=8453
DB_PORT=1527
DEBUG_FLAG=true
PRODUCTION_MODE_ENABLED=true
CLUSTER_TYPE=DYNAMIC    #集群类型：DYNAMIC/CONFIGURED
JAVA_OPTIONS=-Dweblogic.StdoutDebugEnabled=false
T3_CHANNEL_PORT=30012
T3_PUBLIC_ADDRESS=kubernetes
IMAGE_TAG=gurongyun/weblogic:12.2.1.3-domain

3.根据自定义参数配置文件设置镜像构建参数BUILD_ARG。

Domain Name: DOMAIN_NAME (default: domain1)
Admin Port: ADMIN_PORT (default: 7001)
Managed Server Port: MANAGED_SERVER_PORT (default: 8001)
Debug Port: DEBUG_PORT (default: 8453)
Admin Server Name: ADMIN_NAME (default: admin-server)
Admin Server Host: ADMIN_HOST (default: admin-server)
Cluster Name: CLUSTER_NAME (default: cluster1)
Managed Server Name Base: MANAGED_SERVER_NAME_BASE (default: managed-server)

运行
. container-scripts ./properties/domain.properties

4.镜像构建

基础镜像:gurongyun/weblogic:12.2.1.3-generic

运行
docker build --force-rm=true --no-cache=true $BUILD_ARG -t weblogic:12.2.1.3-domain .

k8s集群部署

1. 创建k8s的namespaces

kubectl create ns weblogic

2.创建admin server的账号密码secret（username/password需要64编码加密)

kubectl apply -f ./k8s/wlsecret.yaml

3.镜像仓库密钥（不提供）

4.运用admin-server

kubectl apply -f ./k8s/admin-server.yaml

kubectl apply -f ./k8s/admin-server-svc.yaml

5.运行managed-server

kubectl apply -f ./k8s/managed-server.yaml

kubectl apply -f ./k8s/managed-server-svc.yaml

6.访问控制台

 http://IP:7001/console


