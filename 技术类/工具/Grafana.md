# [Grafana](https://grafana.com/)
这是一个强大的数据监控面板。管理员可以自定义各种面板并选择公开。
# 常见问题
以下是使用Grafana时可能遇到的问题及解决办法
## 如果你希望复用端口/在子路径访问服务：
- nginx配置：
```
location /stats/ {  # 根据偏好修改子路径
        proxy_set_header Host $http_host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_pass http://localhost:9091/;  # 将端口修改为Grafana绑定的端口
        rewrite ^/stats/(.*) /$1 break;  # 匹配第一行的子路径
    }
```
## 在面板中添加跳转链接
通过以下的方法，你可以为面板中的不同仪表添加不同的链接。你可以选择使用此方法将公开的仪表盘链接到一起，或跳转外部链接。**请注意使用这种方法进行仪表盘内的跳转会出现显示错误，需要手动刷新页面以恢复。**
### 使用Panel Link
    Panel options >> Panel links
在此处添加的链接会在点击仪表标题时跳转
### 使用Data link
    Data links
在此处添加的链接会在点击仪表时进行跳转
