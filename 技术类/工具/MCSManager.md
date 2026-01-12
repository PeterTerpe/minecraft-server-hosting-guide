# MCSManger（MCSM）

*MCSManger已有成熟详尽的教程文档，具体请查阅 [MCSManger官方教程文档](https://docs.mcsmanager.com/zh_cn/) 。此篇仅阐述使用注意事项和基于教程的相关建议。*

## 简介

一个针对MC服务端设计的面板托管服务，也可以运行任何命令行程序，支持docker镜像、多用户、主机文件管理。

### 为什么使用MCSManger（面板服务）

刚接触开服的服主可能会使用ssh并配合tmux等进程管理器去管理Minecraft服务器。此类操作相比面板管理有如下不足:

- **操作繁琐**：需要反复输入命令，无法快速操作。
  
- **文件管理不便**：修改配置或更换模组需要额外的 SFTP 工具。
  
- **稳定性依赖人工**：进程崩溃后需手动重新启动。
  
- **协作风险高**：多人维护需共享 SSH 权限，存在安全隐患。
  

而使用MCSManger可解决传统管理方式的不足。它提供了更为方便的图形化界面管理。并使用网页服务，使用浏览器即可快捷管理部署在多台设备上的服务器。且拥有可靠的进程管理（开机自启，崩溃重启等）。对于任何希望长期、稳定、高效地运营 Minecraft 服务器的服主来说，MCSManager 几乎是不可或缺的现代化管理工具。

## 安装方式

### - Linux安装

根据 [MCSManger官方教程文档](https://docs.mcsmanager.com/zh_cn/) ，MCSManger安装方式分别有

- Linux自动安装
  
- Linux手动安装
  
- Docker安装面板
  

对于新手来说这里推荐Linux自动安装，方便快捷，输入教程内的一键脚本即可完成安装。且后续更新面板时，只需再次输入一键安装脚本即可完成安装。若对容器化技术了解的服主可使用Docker安装面板，仅需再次拉取镜像即可完成更新。

Linux手动安装较为麻烦，且后续更新仍需手动多次步骤操作。这里不做推荐。

## 注意事项
- 如果MCSM崩溃，其所托管的程序大概率也会跟着一起终止。最有可能造成崩溃的是分配内存超过了最大内存 (OOM)。
- 重启docker时对运行在docker上的实例进行重启/停止操作可能导致MCSM不能正确找到容器ID，导致实例无法访问，重启daemon解决。
- 如果MCSM崩溃，其所托管的程序大概率也会跟着一起终止。最有可能造成崩溃的是分配内存超过了最大内存 (OOM)
  - 降低服务端的内存，通常情况下一个Minecraft生存服8GB即可满足大部分需求，最大不超过12GB
  - 若该问题频繁出现，可排查服务端是否发生内存泄漏等问题
- MCSManger作为运行在公网环境中的面板服务。需做好安全防护措施。防止被暴力破解密码导致面板后台入侵
  - 面板设置复杂的用户名称和密码，避免简单重复密码
  - 可配置HTTPS访问，防止中间人监听
  - 可套上WAF和CDN，或设置IP段仅自己可访问
  - 管理员权限仅给予信任的人
  - 若商用（他人使用），请使用Docker部署服务端，以免用户恶意操作危害宿主机

## 通过公网IP建立连接
如有公网IP，访问daemon最方便的方式是通过公网直连。关于家庭网络如何使用公网IP访问，请见[家宽IP](../网络/家宽IP.md)。

## 通过Cloudflared建立连接
Cloudflared是Cloudflare提供的免费Tunnel，可以将内网中的任意端口暴露于公网中的一个域名，并享受Cloudflare的全球网络加速。可以额外配置WAF及路径分流等。

>关于如何配置Cloudflared请见[（待补充）]()，以下教程仅讲解针对MCSM的特殊配置，请确认你已经配置好了隧道并了解如何新建Application Route。

MCSM连接分为Web和Daemon，必须完成两项的配置才能建立连接。

### Web
Web的配置较为简单：
1. 在配置文件`<安装目录>/web/data/SystemConfig/config.json`中找到`httpPort`配置项。
2. 在Tunnel中新建一个Application Route，将`httpPort`端口的绑定到一个域名上。
3. 使用该域名即可访问MCSM面板。

### Daemon
请参考[官方文档](https://docs.mcsmanager.com/ops/cloudflare_tunnels.html)

由于Cloudflare入口服务器并不能接受所有端口的请求，Daemon的端口应该被更改来适应这个条件。

>请注意使用这个方法建立连接不如公网直连来的稳定，但好处是不用配置SSL证书，以及可能对文件上传下载速度有提升（从海外连接更加明显）

1. 在配置文件`<安装目录>/daemon/data/Config/global.json`中找到`port`选项，将其更改为下列端口中的一个：
   - <details>
     <summary>可选端口</summary>
       443<br>2053<br>2083<br>2087<br>2096<br>8443
     </details>
2. 在Tunnel中新建一个Application Route，将`port`端口的绑定到一个域名上。
3. 在面板的节点设置中，添加或编辑对应节点：
   - 将`远程节点 IP 地址`设置为：`wss://<你绑定的域名>`
   - 将`远程节点端口`设置为`port`
   - 如果在Daemon配置文件中配置了`prefix`，应该将`路径前缀`设置为`prefix`
4. 保存之后顺利的话就可以正常连接了。

**注意事项**
- Cloudflared一开始可能会因为Daemon连接导致Tunnel瘫痪，但日志中未见异常，重启Tunnel后一般可以解决。（PeterTerpe注）


