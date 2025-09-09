# [Prometheus](https://prometheus.io/)
一个全平台系统监测软件，可以用于监测并记录系统的各项数据，以及MC服务器的状况。

# 目录
- [对接Grafana面板](#对接grafana面板)
- [监测Fabric Minecraft](#监测fabric-minecraft)
- [监测系统资源占用](#监测系统资源占用)
# 对接[Grafana面板](./Grafana.md)
Prometheus仅进行数据的监控和记录，并提供查询的api接口，并不直接提供可视化的监控页面，若希望使用图形化的监控页面，可以安装Grafana来展示。
# 监测Fabric Minecraft
[Fabric Exporter](https://modrinth.com/mod/fabricexporter)是一个为Fabric MC打造的MOD，在服务端安装此mod可以向Prometheus发送服务端的各项数据，包括TPS、MSPT、区块加载状况、实体加载状况、玩家数等数据。
# 监测系统资源占用
为Prometheus安装一个[node_exporter](https://github.com/prometheus/node_exporter)插件即可使Prometheus记录系统数据，包括CPU占用、RAM占用、各类温度等数据。
