> ## Documentation Index
> Fetch the complete documentation index at: https://gitee.com/Tencent-RTC/docs/raw/main/zh/llms.txt
> Use this file to discover all available pages before exploring further.

## 功能描述

本文主要介绍应对防火墙受限的最佳实践。例如在企业内网这类有防火墙的网络环境中，往往由于防火墙的原因，无法正常使用 TRTC Web SDK。这种情况下，有两种解决方案：

- 方案一：监听 SDK 错误，引导更换网络 或者 配置防火墙白名单。
- 方案二：使用 Nginx + coturn 代理方案。

> TRTC Web SDK 默认使用 UDP 与 TRTC 服务器进行媒体通信，并且有内置的 Turn Server，支持以 UDP or TCP 中转媒体数据。
> 正常在公网下，用户无需搭建任何代理，SDK 会按直连、Turn Server UDP、Turn Server TCP 的顺序尝试建立媒体连接。
> 若明确知道用户将在内网防火墙内使用，则可能会遇到无法建立媒体连接，此时则需要搭建代理。

## 前提条件

- 方案二需要搭建两个服务器，Nginx + Turn Server，您可以联系贵公司的运维同学协助帮忙搭建。Nginx 代理服务器用于代理转发 TRTC Web SDK 的 Websocket 信令数据包；Turn Server 用于转发音视频数据包。

## 实现流程

### 方案一

> 该方案适用于：您无法确定用户的网络是否会受防火墙限制，此时可以监听 SDK 错误，引导用户更换网络 or 检查防火墙。

当您调用 startLocalVideo, startLocalAudio, startRemoteVideo 等 API 时，SDK 内部会建立媒体连接通道，用于传输音视频数据。当遇到防火墙受限时，SDK 可能无法建立连接，此时 SDK 会抛出防火墙受限的错误，并继续进行重试。

您可以参照如下代码示例，监听该错误，并引导用户更换网络 or 检查网络防火墙，对 TRTC Web SDK 使用到的域名和端口开白名单。参考：[TRTC Web SDK 域名和端口白名单](https://cloud.tencent.com/document/product/647/34399#webrtc-.E9.9C.80.E8.A6.81.E9.85.8D.E7.BD.AE.E5.93.AA.E4.BA.9B.E7.AB.AF.E5.8F.A3.E6.88.96.E5.9F.9F.E5.90.8D.E4.B8.BA.E7.99.BD.E5.90.8D.E5.8D.95.EF.BC.9F)。

```js
trtc.on(TRTC.EVENT.ERROR, error => {
  // 用户网络防火墙受限，会导致无法正常进行音视频通话。
  // 此时引导用户更换网络 or 检查网络防火墙设置。
  if (error.code === TRTC.ERROR_CODE.OPERATION_FAILED && error.extraCode === 5501) {
    
  }
});
```

### 方案二

> 该方案适用于：您确定用户的网络是受防火墙限制的，此时需要搭建代理服务器来解决。

| 方案 | 适用场景                           | 网络要求                                                                     |
| ------ | ---------------------------------- | ---------------------------------------------------------------------------- |
| 2-A | 用户网络可以访问特定的外网代理服务器   | 代理服务器搭建在外网，内网防火墙需开通白名单，允许内网用户访问外网的代理服务器。       |
| 2-B | 用户网络只能访问内网代理服务器 | 代理服务器搭建在内网，内网防火墙需开通白名单，允许内网代理服务器访问外网。 |

<div style="display: flex;justify-content: center;">
  <img src="./assets/proxy-1.png"/>
</div>

  <div style="display: flex;justify-content: center;">
    2-A 方案示例图
  </div>

<div style="display: flex;justify-content: center;">
	<img src="./assets/proxy-2.png" />
</div>

<div style="display: flex;justify-content: center;">2-B 方案示例图</div>

### 设置代理服务器

当您搭建好 Nginx 和 Turn server 后，可以参考如下示例，设置代理服务器。

```javascript
const trtc = TRTC.create(); 

await trtc.enterRoom({
  ...,
  proxy: {
    // 设置 Websocket 代理，用于中转 SDK 与 TRTC 后台的信令数据包。
    websocketProxy: 'wss://proxy.example.com/ws/',
    // 设置 turn server，用于中转 SDK 与 TRTC 后台的媒体数据包。14.3.3.3:3478 为 turn server 的 ip 及端口。
    turnServer: { url: '14.3.3.3:3478', username: 'turn', credential: 'turn', credentialType: 'password' },
    // 默认 SDK 会直连 TRTC 服务器，如果连不上，则会尝试连 TURN server。你可以指定 'relay' 来强制连接 TURN server。
    iceTransportPolicy: 'all', 
    // SDK 默认会向 yun.tim.qq.com 域名上报日志，但若该域名在您的内网无法访问，您需要给该域名开白名单或者配置以下日志代理。
    // 设置日志上报代理，日志是排查问题的关键数据，请务必设置该代理。
    loggerProxy: '',
  }
})
```

### 2-A 方案

#### 搭建 Nginx 服务器

1. 部署 Nginx 服务器

   参考互联网中搜索到的 Nginx 服务器部署教程进行搭建部署。如果企业已有部署 Nginx 服务，可以不用部署直接进行配置。

2. 配置 Nginx 服务器

   ```sh
   vi /etc/nginx/nginx.conf 
   ```

   ```
   http {
     server { 
       # nginx 服务器的访问域名
       server_name proxy.example.com; 
       # nginx 服务器的访问端口
       listen 443; 
   
       ssl on; 
   
       location /ws/ { # 对应 setProxyServer 中的 websocketProxy 参数
         proxy_pass https://signaling.rtc.qq.com/; # TRTC 的服务器
         proxy_http_version 1.1; 
         proxy_set_header Upgrade $http_upgrade; 
         proxy_set_header Connection "upgrade"; 
       }
       location /logger/ { # 对应 setProxyServer 中的 loggerProxy 参数
         proxy_pass https://yun.tim.qq.com/;
       }
   
       #域名对应的 SSL 证书，HTTPS 用，用户自行申请 
       ssl_certificate ./crt/1_proxy.trtcapi.com_bundle.crt; 
       ssl_certificate_key ./crt/2_proxy.trtcapi.com.key; 
     }
   }
   ```

3. 执行重新加载 Nginx

   ```sh
   sudo nginx -s reload
   ```

4. 确认公司防火墙允许访问 Nginx 服务器 IP 及端口

#### 搭建 Turn 服务器

您可以在互联网中搜索 turn server 搭建教程进行搭建；也可以使用如下在 `Centos 中搭建 turn server `的脚本进行搭建。

1. 在 Linux 服务器中，创建脚本文件 `turn.sh`，脚本内容如下：

   ```sh
   #!/usr/bin/env bash
   
   # current file name is turn.sh
   
   # ref:
   # https://gabrieltanner.org/blog/turn-server    STEP 3 testing turn server
   # https://medium.com/av-transcode/what-is-webrtc-and-how-to-setup-stun-turn-server-for-webrtc-communication-63314728b9d0
   
   # as super-user
   # usage:  current_program <external-ip>
   
   set -x
   set -e
   
   ip a
   pwd
   whoami
   
   display_usage() {
           echo "This script must be run with super-user privileges."
           echo -e "\nUsage: $0 <external-ip> \ne.g. $0 154.8.246.205"
   }
   
   # if less than two arguments supplied, display usage
   if [ $# -lt 1 ]
   then
           display_usage
           exit 1
   fi
   
   if [[ $1 =~ ^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
     echo "get external ip $1"
   else
     echo "wrong external ip $1 , must not have whitespace, tab and other char"
     exit 2
   fi
   
   yum install -y coturn
   
   # $1 is <external-ip>
   cat <<EOF > /etc/coturn/turnserver.conf
   external-ip=$1
   listening-port=3478
   lt-cred-mech
   max-port=65535
   min-port=20000
   no-dtls
   no-tls
   realm=tencent
   user=turn:turn
   verbose
   EOF
   ```

2. 增加可执行权限

   ```sh
   chmod +x turn.sh
   ```

3. 以 root 身份执行脚本，`sudo ./turn.sh <服务器外网IP>`， 例如：

   ```sh
   sudo ./turn.sh 14.3.3.3
   ```

4. 启动 turn server

   ```sh
   systemctl start coturn
   
   # 检测 turn 是否启动成功
   ps aux | grep coturn
   
   # 若要重启服务，则执行
   service coturn restart 
   ```

5. 配置 turn 服务器的防火墙，打开入站端口 3478（TCP & UDP），及上述配置中 min ~ max port 之间的出站端口（UDP）。

6. 配置公司内网防火墙，允许访问 turn 服务器的 IP，及打开出站端口 3478（TCP & UDP）。

7. 测试 turn server

   使用该[测试页面](https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/)，测试搭建好的 turn server 是否能够正常连通访问。如下截图中 done 说明 turn 能正常访问。

   <img src="./assets/turn-test.png" alt="turn-test" style="zoom: 33%;" />

### 2-B 方案

2-B 方案搭建 Nginx 代理的方式，与 2-A 方案相同。主要有两处区别：

1. 搭建 turn server 时，配置文件中的 external-ip 字段需填写服务器在您企业内网的地址。

```sh
# 2-A 方案中启动脚本填的是服务器外网地址，例如外网地址为 14.3.3.3
sudo ./turn.sh 14.3.3.3
# 2-B 方案中启动脚本填的是服务器在企业内网地址，例如内网地址为 10.0.0.4
sudo ./turn.sh 10.0.0.4
```

2. 防火墙配置：

- 对于 Nginx 服务器，需要在公司内网防火墙配置域名白名单，允许 Nginx 服务器访问 TRTC 的相关域名，参考：[TRTC Web SDK 域名和端口白名单](https://cloud.tencent.com/document/product/647/34399#webrtc-.E9.9C.80.E8.A6.81.E9.85.8D.E7.BD.AE.E5.93.AA.E4.BA.9B.E7.AB.AF.E5.8F.A3.E6.88.96.E5.9F.9F.E5.90.8D.E4.B8.BA.E7.99.BD.E5.90.8D.E5.8D.95.EF.BC.9F)
- 对于 Turn Server，需要允许 Turn Server 能够访问外网。

