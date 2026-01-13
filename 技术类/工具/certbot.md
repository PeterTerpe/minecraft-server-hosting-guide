# Certbot
一款自动安装ssl证书的程序。
## 在没有80/443端口的情况下使用
在家宽IP屏蔽常用端口的情况下，最好的办法是使用DNS acme验证并获取证书，以下是DNS提供商为cloudflare的示例：（记得替换sub.example.com）
1. 先安装certbot与cloudflare DNS插件：
```sudo apt install -y certbot python3-certbot-dns-cloudflare```
2. 登录cloudflare账户后创建一个api token并赋予Zone.DNS读与写的权限。
3. （以Linux为例）在/etc/letsencrypt/目录中创建cloudflare.ini文件并妥善设置访问权限，内容为：
```
dns_cloudflare_api_token = 你的TOKEN
```
4. 执行certbot获取证书，顺利的话20秒以内就会成功。
```
sudo certbot certonly \
--dns-cloudflare \
--dns-cloudflare-credentials /etc/letsencrypt cloudflare.ini \
-d <sub.example.com>
```
生成的证书默认会保存在/etc/letsencrypt/live/sub.example.com/目录下，使用反代软件时指定对应文件即可。
- 如果使用了v2rayA等代理软件，建议配置cloudflare.com与letsencrypt.org为直连，否则可能不成功。
