# [Grafana](https://grafana.com/)
这是一个强大的数据监控面板。管理员可以自定义各种面板并选择公开。

### 如果你希望复用端口/在子路径访问服务：
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
