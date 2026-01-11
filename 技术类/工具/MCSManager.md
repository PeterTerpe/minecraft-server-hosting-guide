# MCSManger（MCSM）
### 简介
一个针对MC服务端设计的面板托管服务，也可以运行任何命令行程序，支持docker镜像、多用户、主机文件管理。

## 注意事项
- 如果MCSM崩溃，其所托管的程序大概率也会跟着一起终止。最有可能造成崩溃的是分配内存超过了最大内存 (OOM)。
- 重启docker时对运行在docker上的实例进行重启/停止操作可能导致MCSM不能正确找到容器ID，导致实例无法访问，重启daemon解决。

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