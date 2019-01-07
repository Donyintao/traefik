# traefik 简介

traefik(https://traefik.io/) 是一款开源的反向代理与负载均衡工具。非常适合与微服务系统结合，可以实现自动化动态配置。目前支持Docker，Swarm，Mesos/Marathon，Mesos，Kubernetes，Consul，Etcd，Zookeeper，BoltDB，Rest API等等后端模型。

## 为什么选择 traefik？

- 单文件部署，与系统无关，同时也提供小尺寸 Docker 镜像。
- 支持 Docker/Etcd 后端，天然连接我们的微服务集群。
- 内置 Web UI，管理相对方便。
- 自动配置 ACME(Let’s Encrypt) 证书功能。
- 性能尚可，我们也没有到压榨 LB 性能的阶段，易用性更重要。
- Restful API支持。
- 支持后端健康状态检查，根据状态自动配置。
- 支持动态加载配置文件和 graceful 重启。
- 支持WebSocket和HTTP/2。

## 部署Traefik

#### TLS Certificate证书配置

``` bash
# mkdir -p /tmp/sslTmp && cd /tmp/sslTmp
# openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=traefik-ui.testing.com"
# kubectl create secret generic traefik-cert --from-file=tls.crt --from-file=tls.key -n kube-system
```

#### Kubernetes ConfigMap配置

说明: 同时允许http和https服务

``` bash
# vim traefik.toml
defaultEntryPoints = ["http","https"]
[entryPoints]
  [entryPoints.http]
  address = ":80"
  [entryPoints.https]
  address = ":443"
    [entryPoints.https.tls]
      [[entryPoints.https.tls.certificates]]
      CertFile = "/ssl/tls.crt"
      KeyFile = "/ssl/tls.key"
       
# kubectl create configmap traefik-conf --from-file=traefik.toml -n kube-system
```

说明: 只允许https服务

``` bash
defaultEntryPoints = ["http","https"]
[entryPoints]
  [entryPoints.http]
  address = ":80"
    [entryPoints.http.redirect]
    entryPoint = "https"
  [entryPoints.https]
  address = ":443"
    [entryPoints.https.tls]
      [[entryPoints.https.tls.certificates]]
      certFile = "/ssl/tls.crt"
      keyFile = "/ssl/tls.key"
      
# kubectl create configmap traefik-conf --from-file=traefik.toml -n kube-system
```

#### Traefik install

``` bash
# kubectl apply -f https://raw.githubusercontent.com/Donyintao/traefik/master/traefik-rbac.yaml
# kubectl apply -f https://raw.githubusercontent.com/Donyintao/traefik/blob/master/traefik-deployment.yaml
```

#### Traefik Ingress

``` bash
# https://raw.githubusercontent.com/Donyintao/traefik/master/traefik-ingress.yaml
```

#### 验证
待补充...