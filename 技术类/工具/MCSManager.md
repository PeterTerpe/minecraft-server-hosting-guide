# MCSManger（MCSM）
### 简介
一个针对MC服务端设计的面板托管服务，也可以运行任何命令行程序，支持docker镜像、多用户、主机文件管理。

## 注意事项
- 如果MCSM崩溃，其所托管的程序大概率也会跟着一起终止。最有可能造成崩溃的是分配内存超过了最大内存 (OOM)。
- 重启docker时对运行在docker上的实例进行重启/停止操作可能导致MCSM不能正确找到容器ID，导致实例无法访问，重启daemon解决。

## 通过cloudflared tunnel访问
Cloudflared tunnel可以轻松配置Panel的访问，但对daemon支持不好，若须使用tunnel连接daemon可以参考这篇[视频教程](https://www.youtube.com/watch?v=ITrh7SDVC9U)。

如有公网IP，访问daemon最方便的方式是通过公网直连。关于家庭网络如何使用公网IP访问，请见[家宽IP](../网络/家宽IP.md)。
