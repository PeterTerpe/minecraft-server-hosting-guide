# Frp
使用Frp来连接MC服务器通常有以下原因：
1. 没有公网IP
2. 希望隐藏公网IP
3. 获得更好的连接质量（选择有多网优化的节点）
4. 免去端口转发和DDNS

## 两种使用Frp的方法：
- [使用第三方Frp](#第三方frp)
  - 有完全免费的选项
  - 通常有隧道数量和流量限制
  - 通常不支持绑定自定义域名
  - 受第三方平台其他用户使用状况的影响
- [自建Frp](#自建frp)
  - 需要租赁云服务器
  - 通常不限流量，不限制隧道数
  - 通常比第三方Frp更稳定

### 第三方Frp
- [SakuraFrp](https://www.natfrp.com/)
  - 使用国内节点需要花费1元刷脸实名认证，需要年满18岁
  - 有免费选项，但是延迟不佳
  - 付费节点延迟可控，价格比较便宜
  - 节点可能会发生波动，需要更换节点，导致连接中断
### 自建Frp
通常使用[fatedier/frp](https://github.com/fatedier/frp)进行搭建，以下推荐几个云服务器：
- 阿里云轻量服务器
  - 新人第一年有优惠价格，可能比某些付费Frp还便宜
  - 部分区域的实例带多网优化
  - 200Mbps峰值带宽（用多了会限速）

## 获取真实IP
*这个章节的内容仅在Linux环境下针对[fatedier/frp](https://github.com/fatedier/frp)测试过*

不进行额外配置的情况下，经过frp转发的服务在日志中会显示访问IP为本机，如果想要获得真实的访问IP需要进行以下额外配置：

### 在frpc.toml中：
在每个想要获取真实ip的proxy配置中添加`transport.proxyProtocolVersion = "v2"`

参考[frp官方说明](https://github.com/fatedier/frp?tab=readme-ov-file#proxy-protocol)

⚠️ **注意**：如果使用Velocity或GeyserMC，请查看下文对应的说明！

### Velocity
如果你有Velocity代理通过启用了`transport.proxy`的frp连接，就需要额外配置`velocity.toml`，否则无法通过frp连接。

修复：在`velocity.toml`中的`[advanced]`部分找到`haproxy-protocol`并设置为`true`。重启velocity

⚠️ **注意**：如果启用了`haproxy-protocol`，velocity就不能接受正常的直接连接，如果想要同时坚固frp获取真实IP和正常直连，需要使用HAProxy或Nginx给正常连接也进行proxy转发。

如果已经配置好Nginx且没有流量均衡的需求(仅有一个Velocity)，直接用Nginx转发最为简单，否则请使用[HAProxy](https://www.haproxy.org/)

<details><summary>Nginx最简配置：（请根据实际情况修改）
</summary>

先确保Nginx主文件包含了stream.d文件夹中的配置：
```
# /etc/nginx/nginx.conf

stream {
    include /etc/nginx/stream.d/*.conf;
}
```

```
# /etc/nginx/stream.d/velocity_stream.conf

upstream velocity_backend {
    server 127.0.0.1:<Velocity的监听端口>;
}

server {
    listen 0.0.0.0:<玩家直连的端口>;
    proxy_pass velocity_backend;
    proxy_protocol on;
}
```

>如果`nginx -s reload`返回`unknown directive "stream"`，请确保已安装Nginx stream模块。
>
>在基于Debian的系统上安装：`apt install libnginx-mod-stream`
</details>

### GeyserMC
如果你使用了Standalone版本的GeyserMC连接Velocity，需要在`config.yml`中的`advanced -> java`部分设置`use-haproxy-protocol = true`来兼容Velocity的`haproxy-protocol = true`

*未测试Velocity插件版本，可能需要类似操作或无需操作*

*尝试使在frp后面的GeyserMC获取真实IP未成功*
