# chatgpt自建网关部署方案

## 写在前面
部署架构为1网关+N个负载节点组成，可根据自身GPT账号规模调整负载节点数量，建议GPT账号与代理节点5:1，超过5个账号需要增加代理节点

所有节点均需自行准备，要求可直连访问OpenAi（香港不行），本网关不限制速率，但需要根据账号规模搭建代理节点，

如不想购买vps，使用动态住宅ip也可，推荐一个厂商：[https://www.ipwo.net](https://www.ipwo.net/?pl=huasheng)

注意：：**网关支持转API，详情联系tg:[@sansui_qaq](https://t.me/sansui_qaq)**


## 部署流程

### 安装网关

执行一键部署脚本

```
curl -sSfL -o gateway-node-quick-install.sh https://raw.githubusercontent.com/wm-chatgpt/chatgpt-gateway-node-deploy/main/gateway-node-quick-install.sh && bash gateway-node-quick-install.sh

```
##### 安装说明
AuthKey : 接口访问秘钥，对应share配置中的AuthKey，填写free为裸奔模式，忽略鉴权

Licence : 授权码，联系客服获取，需提供主节点IP

脚本安装成功后会打印网关地址，可以自行配置反代、https等，处理完成后修改share启动参数即可：

```
CHATPROXY: "http://部署节点机器IP:8100"

AUTHKEY: "你配置的值"
```
#### 配置项说明
配置文件目录`/root/chatgpt-gateway-node/config.yaml`
```
# 代理节点地址，默认无代理  
PROXY_URL :
  - socks5://xx:yy@111.222.333.444:8443 
AUTH_KEY : 网关访问秘钥，填free，则忽视鉴权，开启裸奔模式
#网关节点参与代理，默认不参与，不支持热更新
GATEWAY_ENABLE_PROXY: false
#后端计算sentinel token 默认为false，支持热更新，true则支持api模式
BackendGenSentinel: true
LICENCE : 授权码，
AUTH_KEY_HEADER ：header中秘钥key名称，默认为AuthKey，**share网关不要配置该值**，非share网关可自行配置
```
联系客服获取授权码：[@sansui_qaq](https://t.me/sansui_qaq)
#### 监控说明
基于uptime-kuma监控，https://github.com/louislam/uptime-kuma
如图配置即可
<img width="700" alt="image" src="https://github.com/wm-chatgpt/chatgpt-gateway/assets/20039029/1d8c99df-01c9-4540-99f4-cc894799dc75">

* http://网关地址/gateway/ping
* json查询方式 关键词：forbiddenIps 期望值：none
* header增加AUTH_KEY {"authkey":"你的key"}


### 负载节点

执行一键部署脚本
```
curl -sSfL -o proxy-node-quick-install.sh https://raw.githubusercontent.com/wm-chatgpt/chatgpt-proxy-node-deploy/main/proxy-node-quick-install.sh && bash proxy-node-quick-install.sh
```
输入代理端口、账号、密码，如无特殊情况，一路回车即可

安装完成后会打印代理节点地址，复制留存

检查网关代理是否启动成功，`curl -x socks5://代理节点地址 myip.ipip.net`

编辑主节点机器 `安装目录下的config.yaml`，将代理地址填入配置文件，该文件热更新，无需重启容器，形如：

<img width="645" alt="image" src="https://github.com/wm-chatgpt/chatgpt-gateway/assets/20039029/64c6ab2d-d42b-45ec-b4c9-6cef9ac47121">




