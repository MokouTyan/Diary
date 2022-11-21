校园论坛的搭建（关联闭社、抒发森林、comin、mastodon）

### 前言

在看完前两篇博客之后，我们就可以开始实操阶段了，我采用尽量简单的方式进行搭建

博客虽然长，但操作其实并不多，希望能耐心看完

[搭建校园论坛之前所需了解的（关联闭社、抒发森林、comin、mastodon）](https://www.cnblogs.com/mokou/p/16727162.html)

[校园论坛服务器的选购（关联闭社、抒发森林、comin、mastodon）](https://www.cnblogs.com/mokou/p/16728058.html)

### 搭建所需

一个公网ip的服务器，能使用80端口，且能通过测试：[Ping.cn:网站测速-ping检测-dns查询-ipv6网站测试-路由跟踪查询-站长工具](https://www.ping.cn/)

网络、linux，docker相关基础知识

### 宝塔安装

宝塔的作用是让我们可以方便查看机器的性能监考

首先进入宝塔官网，下载新版本的宝塔：https://www.bt.cn/new/download.html

我用的是Debian版本的，根据不同需求进行更改

```shell
wget -O install.sh http://download.bt.cn/install/install-ubuntu_6.0.sh && bash install.sh ed8484bec
```

因为新版要手机认证以及注册账号，我不是很想暴露自己，所以现在要降级

降级版本有7.4.5，7.4.7，7.4.8，7.5.2，7.5.1，7.6.0可选，降级失败了在命令行输入bt，选择修复

```shell
wget http://xiutu.us/bt/LinuxPanel-7.6.0.zip
unzip LinuxPanel-7.6.0.zip
cd panel
bash update.sh
cd .. && rm -f LinuxPanel-7.6.0.zip && rm -rf panel
echo "{\"uid\":1000,\"username\":\"admin\",\"serverid\":1}" > /www/server/panel/data/userInfo.json
```
shell输入bt，进入14查看端口和账号密码

![](https://img2022.cnblogs.com/blog/1446116/202209/1446116-20220907133329813-17210531.png)

看看能不能进，能进的话取消手机号绑定

进入后开始安装基础网络环境

![](https://img2022.cnblogs.com/blog/1446116/202209/1446116-20220907133749597-436074155.png)

到这里宝塔已经安装差不多了

### docker安装

```shell
apt-get update
curl -sSL https://get.daocloud.io/docker | sh
```

耐心等待，然后安装docker-compose

```shell
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.4.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
docker-compose --version
```

最后用docker-compose --version验证一下是否安装成功

### 下载mastodon

根据官方文档：https://gist.github.com/TrillCyborg/84939cd4013ace9960031b803a0590c4

```shell
# Clone mastodon to ~/live directory
git clone https://github.com/tootsuite/mastodon.git live
# Change directory to ~/live
cd ~/live
# Checkout to the latest stable branch
git checkout $(git tag -l | grep -v 'rc[0-9]*$' | sort -V | tail -n 1)
```

这里可以看到一个样本文件

```shell
cp .env.production.sample .env.production
```

在该目录下赋予public目录权限

```shell
chown -R 991:991 public
```

构建这个docker，需要等待一段时间，我等了一小时= =

```shell
docker-compose build
```

最后使用docker images查看一下生成的镜像

![](https://img2022.cnblogs.com/blog/1446116/202209/1446116-20220908132155991-1231054495.png)

### 配置mastodon

使用命令：

```shell
docker-compose run --rm web bundle exec rake mastodon:setup
```

然后会出现以下内容，中文部分要注意

```shell
root@VM-16-15-debian:~/live# docker-compose run --rm web bundle exec rake mastodon:setup
[+] Running 15/15
 ⠿ redis Pulled                                                                                            16.9s
   ⠿ 213ec9aee27d Pull complete                                                                             9.0s
   ⠿ c99be1b28c7f Pull complete                                                                             9.1s
   ⠿ 8ff0bb7e55e3 Pull complete                                                                             9.5s
   ⠿ 477c33011f3e Pull complete                                                                             9.8s
   ⠿ 2bbc51a93257 Pull complete                                                                             9.9s
   ⠿ 2d27eae19281 Pull complete                                                                            10.0s
 ⠿ db Pulled                                                                                               34.1s
   ⠿ 85c3ef7cf9a6 Pull complete                                                                             6.6s
   ⠿ ac29cc04759a Pull complete                                                                             8.4s
   ⠿ 2a37e244d86b Pull complete                                                                            24.4s
   ⠿ 36d7202aa1cf Pull complete                                                                            24.5s
   ⠿ 3acdddb9790a Pull complete                                                                            24.6s
   ⠿ 9a938759f2bf Pull complete                                                                            24.7s
   ⠿ 5d65a6241248 Pull complete                                                                            24.8s
[+] Running 4/4
 ⠿ Network live_internal_network  Created                                                                   0.1s
 ⠿ Network live_external_network  Created                                                                   0.1s
 ⠿ Container live-redis-1         Created                                                                   0.4s
 ⠿ Container live-db-1            Created                                                                   0.4s
[+] Running 2/2
 ⠿ Container live-db-1     Started                                                                          1.1s
 ⠿ Container live-redis-1  Started                                                                          1.1s
Your instance is identified by its domain name. Changing it afterward will break things.
Domain name: 这里填上你的域名

Single user mode disables registrations and redirects the landing page to your public profile.
Do you want to enable single user mode? No

Are you using Docker to run Mastodon? Yes

PostgreSQL host: db
PostgreSQL port: 5432
Name of PostgreSQL database: postgres
Name of PostgreSQL user: postgres
Password of PostgreSQL user: （这里按回车就行）
Database configuration works! 🎆

Redis host: redis
Redis port: 6379
Redis password: （这里按回车就行）
Redis configuration works! 🎆

Do you want to store uploaded files on the cloud? No

Do you want to send e-mails from localhost? No
SMTP server: smtp.126.com（看你邮箱怎么配）
SMTP port: 25（我这里用的是非SSL的）
SMTP username: （你的邮箱）
SMTP password: （授权码，不是密码）
SMTP authentication: plain
SMTP OpenSSL verify mode: none
E-mail address to send e-mails "from": （你的邮箱）
Send a test e-mail with this configuration right now? Yes
Send test e-mail to: （你的测试邮箱）

This configuration will be written to .env.production
Save configuration? Yes
Below is your configuration, save it to an .env.production file outside Docker:
（从这里开始拷贝）
# Generated with mastodon:setup on 2022-09-08 05:30:02 UTC

LOCAL_DOMAIN=mokou.
SINGLE_USER_MODE=false
SECRET_KEY_BASE=
OTP_SECRET=
VAPID_PRIVATE_KEY=
VAPID_PUBLIC_KEY=
DB_HOST=db
DB_PORT=5432
DB_NAME=postgres
DB_USER=postgres
DB_PASS=
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=
SMTP_SERVER=smtp.126.com
SMTP_PORT=25
SMTP_LOGIN=
SMTP_PASSWORD=
SMTP_AUTH_METHOD=plain
SMTP_OPENSSL_VERIFY_MODE=none
SMTP_FROM_ADDRESS=
（这里复制结束）
It is also saved within this container so you can proceed with this wizard.

Now that configuration is saved, the database schema must be loaded.
If the database already exists, this will erase its contents.
Prepare the database now? Yes
Running `RAILS_ENV=production rails db:setup` ...


Database 'postgres' already exists
Done!

All done! You can now power on the Mastodon server 🐘

Do you want to create an admin user straight away? Yes
Username: admin
E-mail: （你的邮箱）
Error connecting to Redis on 127.0.0.1:6379 (Errno::ECONNREFUSED)
Error connecting to Redis on 127.0.0.1:6379 (Errno::ECONNREFUSED)
Switching object-storage-safely from green to red because Redis::CannotConnectError Error connecting to Redis on 127.0.0.1:6379 (Errno::ECONNREFUSED)
Error connecting to Redis on 127.0.0.1:6379 (Errno::ECONNREFUSED)
You can login with the password: （这里有个密码必须记住！！！！！！第一次用admin登录用这个密码）
You can change your password once you login.
root@VM-16-15-debian:~/live# 
```

现在完全配置好了，记得吧上面拷贝的内容放到.env.production文件中

```shell
nano .env.production
```

启动docker

```shell
docker-compose up -d
```

### 申请SSL证书

进入宝塔，先创建一个站点，你可以先用自己的域名进行测试，然后再向thu站长申请子域名（以closed.social结尾）

![](https://img2022.cnblogs.com/blog/1446116/202209/1446116-20220908134116036-1088518799.png)

直接在宝塔后台就可以一键申请免费SSL证书，还可以自动续签

![image-20220925163355901](https://i0.hdslb.com/bfs/album/a9d9ce82cd7fb62082a3eff6d8040b69c562f24e.png)

将key和pem的内容拷贝下来，放到

### 配置反向代理

![image-20220925164041654](https://i0.hdslb.com/bfs/album/87bf5fd178abc377e83432506c5c081784364bde.png)

然后开始配置反向代理

```
map $http_upgrade $connection_upgrade {
  default upgrade;
  ''      close;
}

upstream backend {
    server 127.0.0.1:3000 fail_timeout=0;
}

upstream streaming {
    server 127.0.0.1:4000 fail_timeout=0;
}

proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=CACHE:10m inactive=7d max_size=1g;

server {
  listen 80;
  listen [::]:80;
  server_name fzu.closed.social（根据你的域名）;
  root /root/live/public（根据你自己的目录位置）;
  location /.well-known/acme-challenge/ { allow all; }
  location / { return 301 https://$host$request_uri; }
}

server {
  listen 443 ssl http2;
  listen [::]:443 ssl http2;
  server_name fzu.closed.social（根据你的域名）;

  ssl_protocols TLSv1.2 TLSv1.3;
  ssl_ciphers HIGH:!MEDIUM:!LOW:!aNULL:!NULL:!SHA;
  ssl_prefer_server_ciphers on;
  ssl_session_cache shared:SSL:10m;
  ssl_session_tickets off;

  # Uncomment these lines once you acquire a certificate:
  ssl_certificate     /home/www/pem.pem（刚才保存的SSL文件）;
  ssl_certificate_key /home/www/key.key（刚才保存的SSL文件）;

  keepalive_timeout    70;
  sendfile             on;
  client_max_body_size 80m;

  root /root/live/public（根据你自己的目录位置）;

  gzip on;
  gzip_disable "msie6";
  gzip_vary on;
  gzip_proxied any;
  gzip_comp_level 6;
  gzip_buffers 16 8k;
  gzip_http_version 1.1;
  gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript image/svg+xml image/x-icon;

  add_header Strict-Transport-Security "max-age=31536000" always;

  location / {
    try_files $uri @proxy;
  }

  location ~ ^/(emoji|packs|system/accounts/avatars|system/media_attachments/files) {
    add_header Cache-Control "public, max-age=31536000, immutable";
    add_header Strict-Transport-Security "max-age=31536000" always;
    try_files $uri @proxy;
  }

  location /sw.js {
    add_header Cache-Control "public, max-age=0";
    add_header Strict-Transport-Security "max-age=31536000" always;
    try_files $uri @proxy;
  }

  location @proxy {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Proxy "";
    proxy_pass_header Server;

    proxy_pass http://backend;
    proxy_buffering on;
    proxy_redirect off;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    proxy_cache CACHE;
    proxy_cache_valid 200 7d;
    proxy_cache_valid 410 24h;
    proxy_cache_use_stale error timeout updating http_500 http_502 http_503 http_504;
    add_header X-Cached $upstream_cache_status;
    add_header Strict-Transport-Security "max-age=31536000" always;

    tcp_nodelay on;
  }

  location /api/v1/streaming {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header Proxy "";

    proxy_pass http://streaming;
    proxy_buffering off;
    proxy_redirect off;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection $connection_upgrade;

    tcp_nodelay on;
  }

  error_page 500 501 502 503 504 /500.html;
}

```

现在就可以通过网页访问了

### 限制本校邮箱注册

先在目录下关闭docker

```
docker-compose down
```

在目录下

```
nano .env.production
```

在文件内添加一行，根据你的学校进行修改

```
EMAIL_DOMAIN_ALLOWLIST=fzu.edu.cn
```

在目录下运行

```
docker-compose up -d
```

测试一下是否只能使用校园邮箱注册

### 切换白名单模式

搭建好了后，mastodon默认使用的是黑名单模式，可以屏蔽几个域名的用户

白名单模式的意思是，只有这几个域名的服务器才可以互相交流访问，这就是高校网络的基础

先在后台写好所有的内容，确认没问题后再开启白名单模式

因为切换白名单模式后，网页后台就无法直接修改/about页面，所以还是先改好了，再切白名单模式

![image-20220925164817067](https://i0.hdslb.com/bfs/album/1370a2f2103d9ff587a06ffc5f0e4e7b2b3e7082.png)

切换白名单模式之前，先把docker内的文件拷贝出来，cp后面跟着的是docker的容器id

```
docker ps
#查看名字为web的容器ID是什么
docker cp 03526798b324:/opt/mastodon/app/controllers/ /root/mastodon/
```

上面这句命令的意思是将/opt/mastodon/app/controllers/拷贝到宿主机的/root/mastodon/

去目录下确认一下文件在不在

然后修改其中的两个文件

```
nano about_controller.rb
nano home_controller.rb
```

注释该行

![image-20220925170319615](https://i0.hdslb.com/bfs/album/8dc020f94267a9319ed08cbe3b4a8a2e360cdc09.png)

![image-20220925170411343](https://i0.hdslb.com/bfs/album/c8608a51969780bf3cc1a672f87012b41049d8b1.png)

回到live目录，先关闭docker，编辑docker-compose.yml文件

```
docker-compose down
nano docker-compose.yml
```

在web和sidekiq的volume中添加刚才映射出来的目录

![image-20220925170827139](https://i0.hdslb.com/bfs/album/cebb5f67b06bf0d4e430a06650829d576f88a97c.png)

![image-20220925170719809](https://i0.hdslb.com/bfs/album/f94777d5bcf70a1479320e7b029e59442a10122c.png)

保存退出后，再修改一次环境

```
nano .env.production
```

加入一行

```
LIMITED_FEDERATION_MODE=true
```

现在的环境文件如下

![image-20220925171322492](https://i0.hdslb.com/bfs/album/b87348fbc51aa187d56a8895809a25937f8dd78e.png)

保存退出后，就可以直接运行了

```
docker-compose up -d
```

好了，现在就可以在后台添加其他学校的域名了

![image-20220925171419757](https://i0.hdslb.com/bfs/album/fc565fcca11110cfd7bd449e7d94c19a8ad14f8e.png)